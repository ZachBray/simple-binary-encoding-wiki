After running the [SbeTool](Sbe-Tool-Guide) a number of C++ source files will be created. These files represent the types and messages declared in the schema. For a quick start to SBE look a [this](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-samples/src/main/resources/example-schema.xml) schema and its usage [here](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-samples/src/main/cpp/GeneratedStubExample.cpp).

Messages are designed to be read in the sequential order as define in the schema. This ensures a [stream access](Design-Principles) pattern for performance. If groups or variable data are not processed in order then the data may become corrupt. Conceptually the message is encoded as a series of blocks. The blocks are the root fields, followed by each iteration of repeating groups, and finally followed by one or more variable data fields.

Due to the streaming nature of the codec the size of the message cannot be determined until encoding or decoding is complete. The method <code>MessageFlyweight::position()</code> will return the index in the underlying buffer at which the next block will commence, and the <code>MessageFlyweight::size()</code> method will return the current encoded size depending on how far it has progressed.

### Framing

It is expected that the messages are communicated inside a framing protocol. The frame defines the size of the buffer containing the message header and message itself.

    +------------------------------------------------------------+
    |        +----------+----------------------------------+     |
    |Frame   |Msg Header|Message Body                      |     |
    |        +----------+----------------------------------+     |
    +------------------------------------------------------------+

The frame may contain session or transport level fields that belong to different layers of the [OSI Model](http://en.wikipedia.org/wiki/OSI_model) and beyond the scope of the message codec which deals with the [layer 6](http://en.wikipedia.org/wiki/OSI_model#Layer_6:_presentation_layer) presentation.

### Message Header

The message header contains the fields that allows the decoder to identify what codec should be used as the template for a message.

1. **blockLength**: The length of the message root block before repeating groups or variable data commences.
1. **templateId**: The identifier for the template type of the message that is to follow.
1. **schemaId**: The identifier for the schema the message belongs to.
1. **version**: The version of the schema allowing for extension.

**Note**: A new message header type can be defined with different sizes of integers for the template and version according to needs.

To encode a message it is necessary to encode the header then the message.

```cpp
    // Encode the header
    hdr.wrap(buffer, offset, messageHeaderVersion, bufferLength)
       .blockLength(Car::sbeBlockLength())
       .templateId(Car::sbeTemplateId())
       .schemaId(Car::sbeSchemaId())
       .version(Car::sbeSchemaVersion());

    // Then encode the message
    messageFlyweight.resetForEncode(buffer, bufferOffset + MessageHeader.size(), bufferLength);
```

The decoder should decode the header and then lookup which template should be used to decode the message body.

```cpp
    // Reset the message header in preparation for decoding a message.
    hdr.wrap(buffer, bufferOffset, bufferLength);

    int templateId = hdr.templateId();
    int actingVersion = hdr.version();
    int actingBlockLength = hdr.blockLength();

    // Lookup template for decoding the message

    bufferOffset += hdr.size();
    messageFlyweight.resetForDecode(buffer, bufferOffset, actingBlockLength, actingVersion, bufferLength);
```

### Single Fixed Fields

Single fixed fields can be encoded in a fluent style after a message flyweight has been reset for encoding.

```cpp
    car.wrapForEncode(buffer, bufferOffset, bufferLength)
       .serialNumber(1234)
       .modelYear(2013);
```

Decoding single fixed fields is simply the reverse.

```cpp
    car.wrapForDecode(buffer, bufferOffset, actingBlockLength, actingVersion, bufferLength);

    sb.append("\ncar.serialNumber=").append(car.serialNumber());
    sb.append("\ncar.modelYear=").append(car.modelYear());
```

### Fixed Array Fields

It is possible to encode a fixed length array of a primitive type in a field.

To encode the array an element at a time

```cpp
    for (int i = 0, size = car.someNumbersLength(); i < size; i++)
    {
        car.someNumbers(i, i);
    }
```

Decoding is simply the reverse.

```cpp
    sb.append("\ncar.someNumbers=");
    for (int i = 0, size = car.someNumbersLength(); i < size; i++)
    {
        sb.append(car.someNumbers(i)).append(", ");
    }
```

### Fixed String Arrays

When encoding things like financial symbols it often beneficial to encode these as fixed length character arrays. SBE characters are single bytes. The default encoding is US-ASCII but it is possible to signify other encodings by using the characterEncoding attribute of the schema. In addition to the fixed array access method the following are provided.

For encoding a put method is defined taking a source pointer at which to begin copying. The copy will always be for the size of the field.

```cpp
       car.putVehicleCode(VEHICLE_CODE);
```

For decoding a get method is define taking destination byte array with an destination offset at which copy into to.

```cpp
       char tmp[80];
       car.getVehicleCode(tmp, sizeof(tmp));
```

### Constants

Constants do not get read from the underlying buffer. Their value as defined in the schema are returned directly by the codec using the same API as non-constant fields.

### Enumerations

Choice from the message schema directly map to enums in C++. Encoding is as follows.

```cpp
    car.wrapForEncode(buffer, bufferOffset)
       .available(BooleanType::T)
       .code(Model::A);
```

Decoding is simply the reverse.

```cpp
    sb.append("\ncar.available=").append(car.available());
    sb.append("\ncar.code=").append(car.code());
```

### BitSets

A bitset is multi-value choice that is mapped to the presence or not of particular bits in an integer. The bitset is returned from the flyweight with properties that take true or false arguments for each named bit. A `clear()` method is provided to reset the flags and should be used to initialize for encoding.

Encoding

```cpp
    car.extras().clear()
                .cruiseControl(true)
                .sportsPack(true)
                .sunRoof(false);
```

Decoding

```cpp
    OptionalExtras &extras = car.extras();
    sb.append("\ncar.extras.cruiseControl=").append(extras.cruiseControl());
    sb.append("\ncar.extras.sportsPack=").append(extras.sportsPack());
    sb.append("\ncar.extras.sunRoof=").append(extras.sunRoof());
```

### Composite Types

Composite types provide a means of reuse. The map directly to a class as a flyweight pattern in C++.

**Note**: For efficiency it is best to hold onto the reference to the type until all fields in the type have been accessed.

Encoding

```cpp
    car.engine().capacity(2000)
                .numCylinders((short)4)
                .putManufacturerCode(MANUFACTURER_CODE);
```

Decoding

```cpp
    Engine &engine = car.engine();
    sb.append("\ncar.engine.capacity=").append(engine.capacity());
    sb.append("\ncar.engine.numCylinders=").append(engine.numCylinders());
    sb.append("\ncar.engine.maxRpm=").append(engine.maxRpm());
```

## Repeating Groups

Repeating groups allow for collections of repeating type which can even be nested. The groups are types represented as a flyweight.

To encode it is necessary to first stage the count of time the group will repeat and then use the next() method to cursor forward while encoding.

```cpp
    Car::PerformanceFigures &performanceFigures = car.performanceFiguresCount(2);
    performanceFigures.next()
        .octaneRating((short)95)
        .accelerationCount(3)
            .next().mph(30).seconds(4.0f)
            .next().mph(60).seconds(7.5f)
            .next().mph(100).seconds(12.2f);
    performanceFigures.next()
        .octaneRating((short)99)
        .accelerationCount(3)
            .next().mph(30).seconds(3.8f)
            .next().mph(60).seconds(7.1f)
            .next().mph(100).seconds(11.8f);
```

To decode the flyweight implements an iterator type interface allowing for use with looping constructs.

```cpp
    Car::PerformanceFigures &performanceFigures = car.performanceFigures();
    while (performanceFigures.hasNext())
    {
        performanceFigures.next();
        sb.append("\ncar.performanceFigures.octaneRating=").append(performanceFigures.octaneRating());

        Car::PerformanceFigures::Acceleration &acceleration = performanceFigures.acceleration();
        while (acceleration.hasNext())
        {
            acceleration.next();
            sb.append("\ncar.performanceFigures.acceleration.mph=").append(acceleration.mph());
            sb.append("\ncar.performanceFigures.acceleration.seconds=").append(acceleration.seconds());
        }
    }
```

**Note**: Groups must be encoded and decoded in total before progressing to the next group or on to variable data.

## Variable Length Data

To store variable length strings or binary data then the var data fields can be used at the end of the message. These are variable length byte arrays for which optional character encoding can be provided in the schema.

Encoding

```cpp
    car.putMake(MAKE, strlen(MAKE));
    car.putModel(MODEL, strlen(MODEL));
```

Decoding

```cpp
    char tmp[80];
    bytesCopied = car.getMake(tmp, sizeof(tmp));
    sb.append("\ncar.make=").append(tmp, bytesCopied);
    sb.append("\ncar.makeCharacterEncoding=").append(car.makeCharacterEncoding());
    bytesCopied = car.getModel(tmp, sizeof(tmp));
    sb.append("\ncar.model=").append(tmp, bytesCopied);
    sb.append("\ncar.modelCharacterEncoding=").append(car.modelCharacterEncoding());
```

**Note**: Variable data fields must be encoded and decoded in order as defined in the schema.

### Versioning

There are some methods generated to help with versioning changes. In general, a field will return a "null" value (according to the SBE schema) for any field not present in that version. For example, if version 3 of a schema is the current codec and the acting version is set to 1, any fields added since version 2 or 3 will return values equivalent to "null" for the type or set by the schema. In addition, each field has two additional methods to help applications. They are `int <field name>SinceVersion(void)` and `bool <field name>InActingVersion(void)`.
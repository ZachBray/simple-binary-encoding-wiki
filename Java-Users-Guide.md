After running the [SbeTool](Sbe-Tool-Guide) a number of Java source files will be created. These files represent the types and messages declared in the schema. For a quick start to SBE look at [this](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-samples/src/main/resources/example-schema.xml) schema and its usage [here](https://github.com/real-logic/simple-binary-encoding/tree/master/sbe-samples/src/main/java/uk/co/real_logic/sbe/examples).

Messages are designed to be read in the sequential order as defined in the schema. This ensures a [stream access](Design-Principles) pattern for performance. If groups, or variable data, are not processed in order then the data may become corrupt. Conceptually a message is encoded as a series of blocks. The blocks are the root fields, followed by each iteration of repeating groups, and finally followed by one or more variable data fields.

Due to the streaming nature of the codec the encoded length of the message cannot be determined until encoding or decoding is complete. The method <code>MessageFlyweight.limit()</code> will return the index in the underlying buffer at which the next block will commence, and the <code>MessageFlyweight.encodedLength()</code> method will return the current encoded length in bytes depending on how far it has progressed.

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

``` java
    MESSAGE_HEADER_ENCODER
        .wrap(directBuffer, bufferOffset)
        .blockLength(CAR_ENCODER.sbeBlockLength())
        .templateId(CAR_ENCODER.sbeTemplateId())
        .schemaId(CAR_ENCODER.sbeSchemaId())
        .version(CAR_ENCODER.sbeSchemaVersion());


    bufferOffset += MESSAGE_HEADER_ENCODER.encodedLength();

    // Then encode the message
    messageFlyweight.wrap(directBuffer, bufferOffset);
```

The decoder should decode the header and then lookup which template should be used to decode the message body.

``` java
    // Reset the message header decoder in preparation for decoding a message.
    MESSAGE_HEADER_DECODER.wrap(directBuffer, bufferOffset);

    final int actingBlockLength = MESSAGE_HEADER_DECODER.blockLength();
    final int schemaId = MESSAGE_HEADER_DECODER.schemaId();
    final int actingVersion = MESSAGE_HEADER_DECODER.version();

    // Lookup template for decoding the message

    bufferOffset += MESSAGE_HEADER.encodedLength();
    messageFlyweight.wrap(directBuffer, bufferOffset, actingBlockLength, actingVersion);
```

### Single Fixed Size Fields

Single fixed fields can be encoded in a fluent style after a message flyweight has been reset for encoding.

``` java
    car.wrap(directBuffer, bufferOffset)
        .serialNumber(1234)
        .modelYear(2013)
        .available(BooleanType.T)
        .code(Model.A);
```

Decoding single fixed fields is simply the reverse.

``` java
    car.wrap(directBuffer, bufferOffset, actingBlockLength, actingVersion);

    sb.append("\ncar.serialNumber=").append(car.serialNumber());
    sb.append("\ncar.modelYear=").append(car.modelYear());
```

### Fixed Size Array Fields

It is possible to encode a fixed length array of primitive value in a field.

To encode the the array.

``` java
    for (int i = 0, size = CarEncoder.someNumbersLength(); i < size; i++)
    {
        car.someNumbers(i, i);
    }
```

Decoding is simply the reverse.

``` java
    sb.append("\ncar.someNumbers=");
    for (int i = 0, size = CarEncoder.someNumbersLength(); i < size; i++)
    {
        sb.append(car.someNumbers(i)).append(", ");
    }
```

### Fixed Size String Arrays

When encoding things like financial symbols it often beneficial to encode these as fixed length character arrays. SBE characters are single bytes. The default encoding is US-ASCII but it is possible to signify other encodings by using the characterEncoding attribute of the schema. In addition to the fixed array access method the following are provided.

For encoding a put method is defined taking a source byte array with an source offset at which to begin copying. The copy will always be for the size of the field.

``` java
    car.putVehicleCode(VEHICLE_CODE, srcOffset);
```

For decoding a get method is defined taking destination byte array with a destination offset at which to copy into.

``` java
    sb.append("\ncar.vehicleCode=")
      .append(new String(buffer, 0, car.getVehicleCode(buffer, 0, buffer.length), CarDecoder.vehicleCodeCharacterEncoding()));
```

### Constants

Constants do not get read from the underlying buffer. Their value as defined in the schema is returned directly by the codec using the same API as non-constant fields.

### Enumerations

Choice from the message schema directly map to enums in Java. Encoding is as follows.

``` java
    car.wrap(directBuffer, bufferOffset)
       .available(BooleanType.TRUE)
       .code(Model.A);
```

Decoding is simply the reverse.

``` java
    sb.append("\ncar.available=").append(car.available());
    sb.append("\ncar.code=").append(car.code());
```

### BitSets

A bitset is multi-value choice that is mapped to the presence or not of particular bits in an integer. The bitset is returned from the flyweight with properties that take true or false arguments for each named bit.

Encoding

``` java
    car.extras()
        .clear()
        .cruiseControl(true)
        .sportsPack(true)
        .sunRoof(false);
```

Decoding

``` java
    final OptionalExtrasDecoder extras = car.extras();
    sb.append("\ncar.extras.cruiseControl=").append(extras.cruiseControl());
    sb.append("\ncar.extras.sportsPack=").append(extras.sportsPack());
    sb.append("\ncar.extras.sunRoof=").append(extras.sunRoof());
```

### Composite Types

Composite types provide a means of reuse. They map directly to a class as a flyweight pattern in Java.

**Note**: For efficiency it is best to hold onto the reference to the type until all fields in the type have been accessed.

Encoding

``` java
    car.engine()
        .capacity(2000)
        .numCylinders((short)4)
        .putManufacturerCode(MANUFACTURER_CODE, srcOffset);
```

Decoding

``` java
    final EngineDecoder engine = car.engine();
    sb.append("\ncar.engine.capacity=").append(engine.capacity());
    sb.append("\ncar.engine.numCylinders=").append(engine.numCylinders());
    sb.append("\ncar.engine.maxRpm=").append(engine.maxRpm());
```

## Repeating Groups

Repeating groups allow for collections of repeating type which can even be nested. The groups are types represented as a flyweight.

To encode it is necessary to first stage the count of times the group will repeat and then use the next() method to cursor forward while encoding.

``` java
    final CarEncoder.PerformanceFiguresEncoder perfFigures = car.performanceFiguresCount(2);
    perfFigures.next()
        .octaneRating((short)95)
        .accelerationCount(3)
        .next().mph(30).seconds(4.0f)
        .next().mph(60).seconds(7.5f)
        .next().mph(100).seconds(12.2f);
    perfFigures.next()
        .octaneRating((short)99)
        .accelerationCount(3)
        .next().mph(30).seconds(3.8f)
        .next().mph(60).seconds(7.1f)
        .next().mph(100).seconds(11.8f);
```

To decode the flyweight implements Iterable and Iterator allowing for use with the foreach loop pattern.

``` java
    for (final CarDecoder.PerformanceFiguresDecoder performanceFigures : car.performanceFigures())
    {
        sb.append("\ncar.performanceFigures.octaneRating=").append(performanceFigures.octaneRating());

        for (final AccelerationDecoder acceleration : performanceFigures.acceleration())
        {
            sb.append("\ncar.performanceFigures.acceleration.mph=").append(acceleration.mph());
            sb.append("\ncar.performanceFigures.acceleration.seconds=").append(acceleration.seconds());
        }
    }
```

**Note**: Groups must be encoded and decoded in total before progressing to the next group or on to variable data.

## Variable Length Data

To store variable length strings or binary data the var data fields can be used at the end of the message. These are variable length byte arrays for which optional character encoding can be provided in the schema. Three variants of the API are provided for convenience.

The length of the string is typically encoded using an `uint8` or `uint16` primitive type. If strings longer than 64KB are required then uint32 needs to be the type of the length. As Java does not have unsigned types then the `maxValue` attribute must be set to avoid negative values. It is also worth keeping the maxValue well under 2GB to ensure there is space in a buffer for framing and other fields.

``` xml
    <composite name="varDataEncoding">
        <type name="length" primitiveType="uint32" maxValue="1073741824"/>
        <type name="varData" primitiveType="uint8" length="0" characterEncoding="UTF-8"/>
    </composite>
```

Encoding

``` java
        car.make(new String(MAKE));
        car.putModel(MODEL, srcOffset, MODEL.length);
```

Decoding

``` java
    sb.append("\ncar.make.semanticType=").append(CarEncoder.makeMetaAttribute(MetaAttribute.SEMANTIC_TYPE));
    sb.append("\ncar.make=").append(car.make());

    sb.append("\ncar.model=").append(
        new String(buffer, 0, car.getModel(buffer, 0, buffer.length), CarEncoder.modelCharacterEncoding()));

    final UnsafeBuffer tempBuffer = new UnsafeBuffer(buffer);
    final int tempBufferLength = car.getActivationCode(tempBuffer, 0, tempBuffer.capacity());
    sb.append("\ncar.activationCode=").append(new String(buffer, 0, tempBufferLength));

    sb.append("\ncar.encodedLength=").append(car.encodedLength());
```

**Note**: Variable data fields must be encoded and decoded in order as defined in the schema.
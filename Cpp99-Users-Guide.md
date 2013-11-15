After running the [SbeTool](Sbe-Tool-Guide) a number of C++99 source files will be created. These files represent the types and messages declared in the schema. For a quick start to SBE look a [this](https://github.com/real-logic/simple-binary-encoding/blob/master/examples/resources/TestSchema.xml) schema and its usage [here](https://github.com/real-logic/simple-binary-encoding/blob/master/examples/cpp99/SbeExample.cpp).

Messages are designed to be read in the sequential order as define in the schema. This ensures a [stream access](Design-Principles) pattern for performance. If groups or variable data are not processed in order then the data may become corrupt. Conceptually the message is encoded as a series of blocks. The blocks are the root fields, followed by each iteration of repeating groups, and finally followed by one or more variable data fields.

Due to the streaming nature of the codec the size of the message cannot be determined until encoding or decoding is complete. The method <code>MessageFlyweight::position()</code> will return the index in the underlying buffer at which the next block will commence, and the <code>MessageFlyweight::size()</code> method will return the current encoded size depending on how far it has progressed.

### Framing

It is expected that the messages are communicated inside a framing protocol. The frame defines the size of the buffer containing the message header and message itself.

    +-------------------------------------------------------------+
    |        +----------++----------------------------------+     |
    |Frame   |Msg Header||Message Body                      |     |
    |        +----------++----------------------------------+     |
    +-------------------------------------------------------------+

The frame may contain session or transport level fields that belong to different layers of the [OSI Model](http://en.wikipedia.org/wiki/OSI_model) and beyond the scope of the message codec which deals with the [layer 6](http://en.wikipedia.org/wiki/OSI_model#Layer_6:_presentation_layer) presentation.

### Message Header

The message header contains the fields that allows the decoder to identify what codec should be used as the template for a message.

1. **blockLength**: The length of the message root block before repeating groups or variable data commences.
1. **templateId**: The identifier for the template type of the message that is to follow.
1. **version**: The version of the template allowing for extension.

**Note**: A new message header type can be defined with different sizes of integers for the template and version according to needs.

To encode a message it is necessary to encode the header then the message.

    MESSAGE_HEADER.reset(directBuffer, bufferOffset, messageTemplateVersion)
                  .blockLength(messageFlyweight.blockLength())
                  .templateId((int)messageFlyweight.templateId())
                  .version((short)messageFlyweight.templateVersion());

    // Then encode the message
    messageFlyweight.resetForEncode(directBuffer, bufferOffset);

The decoder should decode the header and then lookup which template should be used to decode the message body.

    // Reset the message header in preparation for decoding a message.
    MESSAGE_HEADER.reset(directBuffer, bufferOffset, messageTemplateVersion);

    final int templateId = MESSAGE_HEADER.templateId();
    final int actingVersion = MESSAGE_HEADER.version();
    final int actingBlockLength = MESSAGE_HEADER.blockLength();

    // Lookup template for decoding the message

    bufferOffset += MESSAGE_HEADER.size();
    messageFlyweight.resetForDecode(directBuffer, bufferOffset, actingBlockLength, actingVersion);

### Single Fixed Fields

Single fixed fields can be encoded in a fluent style after a message flyweight has been reset for encoding.

    car.resetForEncode(directBuffer, bufferOffset)
       .serialNumber(1234)
       .modelYear(2013);

Decoding single fixed fields is simply the reverse.

    car.resetForDecode(directBuffer, bufferOffset, actingBlockLength, actingVersion);

    sb.append("\ncar.serialNumber=").append(car.serialNumber());
    sb.append("\ncar.modelYear=").append(car.modelYear());

### Fixed Array Fields

It is possible to encode a fixed length array of primitive value in a field.

To encode the the array.

    for (int i = 0, size = car.someNumbersLength(); i < size; i++)
    {
        car.someNumbers(i, i);
    }

Decoding is simply the reverse.

    sb.append("\ncar.someNumbers=");
    for (int i = 0, size = car.someNumbersLength(); i < size; i++)
    {
        sb.append(car.someNumbers(i)).append(", ");
    }

### Fixed String Arrays

When encoding things like financial symbols it often beneficial to encode these are fixed length character arrays. SBE characters are single bytes. The default encoding is US-ASCII but it is possible to signify other encodings by using the characterEncoding attribute of the schema. In addition to the fixed array access method the following are provided.

For encoding a put method is defined taking a source byte array with an source offset at which to begin copying. The copy will always be for the size of the field.

       car.putVehicleCode(VEHICLE_CODE, srcOffset);

For decoding a get method is define taking destination byte array with an destination offset at which copy into to.

        sb.append("\ncar.vehicleCode=")
          .append(new String(buffer, 0, car.getVehicleCode(buffer, 0, buffer.length), car.vehicleCodeCharacterEncoding()));

### Constants

Constants do not get read from the underlying buffer. Their value as defined in the schema is returned directly by the codec using the same API as non-constant fields.

### Enumerations

Choice from the message schema directly map to enums in Java. Encoding is as follows.

    car.resetForEncode(directBuffer, bufferOffset)
       .available(BooleanType.TRUE)
       .code(Model.A);

Decoding is simply the reverse.

    sb.append("\ncar.available=").append(car.available());
    sb.append("\ncar.code=").append(car.code());

### BitSets

A bitset is multi-value choice that is mapped to the presence or not of particular bits in an integer. The bitset is returned from the flyweight with properties that take true or false arguments for each named bit.

Encoding

    car.extras()
       .cruiseControl(true)
       .sportsPack(true)
       .sunRoof(false);

Decoding

    final OptionalExtras extras = car.extras();
    sb.append("\ncar.extras.cruiseControl=").append(extras.cruiseControl());
    sb.append("\ncar.extras.sportsPack=").append(extras.sportsPack());
    sb.append("\ncar.extras.sunRoof=").append(extras.sunRoof());

### Composite Types

Composite types provide a means of reuse. The map directly to a class as a flyweight pattern in Java.

**Note**: For efficiency it is best to hold onto the reference to the type until all fields in the type have been accessed.

Encoding

    car.engine()
       .capacity(2000)
       .numCylinders((short)4)
       .putManufacturerCode(MANUFACTURER_CODE, srcOffset);

Decoding

    final Engine engine = car.engine();
    sb.append("\ncar.engine.capacity=").append(engine.capacity());
    sb.append("\ncar.engine.numCylinders=").append(engine.numCylinders());
    sb.append("\ncar.engine.maxRpm=").append(engine.maxRpm());

## Repeating Groups

Repeating groups allow for collections of repeating type which can even be nested. The groups are types represented as a flyweight.

To encode it is necessary to first stage the count of time the group will repeat and then use the next() method to cursor forward while encoding.

    final Car.PerformanceFigures performanceFigures = car.performanceFiguresCount(2);
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

To decode the flyweight implements Iterable and Iterator allowing for use with the foreach loop pattern.

    for (final Car.PerformanceFigures performanceFigures : car.performanceFigures())
    {
        sb.append("\ncar.performanceFigures.octaneRating=").append(performanceFigures.octaneRating());

        for (final Car.PerformanceFigures.Acceleration acceleration : performanceFigures.acceleration())
        {
            sb.append("\ncar.performanceFigures.acceleration.mph=").append(acceleration.mph());
            sb.append("\ncar.performanceFigures.acceleration.seconds=").append(acceleration.seconds());
        }
    }

**Note**: Groups must be encoded and decoded in total before progressing to the next group or on to variable data.

## Variable Length Data

To store variable length strings or binary data then the var data fields can be used at the end of the message. These are variable length byte arrays for which optional character encoding can be provided in the schema.

Encoding

    car.putMake(MAKE, srcOffset, MAKE.length);
    car.putModel(MODEL, srcOffset, MODEL.length);

Decoding

    sb.append("\ncar.make=").append(new String(buffer, 0, car.getMake(buffer, 0, buffer.length), car.makeCharacterEncoding()));
    sb.append("\ncar.model=").append(new String(buffer, 0, car.getModel(buffer, 0, buffer.length), car.modelCharacterEncoding()));


**Note**: Variable data fields must be encoded and decoded in order as defined in the schema.

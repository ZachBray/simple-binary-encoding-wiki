After running the [SbeTool](Sbe-Tool-Guide) a number of Java source files will be created. These files represent the types and messages declared in the schema. For a quick start to SBE look a [this](https://github.com/real-logic/simple-binary-encoding/blob/master/examples/resources/TestSchema.xml) schema and its usage [here](https://github.com/real-logic/simple-binary-encoding/blob/master/examples/java/uk/co/real_logic/sbe/examples/SbeExample.java).

### Framing

It is expected that the messages are communicated inside a framing protocol. The frame defines the size of the buffer containing the message header and message itself.

    +-------------------------------------------------------------+
    |        +----------++----------------------------------+     |
    |Frame   |Msg Header||Message Body                      |     |
    |        +----------++----------------------------------+     |
    +-------------------------------------------------------------+

The frame may contain session or transport level fields that belong to different layers of the [OSI Model](http://en.wikipedia.org/wiki/OSI_model) and beyond the scope of the message codec which deals with the [layer 6](http://en.wikipedia.org/wiki/OSI_model#Layer_6:_presentation_layer) presentation.

### Message Header

The message header contains the fields that allows the decoder to identify what codec should be used for as the template for a message.

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


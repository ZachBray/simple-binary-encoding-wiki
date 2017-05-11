### History

The initial version of the C# generator was produced by Adaptive's Olivier Deheurles which supported The SBE standard up RC2. Most of that code is still in use but the tool now supports the draft standard.

### Quickstart

* Add the [sbe-tool nuget package](https://www.nuget.org/packages/sbe-tool?NormalizedVersion=0.1.7-alpha-1) to your .net framework project
* Look in `packages/sbe-tool*/example` for an example project
* Use `packages/sbe-tool*/bin/sbe-tool.sh` to generate CSharp from your schema

After running the [SbeTool](Sbe-Tool-Guide) a number of C# source files will be created. These files represent the types and messages declared in the schema. For a quick start to SBE look at [this](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-samples/src/main/resources/example-schema.xml) schema and its usage [here](https://github.com/real-logic/simple-binary-encoding/tree/master/csharp/examples). This example is used throughout this document.

### The detail

Messages are designed to be read in the sequential order as defined in the schema. This ensures a [stream access](Design-Principles) pattern for performance. If groups, or variable data, are not processed in order then the data may become corrupt. Conceptually a message is encoded as a series of blocks. The blocks are the root fields, followed by each iteration of repeating groups, and finally followed by one or more variable data fields.

Due to the streaming nature of the codec the encoded length of the message cannot be determined until encoding or decoding is complete.

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

The CSharp implementation uses the [DirectBuffer](https://github.com/real-logic/simple-binary-encoding/blob/master/csharp/sbe-dll/DirectBuffer.cs) implementation to hold the bytes used for encoding and decoding messages. It provides a fast efficient and garbage collection free mechanism for manipulating the underlying byte arrays,

``` csharp
// This byte array is used for encoding and decoding,
// this is what you would send on the wire or save to disk
var byteBuffer = new byte[4096];
// You then "wrap" the array with a DirectBuffer, this class is used by
// the generated code to read and write efficiently to the underlying byte array
var directBuffer = new DirectBuffer(byteBuffer);
```

The CSharp generated objects use a flyweight idiom so that encoding and decoding is done by simply reading and writing values directly into the underlying direct buffer implementation.

To encode a message and it's header:

``` csharp
// First the message header
messageHeader = new baseline.MessageHeader();
messageHeader.Wrap(directBuffer, bufferOffset, Car,Schemaversion);
messageHeader.BlockLength = Car.BlockLength;
messageHeader.SchemaId = Car.SchemaId;
messageHeader.TemplateId = Car.TemplateId;
messageHeader.Version = Car.Schemaversion;

bufferOffset += MessageHeader.Size;

// Then the message
var car = new Car();
car.WrapForEncode(directBuffer, bufferOffset);
// Populate the cars values (see below)
```

The decoder should decode the header and then lookup which template should be used to decode the message body.

``` csharp
// first we decode the header (in a real world scenario you would need the header to decide which SBE decoder you are going to use
bufferOffset = 0;
// position the MessageHeader object at the beginning of the array
MessageHeader.Wrap(directBuffer, bufferOffset, Car,SchemaVersion);

// Extract info from the header which you would use to lookup the
// applicable flyweight to decode this type of message based on templateId and version.
int actingBlockLength = MessageHeader.BlockLength;
int actingVersion = MessageHeader.Version;

bufferOffset += MessageHeader.Size;
// now we decode the message
CarExample.Decode(Car, directBuffer, bufferOffset, actingBlockLength, actingVersion);

// We can then directly read fields. (see below)
```

### Single Fixed Size Fields

Single fixed fields are represented as a field and can be accessed directly.

``` csharp
car.SerialNumber = 1234;
car.Available = BooleanType.T;
```

Decoding single fixed fields is simply the reverse.

``` csharp
var sb = new StringBuilder();
sb.Append("\ncar.serialNumber=").Append(car.SerialNumber);
sb.Append("\ncar.available=").Append(car.Available);
```

### Fixed Size Array Fields

It is possible to encode a fixed length array of primitive value in a field.

To encode the the array.

``` csharp
for (int i = 0, size = car.SomeNumbersLength; i < size; i++)
{
    car.SetSomeNumbers(i, (uint)i);
}
```

Decoding is simply the reverse.

``` csharp
sb.Append("\ncar.someNumbers=");
for (int i = 0, size = car.SomeNumbersLength; i < size; i++)
{
    sb.Append(car.GetSomeNumbers(i)).Append(", ");
}
```

### Fixed Size String Arrays

SBE characters are single bytes. The default encoding is US-ASCII but it is possible to signify other encodings by using
the characterEncoding attribute of the schema. In addition to the fixed array access method the following accessors are provided.

For encoding a set method is defined taking a source byte array with an source offset at which to begin copying. The copy will always be for the size of the field.
``` csharp
// Create a vehicle code of the correct encoding
VehicleCode = Encoding.GetEncoding(Car.VehicleCodeCharacterEncoding).GetBytes("abcdef");
car.SetVehicleCode(vehicleCode, 0); // second argument is offset
```

For decoding a `Get` method is defined taking destination byte array with a destination offset at which to copy into.

``` csharp
sb.Append("\ncar.vehicleCode=");
var vehicleCode = new byte[Car.VehicleCodeLength];
car.GetVehicleCode(vehicleCode, 0);
sb.Append(Encoding.GetEncoding(Car.VehicleCodeCharacterEncoding).GetString(vehicleCode, 0, Car.VehicleCodeLength));
```

There are also `Get` and `Set` methods for individual bytes of a fixed character array. So for example one could instead use:

``` csharp
for (int i = 0, size = Car.VehicleCodeLength; i < size; i++)
{
    sb.Append((char)car.GetVehicleCode(i));
}
```
### Constants

Constants do not get read from the underlying buffer. Their value as defined in the schema is returned directly by the codec using the same API as non-constant fields.

### Enumerations

Choice from the message schema directly map to enums in CSharp. Encoding is as follows.

``` csharp
car.Available = BooleanType.T;
car.Code = Baseline.Model.A;
```

Decoding is simply the reverse.

``` csharp
sb.Append("\ncar.available=").Append(car.Available);
sb.Append("\ncar.code=").Append(car.Code);
```

### BitSets

A bitset is multi-value choice that is mapped to the presence or not of particular bits in an integer. In C# the bitset
is mapped to a `[flags]` enum.

Encoding

``` csharp
car.Extras = OptionalExtras.CruiseControl | OptionalExtras.SportsPack;
```

Decoding

``` csharp
OptionalExtras extras = car.Extras;
sb.Append("\ncar.extras.cruiseControl=").Append((extras & OptionalExtras.CruiseControl) == OptionalExtras.CruiseControl);
sb.Append("\ncar.extras.sunRoof=").Append((extras & OptionalExtras.SunRoof) == OptionalExtras.SunRoof);
sb.Append("\ncar.extras.sportsPack=").Append((extras & OptionalExtras.SportsPack) == OptionalExtras.SportsPack);

```

### Composite Types

Composite types provide a means of reuse. They map directly to a class as a flyweight pattern in CSharp.

Encoding

``` csharp
car.Engine.Capacity = 2000;
car.Engine.NumCylinders = 4;
car.Engine.SetManufacturerCode(ManufacturerCode, srcOffset);
```

Decoding

``` csharp
Engine engine = car.engine;

sb.Append("\ncar.engine.numCylinders=").Append(engine.NumCylinders);
sb.Append("\ncar.engine.manufacturerCode=");
for (int i = 0, size = Engine.ManufacturerCodeLength; i < size; i++)
{
    sb.Append((char)engine.GetManufacturerCode(i));
}
```

## Repeating Groups

Repeating groups allow for collections of repeating types which may be nested. The groups are types represented as a flyweight.

To encode it is necessary to first stage the count of times the group will repeat and then use the `Next()` method to cursor forward while encoding.

``` csharp
Car.PerformanceFiguresGroup perfFigures = car.PerformanceFiguresCount(2);
perfFigures.Next();
perfFigures.OctaneRating = 95;

// this group is in turn nested in the first element of the previous group
Car.PerformanceFiguresGroup.AccelerationGroup acceleration = perfFigures.AccelerationCount(2).Next();
acceleration.Mph = 30;
acceleration.Seconds = 4.0f;

acceleration.Next();
acceleration.Mph = 60;
acceleration.Seconds = 7.5f;

// and so forth
```

For decoding the flyweight does not yet implement an iterable group so the same mechanism is followed.

``` csharp
// the nested group
var performanceFiguresGroup = car.PerformanceFigures;
while (performanceFiguresGroup.HasNext)
{
    var performanceFigures = performanceFiguresGroup.Next();
    sb.Append("\ncar.performanceFigures.octaneRating=").Append(performanceFigures.OctaneRating);
    var accelerationGroup = performanceFigures.Acceleration;
    while (accelerationGroup.HasNext)
    {
        var acceleration = accelerationGroup.Next();
        sb.Append("\ncar.performanceFigures.acceleration.mph=").Append(acceleration.Mph);
        sb.Append("\ncar.performanceFigures.acceleration.seconds=").Append(acceleration.Seconds);
    }
}
```

**Note**: Groups must be encoded and decoded in total before progressing to the next group or on to variable data.

## Variable Length Data

To store variable length strings or binary data the var data fields can be used at the end of the message or repeating group. These are variable length byte arrays for which optional character encoding can be provided in the schema. Three variants of the API are provided for convenience.

The length of the string is typically encoded using an `uint8` or `uint16` primitive type. If strings longer than 64KB
are required then `uint32` needs to be the type of the length. It is also worth keeping the `maxValue` well under 2GB to ensure there is space in a buffer for framing and other fields.

``` xml
<composite name="varDataEncoding">
    <type name="length" primitiveType="uint32" maxValue="1073741824"/>
    <type name="varData" primitiveType="uint8" length="0" characterEncoding="UTF-8"/>
</composite>
<composite name="varStringEncoding">
    <type name="length" primitiveType="uint32" maxValue="1073741824"/>
    <type name="varData" primitiveType="uint8" length="0" characterEncoding="UTF-8"/>
</composite>

```

An example of encoding a varStringEncoding

``` csharp
Manufacturer = Encoding.GetEncoding(Car.ManufacturerCharacterEncoding).GetBytes("Honda");
car.SetManufacturer(Manufacturer, 0, Manufacturer.Length);
```

Decoding

``` csharp
var buffer = new byte[128];
length = car.GetManufacturer(buffer, 0, buffer.Length);
sb.Append("\ncar.manufacturer=").Append(Encoding.GetEncoding(Car.ManufacturerCharacterEncoding).GetString(buffer, 0, length));
```

**Note**: Variable data fields must be encoded and decoded in order as defined in the schema.

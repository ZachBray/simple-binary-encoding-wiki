After running the [SbeTool](Sbe-Tool-Guide) a number of Golang source
files will be created. These files represent the types and messages
declared in the schema. For a quick start to SBE look at [this example
schema](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-samples/src/main/resources/example-schema.xml)
schema and its usage
[here](https://github.com/real-logic/simple-binary-encoding/tree/master/sbe-samples/src/main/java/uk/co/real_logic/sbe/examples).

Messages are designed to be read in the sequential order as defined in
the schema. This ensures a [stream access](Design-Principles) pattern
for performance. If groups, or variable data, are not processed in
order then the data may become corrupt. Conceptually a message is
encoded as a series of blocks. The blocks are the root fields,
followed by each iteration of repeating groups, and finally followed
by one or more variable data fields.

Due to the streaming nature of the codec the encoded length of the
message cannot be determined until encoding or decoding is complete.

### Framing

It is expected that the messages are communicated inside a framing
protocol. The frame defines the size of the buffer containing the
message header and message itself.

    +------------------------------------------------------------+
    |        +----------+----------------------------------+     |
    |Frame   |Msg Header|Message Body                      |     |
    |        +----------+----------------------------------+     |
    +------------------------------------------------------------+

The frame may contain session or transport level fields that belong to
different layers of the [OSI
Model](http://en.wikipedia.org/wiki/OSI_model) and beyond the scope of
the message codec which deals with the [layer
6](https://en.wikipedia.org/wiki/OSI_model#Layer_6:_Presentation_Layer)
presentation.

### Message Header

The message header contains the fields that allows the decoder to
identify what codec should be used as the template for a message.

1. **BlockLength**: The length of the message root block before repeating groups or variable data commences.
1. **TemplateId**: The identifier for the template type of the message that is to follow.
1. **SchemaId**: The identifier for the schema the message belongs to.
1. **Version**: The version of the schema allowing for extension.

**Note**: A message header type can be defined with other fields and with different
sizes of integers for the template and version according to needs. A ```MessageHeader```
type will always be generated and can be used but if a standard message header is used an
optimized ```SbeGoMessageHeader``` type is made generally available.

#### Encoding

To encode a message it is first necessary to encode the header. Using
the Car from the example-schema:

``` go
var car Car
var buf = new(bytes.Buffer)
m := NewSbeGoMarshaller() 

header := SbeGoMessageHeader{car.SbeBLockLength(), car.SbeTemplateId(), car.SbeSchemaId(), car.SbeSchemaVersion()}
header.Encode(m, buf)
```

and then for a (populated) car:
``` go
var car Car
// populate car
CarInit(car) // populate constant value fields
if err:= car.Encode(m, buf, false); err != nil {
    /// handle errors
}
```

Some notes of interest:

The marshaller ```m``` in the above example can be used for both
encoding and decoding and it contains state. As a result each goroutine 
should use it's own marshaller but they can be reused within an existing
goroutine for both encoding and decoding.

Here we use a ```bytes.Buffer``` type to hold our marshalled data but
this can be any ```io.Writer``` such as a socket, pipe etc and can
optionally add a bufio wrapper to coalesce write operations etc. You
can see examples of this
[here](https://github.com/real-logic/simple-binary-encoding/blob/master/gocode/src/example-schema/CarExample.go)

The two ```Encode()``` methods here have slightly different signatures
with the car's ```Encode()``` method having an additional boolean
```doRangeCheck``` argument. Messages have this argument for both
```Encode()``` and ```Decode()``` methods. Message range checking
guarantees that on encode no bytes are written to the output stream if
range checking fails and that on decode an entire message will have
been read from the input stream.

Types other than messages (e.g., groups, varadata) provide a
```RangeCheck()``` method used by the message encoders. It is imagined
that range checking would be used during development to ensure
messages are valid within the spec. For usage where performance is not
paramount it may be left enabled in production.

Each generated type ```Foo``` will have a ```FooInit()``` method to
populate constant value fields. It is only necessary to call this for
types that have constant value fields.

#### Decoding

The decoder decodes the header and can then lookup which template should be used to decode the message body.

``` go

var header SbeGoMessageHeader
m := NewSbeGoMarshaller() 
header.Decode(m, reader)

// Lookup template for decoding the message and determine it is a car
var car Car
if err := car.Decode(m, reader, header.SbeSchemaVersion(), header.SbeBlockLength(), true); err != nil {
    // error handling
}
```
## Golang Idioms

The Golang generator does not produce a flyweight implementation as
for Java and C++, but instead creates types with methods. This allows
the golang implementation to use the standard idiomatic constructs
such as channels for io, goroutines for multiplexing, etc.

This section describes the mappings between SBE types and golang.

### Single fixed fields

Single fixed fields are represented as a field in a type and are
directly accessed e.g.,

``` go
car.SerialNumber = 1234
model := car.ModelYear
```

Integer types (both signed and unsigned) are explicitly sized (e.g., ```uint16```, ```int64``` so they are correctly represented on the wire.

Single characters are represented as a ```byte```.

### Fixed Size Array Fields

SBE fixed size arrays are represented as golang fixed size arrays and can be referenced as such e.g.,

``` go
for i := 0; i < len(car.SomeNumbers); i++ {
    car.SomeNumbers[i] = i;
}
```

Fixed size string arrays are represented as fixed length byte arrays
in golang. There is no default character encoding but if the character
encoding is specified then validity may be performed by the
``RangeCheck()``` method. Currently validated character encodings are
UTF-8 and ASCII.

### Constants

Constants are not encoded and decoded. Their value, as defined in the
schema, is set automatically on decode and can be set using the
```TypeInit()``` method described above for encoding.

### Enumerations

Enumerations in the message schema do not map directly as golang does not
support an enumerated type. Instead they are represented as a type and a
variable containing the known constant values. From the Car example
there is a Model enumeration generated:

``` go
type ModelEnum byte
type ModelValues struct {
	A         ModelEnum
	B         ModelEnum
	C         ModelEnum
	NullValue ModelEnum
}

var Model = ModelValues{65, 66, 67, 0}
```

Using this would look like:
``` go
car.DiscountedModel = Model.C
```

Range checking of enumeration values is only performed when the
SchemaVersion is equal to or greater than the ActingVersion so that
new enumeration values are allowed to propagate to older decoders.

### BitSets

A bitset is multi-value choice that is encoded to the wire as bits in an integer
which represent their presence or not (1 vs 0).  Golang does not support
bitsets and so they are represented in code as an array of boolean
values. Again from the Car example the generated code looks like:

``` go
type OptionalExtras [8]bool
type OptionalExtrasChoiceValue uint8
type OptionalExtrasChoiceValues struct {
	SunRoof       OptionalExtrasChoiceValue
	SportsPack    OptionalExtrasChoiceValue
	CruiseControl OptionalExtrasChoiceValue
}

var OptionalExtrasChoice = OptionalExtrasChoiceValues{0, 1, 2}
```

and the usage is similar to enumerations:

``` go
car.Extras[OptionalExtrasChoice.CruiseControl] = true
```

### Composite Types

Composite types provide a means of reuse. They map directly to a type in Golang and are simply referenced e.g.,

``` go
car.Engine.Capacity = 17
cylinders = car.Engine.NumCylinders
```
### Repeating Groups

Repeating groups allow for collections of repeating type which can
even be nested. The groups are types represented as slices where the
length of the slice determines the number in the group. e.g.,

``` go
groupLength := len(car.FuelFigures)
// Grab the second element's Speed
speed := car.FuelFigures[1].Speed
```    

### Variable Length Data

Variable Length data is used in SBE to represent binary data (and
sometimes variable length strings). In golang these are also
represented as slices and behave as for groups above.

## Semantic Types

The golang generator provides no special support for semantic
types (as for Java and C++).

The `Boolean` semantic type is not represented as a golang `bool` as the
spec requires that discrepancies between SinceVersion and
ActingVersion can be resolved using the NullValue.

The `String` semantic type is simply represented as a byte array as
golang Strings are immutable and are not useful in structures.

The `UTCTimestamp`, `UTCTimeOnly`, `UTCDateOnly` and `MktTime` semantic types
are not represented as a golang `Time` type as none of these semantic
types contain a location. Mapping to a golang `Time` type will be
relatively easy for the application developer who will know the
location and can set it accordingly (null is UTC). They can also
decide what to with golang `Time`'s nanosecond timer when converting
(probably treating it as zero).
FIX/SBE uses an XML format to specify messages, headers, and other elements. The system is typed and users can create new types from primitives. In this section, we will discuss the practical aspects of the XML specification. For details, please see the FIX/SBE specification. An example schema can be found [here](https://github.com/real-logic/simple-binary-encoding/blob/master/examples/resources/TestSchema.xml).

### <code>messageSchema</code> Element

The `messageSchema` element is the root document in XML. It contains primarily all types and messages specified. This element has the following fields:

* `package`: Package. This is the namespace for C++ and package name for Java.
* `version`: Version number of the attached messages and types. Defaults to 0.
* `semanticVersion`: Semantic version information as a string.
* `description`: Description of the entire message schema. (optional)
* `byteOrder`: The default byte ordering (`bigEndian` or `littleEndian`) for all messages types. Default is `littleEndian`.

An example `messageSchema` element would be:

    <messageSchema package="uk.co.real_logic.sbe.examples"
                   semanticVersion="5.2"
                   description="Code generation unit test support"
                   byteOrder="littleEndian">

A `messageSchema` holds two main components. a list of `<types>` and a list of `<message>` elements.

### <code>types</code> Element

The `types` block holds various types created `<type>`, `<set>`, `<enum>`, and `<composite>`.

### Primitive Types

All encoding and decoding for SBE is centered around a set of basic primitive types. These
are the primitive types.

* `char`: Signed character. 8 bits.
* `int8`: Signed 8-bit encoding.
* `int16`: Signed 16-bit encoding.
* `int32`: Signed 32-bit encoding.
* `int64`: Signed 64-bit encoding.
* `uint8`: Unsigned 8-bit encoding.
* `uint16`: Unsigned 16-bit encoding.
* `uint32`: Unsigned 32-bit encoding.
* `uint64`: Unsigned 64-bit encoding.
* `float`: 32-bit floating point encoding. IEEE 754.
* `double`: 64-bit floating point encoding. IEEE 754.

### <code>type</code> Element

Types can be created by name and by composing a set of builtin primitive types. The `<type>`
element has the following attributes.

* `name`: Name of the type.
* `presence`: One of `constant`, `required`, or `optional`. Default is `required`.
* `semanticType`: Semantic information about the type. (optional)
* `description`: Description of the type. (optional)
* `primitiveType`: Primitive Type of the type.
* `length`: Length in primitive type units of the type. Default is 1.
* `characterEncoding`: Character Encoding of the type.

An few examples of the use of `<type>` are below:

    <type name="ModelYear" primitiveType="uint16" semanticType="ModelYear"/>
    <type name="VehicleCode" primitiveType="char" length="6" semanticType="VehicleCode"/>
    <type name="someNumbers" primitiveType="int32" length="5" semanticType="SomeArray"/>

### <code>composite</code> Element

A `composite` is a structure of encoded types. The encoding is in the order of declaration.
Some `composite` elements are used for message header declaration as well as variable length field
encoding and group dimension encoding. The `<composite>` element has the following attributes.

* `name`: Name of the type.
* `semanticType`: Semantic information about the type. (optional)
* `description`: Description of the type. (optional)

Examples of the use of `<composite>` are below:

    <composite name="messageHeader" description="Message Header" semanticType="Length">
        <type name="blockLength" primitiveType="uint16"/>
        <type name="templateId" primitiveType="uint16"/>
        <type name="version" primitiveType="uint8"/>
        <type name="reserved" primitiveType="uint8"/>
    </composite>

    <composite name="Engine" semanticType="Engine">
        <type name="capacity" primitiveType="uint16"/>
        <type name="numCylinders" primitiveType="uint8"/>
        <type name="maxRpm" primitiveType="uint16" presence="constant">9000</type>
        <type name="manufacturerCode" primitiveType="char" length="3"/>
        <type name="fuel" primitiveType="char" presence="constant">Petrol</type>
    </composite>

The `messageHeader` composite is treated as the header for all messages included in the message schema.

### <code>enum</code> Element

Enumerations use the `<enum>` element. This element contains the valid values for the enum. The element
has the following attributes:

* `name`: Name of the enumeration.
* `presence`: One of `constant`, `required`, or `optional`. Default is `required`.
* `semanticType`: Semantic information about the type. (optional)
* `description`: Description of the type. (optional)
* `encodingType`: Primitive Type of the encoding type. Must be `char` or `uint8`.

`<validValue>` elements are contained within the `<enum>` to hold the information about the values. Each element has the following attributes: `name` and `description`. The contents of the `<validValue>` element contains the value itself.

Examples of the use of `<enum>` are below:

    <enum name="BooleanType" encodingType="uint8" semanticType="Boolean">
        <validValue name="FALSE">0</validValue>
        <validValue name="TRUE">1</validValue>
    </enum>

    <enum name="Model" encodingType="char" semanticType="Model">
        <validValue name="A">A</validValue>
        <validValue name="B">B</validValue>
        <validValue name="C">C</validValue>
    </enum>

### <code>set</code> Element

Bit sets use the `<set>` element. This element contains the choice values for the bit set. The element
has the following attributes:

* `name`: Name of the bit set.
* `presence`: One of `constant`, `required`, or `optional`. Default is `required`.
* `semanticType`: Semantic information about the type. (optional)
* `description`: Description of the type. (optional)
* `encodingType`: Primitive Type of the encoding type. Must be `uint8`, `uint16`, `uint32`, or `uint64`.

`<choice>` elements are contained within the `<set>` to hold the information about the values. Each element has the following attributes: `name` and `description`. The contents of the `<choice>` element contains the bit position of that choice. I.e. 0 means bit position 0, 1 means bit position 1, etc.

Examples of the use of `<set>` are below:

    <set name="OptionalExtras" encodingType="uint8" semanticType="Extras">
        <choice name="sunRoof">0</choice>
        <choice name="sportsPack">1</choice>
        <choice name="cruiseControl">2</choice>
    </set>

### <code>message</code> Element

Message elements contain the fields, repeating groups, and variable length data. The order of `<field>`, `<group>`, and `<data>` within each message is very important as it is used to lay the data out in a message.
A `<message>` element itself has the following attributes:

* `name`: Name of the message.
* `description`: Description of the message. (optional)
* `id`: The ID of the message. Aka template ID of the message.
* `blockLength`: The length of the fixed fields in the messages in bytes.
* `semanticType`: The semantic type of the message.

An example `<message>` use is below:

    <message name="Car" id="1" description="Description of a basic Car">

### <code>field</code> Element

Field elements designate the 
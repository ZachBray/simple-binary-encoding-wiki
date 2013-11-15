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

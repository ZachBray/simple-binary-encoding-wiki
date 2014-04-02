FIX/SBE uses an XML format to specify messages, headers, and other elements. The system is typed and users can create new types from primitives. In this section, we will discuss the practical aspects of the XML specification. For details, please see the FIX/SBE specification. An example schema can be found [here](https://github.com/real-logic/simple-binary-encoding/blob/master/examples/resources/example-schema.xml). An XSD for SBE specifications can be found [here](https://github.com/real-logic/simple-binary-encoding/blob/master/main/resources/fpl/SimpleBinary1-0.xsd).

### <code>messageSchema</code> Element

The `messageSchema` element is the root document in XML. It contains primarily all types and messages specified. This element has the following attributes:

* `package`: Package. This is the namespace for C++ and package name for Java.
* `id`: Unique identifier for the schema.
* `version`: Version number of the attached messages and types. Defaults to 0.
* `semanticVersion`: Semantic version information as a string.
* `description`: Description of the entire message schema. (optional)
* `byteOrder`: The default byte ordering (`bigEndian` or `littleEndian`) for all messages types. Default is `littleEndian`.

An example `messageSchema` element would be:

    <messageSchema package="uk.co.real_logic.sbe.examples"
                   id="777"
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

    <composite name="messageHeader" description="Message identifiers and length of message root">
        <type name="blockLength" primitiveType="uint16"/>
        <type name="templateId" primitiveType="uint16"/>
        <type name="schemaId" primitiveType="uint16"/>
        <type name="version" primitiveType="uint16"/>
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
* `encodingType`: Primitive Type of the encoding type. Must be `char` or `uint8` or be a named type that uses one of those `primitiveType`s.

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
* `encodingType`: Primitive Type of the encoding type. Must be `uint8`, `uint16`, `uint32`, or `uint64` or be a named type that uses one of those `primitiveType`s.

`<choice>` elements are contained within the `<set>` to hold the information about the values. Each element has the following attributes: `name` and `description`. The contents of the `<choice>` element contains the bit position of that choice. I.e. 0 means bit position 0, 1 means bit position 1, etc.

Examples of the use of `<set>` are below:

    <set name="OptionalExtras" encodingType="uint8" semanticType="Extras">
        <choice name="sunRoof">0</choice>
        <choice name="sportsPack">1</choice>
        <choice name="cruiseControl">2</choice>
    </set>

### <code>message</code> Element

Message elements contain the fields, repeating groups, and variable length data. The order of `<field>`, `<group>`, and `<data>` within each message is very important as it is used to lay the data out in a message. All `<field>` declarations must precede all `<group>` and `<data>` elements. All `<group>` elements must precede all `<data>` elements. This ordering ensures that all fixed length fields are at the front of the message. Followed by repeating groups and then followed by variable length data.
A `<message>` element itself has the following attributes:

* `name`: Name of the message.
* `description`: Description of the message. (optional)
* `id`: The ID of the message. Aka template ID of the message.
* `blockLength`: The length in bytes of the fixed fields block at the beginning of messages until the first repeating group or variable data field.
* `semanticType`: The semantic type of the message.

An example `<message>` use is below:

    <message name="Car" id="1" description="Description of a basic Car">

### <code>field</code> Element

Field elements designate the fixed size fields of a message. A `<field>` element has the following attributes:

* `name`: Name of the field.
* `description`: Description of the field. (optional)
* `id`: The ID of the field. Aka the schema ID. For FIX, this is the Tag.
* `semanticType`: The semantic type of the field.
* `type`: The type of the field. This can be any primitive type, `<type>`, `<composite>`, `<enum>`, or `<set>`.
* `offset`: Offset in bytes of this field from the start of the `<message>` or `<group>`. (optional)
* `sinceVersion`: The version where this field was added. Defaults to 0.

Examples of the use of `<field>` are below:

    <field name="serialNumber" id="1" type="uint32" semanticType="SerialNumber"/>
    <field name="modelYear" id="2" type="ModelYear"/>
    <field name="available" id="3" type="BooleanType"/>
    <field name="code" id="4" type="Model"/>
    <field name="someNumbers" id="5" type="someNumbers"/>
    <field name="vehicleCode" id="6" type="VehicleCode"/>
    <field name="extras" id="7" type="OptionalExtras"/>
    <field name="engine" id="8" type="Engine"/>

### <code>group</code> Element

Group elements designate a repeating group in a message. Each group contains one or more `<field>` elements. In addition, the group dimensions must have been declared as a composite type. `<group>` elements may be nested arbitrarily deep. A `<group>` element has the following attributes:

* `name`: Name of the group.
* `description`: Description of the group. (optional)
* `id`: The ID of the group. Aka the schema ID.
* `dimensionType`: The composite type that is used to hold the group dimensions. This must be composed of a `numInGroup` type as well as a `blockLength` type. This defaults to "groupSizeEncoding".
* `blockLength`: The length of the fixed fields in the group in bytes.

Examples of the use of `<field>` are below:

    <composite name="groupSizeEncoding" description="Repeating group dimensions" >
         <type name="blockLength" primitiveType="uint16"/>
         <type name="numInGroup" primitiveType="uint8" semanticType="NumInGroup"/>
    </composite>

    <group name="fuelFigures" id="9" dimensionType="groupSizeEncoding">
         <field name="speed" id="10" type="uint16"  semanticType="int"/>
         <field name="mpg" id="11" type="float" semanticType="int"/>
    </group>

A nested repeating group example is below:

    <group name="performanceFigures" id="12" dimensionType="groupSizeEncoding">
        <field name="octaneRating" id="13" type="uint8" semanticType="RON"/>
        <group name="acceleration" id="14" dimensionType="groupSizeEncoding">
             <field name="mph" id="15" type="uint16"  semanticType="int"/>
             <field name="seconds" id="16" type="float" semanticType="int"/>
        </group>
    </group>

### <code>data</code> Element

Data elements designate the variable length fields of a message. 
The data is encoded as a `length` followed by the variable data. This type must have been declared as a composite type in a specific way.
A `<data>` element has the following attributes:

* `name`: Name of the variable data field.
* `description`: Description of the field. (optional)
* `id`: The ID of the field. Aka the schema ID. For FIX, this is the Tag.
* `semanticType`: The semantic type of the field.
* `type`: The composite type of the field. This must be composed of a `length` part and a `varData` part.
* `sinceVersion`: The version where this field was added. Defaults to 0.

Examples of the use of `<data>` are below:

    <composite name="varDataEncoding">
        <type name="length" primitiveType="uint8" semanticType="Length"/>
        <type name="varData" primitiveType="uint8" length="0" characterEncoding="UTF-8" semanticType="data"/>
    </composite>

    <data name="make" id="17" type="varDataEncoding" semanticType="Make"/>
    <data name="model" id="18" type="varDataEncoding" semanticType="Model"/>

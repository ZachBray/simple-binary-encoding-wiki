SBE supports the extension of messages via versioned templates. To be backwards compatible a template must only be extended at the end of a block. Messages cannot remain backwards compatible if existing fields are modified or removed. SBE is a fixed format encoding that supports new **optional** fields being added.

### Message Blocks

Messages are organised into blocks to allow for variable length encoding. The fields of message before groups are considered the root block and their total size is the ```blockLength``` as encoded in the ```messageHeader```.

Groups are blocks of fields that repeat for a given count. Both messages and repeating groups have a header with a block header that encodes the block length.

It is possible to add fields at the end of either the root block of a messages or the block of fields making up a repeating ```group```.

It is **not** possible to add fields to a ```composite``` type without creating a new message template and schema version.

### Adding a new Field

A new field can be added by creating a new ```messageSchema``` and increasing the ```version``` number of the schema, versions start at 0. The new field can then be added at the end of the root block in the ```message``` or the end of a block in a ```group```. The new field should have its ```sinceVersion``` attribute set to be the version number that has been used for the new schema and its ```presence=optional``` attribute set.

```xml
    <messageSchema package="extension"
                   version="1"
                   semanticVersion="5.2"
                   description="Code generation unit test support"
                   byteOrder="littleEndian">

        <message name="Car" id="1" description="Description of a basic Car">
            <field name="serialNumber" id="1" type="uint32"/>
            <field name="modelYear" id="2" type="ModelYear"/>
            <field name="available" id="3" type="BooleanType"/>
            <field name="code" id="4" type="Model"/>
            <field name="someNumbers" id="5" type="someNumbers"/>
            <field name="vehicleCode" id="6" type="VehicleCode"/>
            <field name="extras" id="7" type="OptionalExtras"/>
            <field name="engine" id="8" type="Engine"/>
            <field name="cupHolderCount" id="100" type="uint8" presence="optional" sinceVersion="1"/>
            <!-- Groups continue below -->
```

### Decoding a message from a previous version

A previous version of an encoded message will not have the extension fields in new versions. For a new version of a decoder to handle an older version it is necessary for it to act like a previous version to ensure it does not read beyond the end of an existing block. The decoder must return the null representation for the extension fields when acting as a previous version.

```java
    // Lookup the applicable flyweight to decode this type of message based on templateId and version.
    final int templateId = MESSAGE_HEADER.templateId();
    final short actingVersion = MESSAGE_HEADER.version();
    final int actingBlockLength = MESSAGE_HEADER.blockLength();

    bufferOffset += MESSAGE_HEADER.size();
    car.wrapForDecode(directBuffer, bufferOffset, actingBlockLength, actingVersion); 

    //
    final String cupHolderCount = car.cupHolderCount() == Car.cupHolderCountNullVal() ? "null" : car.cupHolderCount() + "";
    sb.append("\ncar.cutHolderCount=").append(cupHolderCount); 
```
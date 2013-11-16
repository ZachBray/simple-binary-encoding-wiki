SBE uses an Intermediate Representation (IR) between data layout mechanisms and code generators and on-the-fly decoders. [SBE Tool](Sbe-Tool-Guide) can generate serialized IR via the `sbe.ir.filename` option. This generates a file, conventionally using the `.sbeir` extension, that can serve as input to SBE Tool instead of XML for code generation. In addition, the On-The-Fly (OTF) decoder, requires serialized IR for decoding. In this section, we will explore how to generate IR and use it, as well as how IR works.

### Generating Serialized IR

Generating serialized IR is straight forward with the SBE Tool. Simply set `sbe.ir.filename` to point to the filename within the target directory to write the serialized IR to. Here is an example.

    $ java -Dsbe.ir.filename=<message-declarations-file.sbeir> -jar sbe.jar <message-declarations-file.xml>

### Using IR as Input to SBE Tool

### Structure of Serialized IR

SBE uses an Intermediate Representation (IR) between data layout mechanisms and code generators and on-the-fly decoders. [SBE Tool](Sbe-Tool-Guide) can generate serialized IR via the `sbe.ir.filename` option. This generates a file, conventionally using the `.sbeir` extension, that can serve as input to SBE Tool instead of XML for code generation. In addition, the On-The-Fly (OTF) decoder, requires serialized IR for decoding. In this section, we will explore how to generate IR and use it, as well as how IR works.

NOTE: it is our hope that serialized IR can form the foundation for a tool chain approach.

### Generating Serialized IR

Generating serialized IR is straight forward with the SBE Tool. Simply set `sbe.ir.filename` to point to the filename within the target directory to write the serialized IR to. Here is an example.

    $ java -Dsbe.ir.filename=<message-declarations-file.sbeir> -jar sbe.jar <message-declarations-file.xml>

### Using IR as Input to SBE Tool

Serialized IR can act as input to the SBE Tool for code generation. This is done by using the file extension `.sbeir` instead of `.xml`. Here is an example.

    $ java -jar sbe.jar <message-declarations-file.sbeir>

Therefore it is possible to use serialized IR as a means to pass around "compiled" representations.

### Structure of Serialized IR

IR is serialized via SBE itself. The schema for SBE IR is [here](https://github.com/real-logic/simple-binary-encoding/blob/master/main/resources/sbe-ir.xml).

The format of a serialized IR file is a simple sequence, or list, of "Tokens". Each Token is one of the `SerializedToken` messages in the schema above. The first sequence of Tokens in the file is the message header for the messages. This is a composite, usually with the name `messageHeader` that holds the following three encodings: `blockLength`, `templateId`, and `version`.

<table>
<tr>
  <td>Header (List of Tokens)</td>
  <td>Message(s) (List of Tokens)</td>
</tr>
</table>

Careful readers may realize that it is simple to add more messages to an IR file by concatenation. Just concat the list of Tokens to the end of the file. This is intentional.

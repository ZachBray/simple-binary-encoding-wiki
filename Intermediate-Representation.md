SBE uses an Intermediate Representation (IR) between data layout mechanisms and code generators and on-the-fly decoders. [SBE Tool](Sbe-Tool-Guide) can generate serialized IR via the `sbe.ir.filename` option. This generates a file, conventionally using the `.sbeir` extension, that can serve as input to SBE Tool instead of XML for code generation. In addition, the On-The-Fly (OTF) decoder, requires serialized IR for decoding. In this section, we will explore how to generate IR and use it, as well as how IR works.

NOTE: it is our hope that serialized IR can form the foundation for a tool chain approach.

### Generating Serialized IR

Generating serialized IR is straight forward with the SBE Tool. Simply set `sbe.ir.filename` to point to the filename within the target directory to write the serialized IR to. Here is an example.

    $ java -Dsbe.ir.filename=<message-declarations-file.sbeir> -jar sbe-all-1.7.10.jar <message-declarations-file.xml>

### Using Serialized IR as Input to SBE Tool

Serialized IR can act as input to the SBE Tool for code generation. This is done by using the file extension `.sbeir` instead of `.xml`. Here is an example.

    $ java -jar sbe-all-1.7.10.jar <message-declarations-file.sbeir>

Therefore it is possible to use serialized IR as a means to pass around "compiled" representations.

### Structure of Serialized IR

IR is serialized via SBE itself. The schema for SBE IR is [here](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-tool/src/main/resources/sbe-ir.xml).

The format of a serialized IR file is a simple sequence, or list, of "Tokens". Each Token is one of the `SerializedToken` messages in the schema above. The first sequence of Tokens in the file is the message header for the messages. This is a composite, usually with the name `messageHeader` that holds the following three encodings: `blockLength`, `templateId`, and `version`. After the header is one or more messages represented by sequences of Tokens. At a high level, a serialized IR file looks like this:

<table>
<tr>
  <td>Header (List of Tokens)</td>
  <td>Message(s) (List of Tokens)</td>
</tr>
</table>

Each Token has several fields, one of which is the `Signal`. See [here](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-tool/src/main/java/uk/co/real_logic/sbe/ir/Token.java) and [here](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-tool/src/main/java/uk/co/real_logic/sbe/ir/Signal.java) for more
detail on the Token elements.
The header would be expanded to look something like below.

<table>
<tr>
  <td align="center">BEGIN_COMPOSITE<br>name="messageHeader"</td>
  <td align="center">ENCODING<br>name="blockLength"<br>type=uint16<br>size=2<br>offset=0</td>
  <td align="center">ENCODING<br>name="templateId"<br>type=uint16<br>size=2<br>offset=2</td>
  <td align="center">ENCODING<br>name="version"<br>type=uint8<br>size=1<br>offset=4</td>
  <td align="center">END_COMPOSITE<br>name="messageHeader"</td>
</tr>
</table>

Each message is similar in that it is started with `BEGIN_MESSAGE` and ended with `END_MESSAGE`.

Careful readers may realise that it is simple to add more messages to an IR file by concatenation. Just concatenate a list of Tokens to the end of the file. This is intentional and provides a simple mechanism for composition.

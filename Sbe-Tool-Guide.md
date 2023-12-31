The SBE tool is a command line utility that can be used to generate codecs and validate message declaration schemas. The tool is written in Java and can run as an executable JAR file, e.g:

    $ java -jar sbe-all-${SBE_LIB_VERSION}.jar <message-declarations-file.xml>

The tool will accept options using the system property option format as follows:

    $ java [-Doption=value] -jar sbe-all-${SBE_TOOL_VERSION}.jar <message-declarations-file.xml>

The tool supports the following options:
 * `sbe.xinclude.aware`: Is XInclude supported for the schema. Defaults to `false`.
 * `sbe.validation.xsd`: Use XSD to validate or not. XSD filename should be the provided option.
 * `sbe.validation.stop.on.error`: Should the parser stop on first error encountered? Defaults to `false`.
 * `sbe.validation.warnings.fatal`: Are warnings in parsing considered fatal? Defaults to `false`.
 * `sbe.validation.suppress.output`: Should the parser suppress output during validation? Defaults to `false`.
 * `sbe.generate.stubs`: Generate stubs or not. Defaults to `true`.
 * `sbe.target.language`: Target language for code generation, defaults to `Java` (also supports: `CPP`, `C`, and `Golang`). Additionally a fully qualified class name can be provided which implements [TargetCodeGeneratorLoader](https://github.com/real-logic/simple-binary-encoding/blob/master/sbe-tool/src/main/java/uk/co/real_logic/sbe/generation/TargetCodeGenerator.java), e.g. `uk.co.real_logic.sbe.generation.csharp.CSharp`.
 * `sbe.generate.ir`: Generate IR or not. Defaults to `false`.
 * `sbe.output.dir`: Target directory for output generation, defaults to current directory.
 * `sbe.java.generate.interfaces`: Generate implements clauses for Java common interfaces from [Agrona](https://github.com/real-logic/agrona/tree/master/agrona/src/main/java/org/agrona/sbe). Defaults to `false`.
 * `sbe.java.encoding.buffer.type`: Buffer type to be used for the encoder stubs. This type needs to mutate the underlying buffer. Defaults to [MutableDirectBuffer](https://github.com/real-logic/agrona/blob/master/agrona/src/main/java/org/agrona/MutableDirectBuffer.java).
 * `sbe.java.decoding.buffer.type`: Buffer type to be used for the decoder stubs. This type only reads the underlying buffer. Defaults to [DirectBuffer](https://github.com/real-logic/agrona/blob/master/agrona/src/main/java/org/agrona/DirectBuffer.java).
 * `sbe.target.namespace`: Namespace to be used for generated code. Equates to package for Java. If not provided then the `package` attribute from the schema will be used.
 * `sbe.cpp.namespaces.collapse`: Boolean to turn on or off collapsing of nested namespaces in generated C++ stubs. Defaults to `false`.
 * `sbe.java.generate.group-order.annotation`: Should the `GroupOrder` annotation be generated, defaults to `false`.
 * `sbe.keyword.append.token`: String to append to schema tokens that collide with reserved words in the target language.
 * `sbe.decode.unknown.enum.values`: Support unknown decoded enum values.
 * `sbe.csharp.generate.namespace.dir`: Should a directory be created for the namespace under the output directory? Defaults to `true`.
 * `sbe.generate.access.order.checks`: Generate code to check flyweight methods are accessed in a valid order? Defaults to `false`. This option is supported by the Java, C#, and C++ generators. Requires platform-specific configuration to enable the checks at runtime, e.g., setting a system property or constant symbol. Find out more in [Safe Flyweight Usage](./Safe-Flyweight-Usage).
 * `sbe.cpp.disable.implicit.copying`: Disable generation of copy constructors and copy assignment operators? Defaults to `false`. 

The SBE tool can be used with Maven
[see](https://github.com/real-logic/simple-binary-encoding/wiki/Sbe-Tool-Maven)
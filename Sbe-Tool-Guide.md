The SBE tool is a command line utility that can be used to generate codecs and validate message declaration schemas. The tool is written in Java and can run as an executable JAR file, e.g:

    java -jar sbe.jar <message-declarations-file.xml>

The tool will accept options using the system property option format as follows:

    java [-Doption=value] -jar sbe.jar <message-declarations-file.xml>

The tool supports the following options:
 * <code>sbe.validation.xsd</code>: Use XSD to validate or not. XSD filename should be the provided option.
 * <code>sbe.validation.stop.on.error</code>: Should the parser stop on first error encountered? Defaults to false.
 * <code>sbe.validation.warnings.fatal</code>: Are warnings in parsing considered fatal? Defaults to false.
 * <code>sbe.validation.suppress.output</code>: Should the parser suppress output during validation? Defaults to false.
 * <code>sbe.generate.stubs</code>: Generate stubs or not. Defaults to true.
 * <code>sbe.generate.ir</code>: Generate IR or not. Defaults to false.
 * <code>sbe.target.language</code>: Target language for code generation, defaults to Java (also supports Cpp98 and CSharp).
 * <code>sbe.output.dir</code>: Target directory for output generation, defaults to current directory.
 * <code>sbe.target.namespace</code>: Namespace to be used for generated code. Equates package for Java. If not provided then the `package` attribute from the schema will be used.

The SBE tool can be used with Maven
[see](https://github.com/real-logic/simple-binary-encoding/wiki/Sbe-Tool-Guide)
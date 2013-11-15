The SBE tool is a command line utility that can be used to generate codecs and validate message declaration schemas. The tool is written in Java and can run as a executable JAR file.

    java -jar sbe.jar <message-declarations-file.xml>

The tool will accept options using the system property option format as follows.

    java -jar sbe.jar -Doption=value <message-declarations-file.xml>

The tool supports the following options:
 * <code>sbe.validation.xsd</code>: Use XSD to validate or not. XSD filename should be the provided option.
 * <code>sbe.validation.stop.on.error</code>: Should the parser stop on first error encountered? Defaults to false.
 * <code>sbe.validation.warnings.fatal</code>: Are warnings in parsing considered fatal? Defaults to false.
 * <code>sbe.validation.suppress.output</code>: Should the parser suppress output during validation? Defaults to false.
 * <code>sbe.should.generate</code>: Generate or not. Defaults to true
 * <code>sbe.target.language</code>: Target language for code generation, defaults to Java.
 * <code>sbe.output.dir</code>: Target directory for code generation, defaults to current directory.
 * <code>sbe.ir.filename</code>: Filename to serialize IR to within the target directory.
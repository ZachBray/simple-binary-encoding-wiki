### 1.4.0-RC4 (22 Apr 2016)
* Correct examples for illustration of how to do message extension.
* Update On-The-Fly decoding to assign contextual names for types within composites.
* Add feature support for [RC4 Specification](https://github.com/FIXTradingCommunity/fix-simple-binary-encoding/tree/master/v1-0-RC4) which brings in offsets and refs in composites, and restricted ranges on repeating groups.
* Upgrade to Agrona 0.5 to bring in support for encoding into expandable buffers.
* Upgrade to Checkstyle 6.17.
* Upgrade to JMH 1.12.

### 1.3.5-RC3 (01 Apr 2016)
* Performance improvements to C++ generated stub by using move constructors where possible.
* Add a check for string length to avoid truncation in C++ generated stubs.
* Validate that number of items as a repeat count is within range for groups in Java generated stubs.
* Use UINT16, rather than UINT8, for number of times a group repeats in the examples.
* Use UINT32 length strings in the examples.
* Support for UINT32 length var-data strings as fields. Previously allowed UINT8 and UINT16 length strings.
* Fix bug with UINT32 type in Java not treating the most significant bit correctly.

### 1.3.4-RC3 (24 Mar 2016)
* Generate MessageEncoder and MessageDecoder interfaces to common message methods.
* Fix up code for Agrona changes to org.agrona package structure.
* Update to Checkstyle 6.16.1.
* Update to Gradle 2.12.
* Update to Agrona 0.4.13.

### 1.3.3-RC3 (01 Mar 2016)
* Validate length of encoded var data strings against length type of var data in schema.
* Update var data length type in IR and examples to be uint16 to support larger strings.
* Added the ability to append a token to property names that have keyword clashes.
* Added fluent API support for putting strings into var data fields.
* Update to Checkstyle 6.15.
* Update to Gradle 2.11.
* Update to Agrona 0.4.11.

### 1.3.2-RC3 (06 Feb 2016)
* Warn that optional presence is not allowed on var data fields.
* Bring C++ inline with Java features.
* Update to Agrona 0.4.11.
* Constant values for enums need to be in US-ASCII rather than default encoding.
* Code quality changes to reduce number of Sonar warnings.

### 1.3.1-RC3 (18 Jan 2016)
* Add Checkstyle for indentation and fix violations.
* Update to JMH 1.11.3
* Check for keywords class on formatted property field.
* Update to Agrona 0.4.10.
* Fix publishing of sbe-all JAR to Maven Central.

### 1.3-RC3 (06 Jan 2016)
* Updated implementation to reflect the [RC3 Specification](https://github.com/FIXTradingCommunity/fix-simple-binary-encoding/tree/master/v1-0-RC3/doc). RC3 is more generally useful with support added for var data in repeating groups and composites that can contain other types than just primitive encodings.
* Significant rewrite of Java and C++ OTF implementation to be more efficient with no back or re-scanning of the IR, plus they are now similar in usage and API.
* Java generated stubs are no longer dependent on CodecUtil and one level less for better inlining by the optimiser.
* IR updated to carry the description and count of tokens per component.
* Other language implementations have been removed until ports for RC3 are available.
* Project structure updated to be a Gradle modular build.
* RC4 features are available for testing if appropriate RC4 XSD schema is provided or schema validation is turned off.
* Generated code is marked with the @Generated annotation.
* C++ updated to C++ 11.
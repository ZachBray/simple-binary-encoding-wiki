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
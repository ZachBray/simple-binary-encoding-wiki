### 1.10.1 (16 Dec 2018)
* Warnings clean up in code base.
* Add OS X to Travis for native build.
* Fix golang generated codec for metadata attributes. [Issue #620](https://github.com/real-logic/simple-binary-encoding/issues/620).
* Add `Span` support to generated C# codecs. [PR #589](https://github.com/real-logic/simple-binary-encoding/pull/589).
* Upgrade to Checkstyle 8.15.
* Upgrade to Gradle 4.10.3.

### 1.10.0 (25 Nov 2018)
* Add support for `Span` in C# codecs. [PR #589](https://github.com/real-logic/simple-binary-encoding/pull/589).
* Check when wrapping the same buffer in all types to avoid Java GC write barrier where possible.
* Set `parentMessage` on construction of group codecs and remove need for dimensions composite to reduce footprint and improve performance.
* Allow signed `numInGroup` and issue warning to support `int32` type. [PR #617](https://github.com/real-logic/simple-binary-encoding/pull/617).
* Allow flexibility on group header member ordering in codecs. [PR #614](https://github.com/real-logic/simple-binary-encoding/pull/614).
* Fix Golang return type bug. [PR #611](https://github.com/real-logic/simple-binary-encoding/pull/611).
* Allow `uint32` for `blockLength` in Java codecs but issue warning. [Issue #609](https://github.com/real-logic/simple-binary-encoding/issues/609).
* Add non-const buffer access method to C++ codecs.
* Upgrade to Mockito 2.23.4.
* Upgrade to Shadow 4.0.3.
* Upgrade to Agrona 0.9.27.

### 1.9.0 (29 Oct 2018)
* This is the first release that supports building and running on Java 11.
* Add a `wrap<field-name>(DirectBuffer)` to Java codecs for more efficient consumption of strings.
* Return `const char *` from `buffer()` method in C++ codecs. [Issue #601](https://github.com/real-logic/simple-binary-encoding/issues/601).
* Support constant members in message header Java codecs. [PR #602](https://github.com/real-logic/simple-binary-encoding/issues/602).
* Generate static constexpr methods for C++ codecs for constant enums. [Issue #591](https://github.com/real-logic/simple-binary-encoding/issues/591).
* Added encoding offset and other metadata to C++ composite codecs. [Issue #594](https://github.com/real-logic/simple-binary-encoding/issues/594).
* Fix length encoding on arrays for C++ codecs. [Issue #595](https://github.com/real-logic/simple-binary-encoding/issues/595).
* Add methods to access schema id and version for all fixed flyweights for Java and C++. [Issue #593](https://github.com/real-logic/simple-binary-encoding/issues/593).
* Detect duplication ids in message fields. [Issue #597](https://github.com/real-logic/simple-binary-encoding/issues/597).
* Add Travis build matrix for Java 8 and 11 for OpenJDK and Oracle JDK.
* Upgrade to Checkstyle 8.14.
* Upgrade to Mockito 0.23.0.
* Upgrade to Agrona 0.9.26.

### 1.8.9 (05 Oct 2018)
* Fix string format warnings in Rust and Golang generators.
* Add Travis build.
* Upgrade to Shadow 4.0.0.
* Upgrade to Gradle 4.10.1.
* Upgrade to Agrona 0.9.25.

### 1.8.8 (12 Sep 2018)
* Fix initialisation of encoding type with length is zero for C++. [Issue #583](https://github.com/real-logic/simple-binary-encoding/issues/583).
* Fix formatting of property access methods for Java within composites. [Issue #582](https://github.com/real-logic/simple-binary-encoding/issues/582).
* Upgrade to Mockito 2.22.0.
* Upgrade to Gradle 4.10.
* Upgrade to Agrona 0.9.24.

### 1.8.7 (18 Aug 2018)
* Use find package to discover java executable when building C++ codecs tests.
* Use pointers in golang codecs to improve efficiency. [PR #578](https://github.com/real-logic/simple-binary-encoding/pull/578).
* Upgrade to Checkstyle 8.12.
* Upgrade to Mockito 2.21.0.
* Upgrade to Agrona 0.9.23.

### 1.8.6 (30 Jul 2018)
* Provide the ability to query for an empty bit set in Java and C++ codecs for simplicity. [PR #575](https://github.com/real-logic/simple-binary-encoding/pull/575).
* Upgrade to Mockito 2.20.1.
* Upgrade to Gradle 4.9.
* Upgrade to Agrona 0.9.22.

### 1.8.5 (19 Jul 2018)
* Invalid Java generated for codec with an extension string field that is appendable. [PR #574](https://github.com/real-logic/simple-binary-encoding/pull/574).

### 1.8.4 (16 Jul 2018)
* Upgrade to Mockito 2.19.1.
* Upgrade to Agrona 0.9.21.

### 1.8.3 (07 Jul 2018)
* Regenerate IR Codecs to quote strings when printing.
* Upgrade to Checkstyle 8.11.
* Upgrade to Gradle 4.8.1.
* Upgrade to Agrona 0.9.20.

### 1.8.2 (03 Jul 2018)
* Update to latest CME fix binary market template for testing.
* Allow, with warning, `numInGroup` type to use `uint32` for repeating groups.
* Add support to C# codecs for constant presence enums.
* Fix issue with loss of IR data on encoding the IR for constant presence enums. [Issue #560](https://github.com/real-logic/simple-binary-encoding/issues/560).
* Complete functionality for `JsonPrinter` so that an SBE message can be transformed to JSON for debugging. [Issue #560](https://github.com/real-logic/simple-binary-encoding/issues/560).
* Remove debugging output from C++ IR decoder. [Issue #559](https://github.com/real-logic/simple-binary-encoding/issues/559).
* Make `HEADER_SIZE` a public constant in Java codecs for repeating groups.
* Allow Java decoders to use an `Appendable` for ASCII strings. [PR #557](https://github.com/real-logic/simple-binary-encoding/pull/557).
* Upgrade to Mockito 2.19.0.
* Upgrade to Checkstyle 8.10.1.
* Upgrade to Agrona 0.9.19.
* Upgrade to GSON 2.8.5.

### 1.8.1 (04 May 2018)
* Upgrade to JMH 1.21.
* Upgrade to Checkstyle 8.10.
* Upgrade to Shadow 2.0.4.
* Upgrade to Agrona 0.9.18.

### 1.8.0 (29 Apr 2018)
* Use `Constructor` to dynamically create new instances to avoid deprecated warning in Java 10.
* Remove use of `javax.annotation.Generated` so that the generated codecs compile and and run on Java 10.
* Rename `position` methods in C++ generated codecs to `sbePosition` to avoid clash with Aeron codecs for the archive and clustering. **Note:** this is a breaking change for those who have used these methods.
* Upgrade to Gradle 4.7.
* Upgrade to Agrona 0.9.17.

### 1.7.10 (23 Apr 2018)
* Return `NULL_VAL` for unknown `enum` value so it can be used in a Java switch. [PR #548](https://github.com/real-logic/simple-binary-encoding/pull/548)
* Improve C++ Unit tests. [PR #459](https://github.com/real-logic/simple-binary-encoding/pull/549).
* Use since version from field rather than type in Java codecs. [PR #548](https://github.com/real-logic/simple-binary-encoding/pull/548).
* Generate put `CharSequence` methods for ASCII encoded fields in Java. [PR #547](https://github.com/real-logic/simple-binary-encoding/pull/547)
* Cleaning up unit tests' global namespaces for C++. [PR #545](https://github.com/real-logic/simple-binary-encoding/pull/545)
* Generates invalid CSharp code when using sinceVersion attribute inside groups. [PR #555](https://github.com/real-logic/simple-binary-encoding/pull/555).
* Add support for netstandard2.0 removing support for 1.3 for .NET.
* Upgrade to Shadow 2.0.3.
* Upgrade to Mockito 2.18.3.
* Upgrade to Gradle 4.6.
* Upgrade to Checkstyle 8.9.
* Upgrade to Agrona 0.9.16.

### 1.7.9 (09 Mar 2018)
* Remove redundant throws clause from Java `TargetCodeGenerator`.
* Removed the using of namespace in generated C++ headers. Issue [#544](https://github.com/real-logic/simple-binary-encoding/pull/544).
* Fix generated C# code for version checking of fields. Issue [#543](https://github.com/real-logic/simple-binary-encoding/issues/543).
* Upgrade to Agrona 0.9.15.

### 1.7.8 (23 Feb 2018)
* Advance toString() operation on Java decoders when raw bytes for var data is encountered. Issue [#542](https://github.com/real-logic/simple-binary-encoding/issues/542).
* Add C++ classes for OTF and header include to the sources jar. Issue [#540](https://github.com/real-logic/simple-binary-encoding/issues/540).
* Upgrade to Agrona 0.9.14.

### 1.7.7 (23 Feb 2018)
* Regenerate IR codecs.
* Provide `ByteOrder` as a constant available in the generated Java codecs.
* Do not allocate when putting or getting an empty string in Java codecs.
* Apply strict indentation code style.
* Upgrade to JMH 1.20.0.
* Upgrade to Gradle 4.5.1.
* Upgrade to Checkstyle 8.8.
* Reduce Hamcrest dependency from all to library.
* Upgrade to Mockito 2.15.0.
* Upgrade to Agrona 0.9.13.

### 1.7.6 (15 Dec 2017)
* Fix build of golang code due to failing test. Issue [#532](https://github.com/real-logic/simple-binary-encoding/issues/532).
* Generate getters for constant properties in Java decoders. Issue [#531](https://github.com/real-logic/simple-binary-encoding/issues/531).
* Rework C++ OTF to support unaligned access.
* Generate field accessors in C++ to use `memcpy` to support platforms requiring aligned access and X86 when the XMM registers are used. Issue [#506](https://github.com/real-logic/simple-binary-encoding/issues/506).
* Rework C++ generated code for options sets to work with Visual Studio.
* Upgrade to Mockito 2.13.0.
* Upgrade to Checkstyle 8.5.
* Upgrade to JShadow 2.0.1.
* Upgrade to Gradle 4.4.
* Upgrade to Agrona 0.9.12.

### 1.7.5 (15 Nov 2017)
* Support lookup of an enum value to be used as the value for a constant primitive type in a composite or message. Issue [#529](https://github.com/real-logic/simple-binary-encoding/issues/529).
* Take account of constant fields when computing block length. Issue [#528](https://github.com/real-logic/simple-binary-encoding/issues/528).
* Validate the type is correct in a `valueRef` attribute.
* Update to Mockito 2.12.0.
* Update to Checkstyle 8.4.
* Update to Agrona 0.9.11.

### 1.7.4 (01 Nov 2017)
* Generate Javadocs for major types in codecs based on descriptions. Simple fields to come later.
* Take the max of type and field version rather than just having the field take precedence.
* Expose a `JsonPrinter` method that takes an `UnsafeBuffer` rather than a `ByteBuffer`.
* Update to Mockito 2.11.0.

### 1.7.3 (16 Oct 2017)
* Fix the generated group name for arrays within groups for c++ codecs. [#521](https://github.com/real-logic/simple-binary-encoding/pull/521).
* Upgrade to Agrona 0.9.9.

### 1.7.2 (11 Oct 2017)
* Fix move constructors and assignment operators for c++ 11 code generation.
* Add bit twiddling methods to codecs for bitsets. Issue [#489](https://github.com/real-logic/simple-binary-encoding/issues/489).
* Remove old SBE typedefs from C++ codecs.
* Handle case of constant array of char with length of 1. Issue [#505](https://github.com/real-logic/simple-binary-encoding/issues/505).
* Capture types by `ref` name for support refs in refs. Issue[#496](https://github.com/real-logic/simple-binary-encoding/issues/496).
* Improvements to efficiency of introducing c++ 11 functionality.
* Initial support for Rust language.
* Upgrade to GSON 2.8.2.
* Upgrade to Checkstyle 8.3.
* Upgrade to Mockito 2.10.0.
* Upgrade to Agrona 0.9.8.

### 1.7.1 (21 Aug 2017)
* Add accessors for buffer length on C++ codecs. [Issue #479](https://github.com/real-logic/simple-binary-encoding/issues/479).
* Add the ability to lookup message tokens when decoding IR by template id for C++. [Issue #484](https://github.com/real-logic/simple-binary-encoding/issues/484).
* Don't dereference a null character encoding. [Issue #491](https://github.com/real-logic/simple-binary-encoding/pull/491).
* Add metadata attribute for presence. [Issue #483](https://github.com/real-logic/simple-binary-encoding/issues/483).
* Add `noexcept` specifier for C++ [PR #480](https://github.com/real-logic/simple-binary-encoding/pull/480)
* Capture referenced name when dealing with simple types for refs. [Issue #477](https://github.com/real-logic/simple-binary-encoding/issues/477).
* Handle `presence=optional` better in Golang generator. [PR #475](https://github.com/real-logic/simple-binary-encoding/pull/475).
* Validate message names and field names are unique in a given context. [Issue #470](https://github.com/real-logic/simple-binary-encoding/issues/470)
* Copy elements of correct size in C++ fix arrays. [Issue #402](https://github.com/real-logic/simple-binary-encoding/issues/402).
* Generate a field accessor as a `char` for constant Java char arrays exactly 1 in length.
* Generate a field accessor as a `String` for constant Java char arrays greater than 1 in length.
* Generate garbage free settors for Java strings and char arrays when the encoding is US_ASCII.
* Use compiler detections to guard pragmas to suppress excessive warnings. [Issue #437](https://github.com/real-logic/simple-binary-encoding/issues/437).
* Provide dotnet core support. [PR #456](https://github.com/real-logic/simple-binary-encoding/pull/456).
* Correct defaults for `UINT32` type as per specification. **Note:** this maybe breaking for those relying on hardcoded references for the values.  [Issue #455](https://github.com/real-logic/simple-binary-encoding/issues/455).
* Upgrade to Mockito 2.8.47.
* Upgrade to JMH 1.19 for Java benchmarks.
* Upgrade to sevntu-checks:1.24.1.
* Upgrade to Checkstyle 8.1.
* Upgrade to Gradle 4.1.
* Upgrade to Agrona 0.9.7.

### 1.7.0 (14 Apr 2017)
* Use compiler detections to guard pragmas to suppress excessive warnings in C++ #454
* Reinstate C# support but consider it beta.
* Fix min/max macro conflicts on Windows for C++.
* Remove `sinceVersion=1` from `deprecated` attribute in IR as the base IR has no message header.
* Support generation of var-data methods for raw data which do not have a `characterEncoding`.
* Default to `US-ASCII` rather than `UTF-8` for `char` type as per specification.
* Generate `wrapAndApplyHeader()` method to Java encoders for convenience. 
* Upgrade to Agrona 0.9.6.
* Upgrade to Checkstyle 7.7.

### 1.6.2 (12 Apr 2017)
* Upgrade to Agrona 0.9.5.
* Upgrade to Gradle 3.5.
* Upgrade to Mockito 2.7.22.

### 1.6.1 (06 Apr 2017)
* Share some test code with Golang.
* Upgrade to Checkstyle 7.6.1.
* Upgrade to Agrona 0.9.4.
* Upgrade to Gradle 3.4.1.
* Upgrade to Mockito 2.7.21.

### 1.6.0 (22 Feb 2017)
* Allow complex types to be included in the `messageHeader` for a schema.
* Fixed bug [#435](https://github.com/real-logic/simple-binary-encoding/issues/435) so that the name is correctly picked up for ref types in composites.
* Add `referencedName` to the IR so that is can be used with ref types.
* Code clean ups for C++.
* Go lang support beta complete.
* Upgrade to Agrona 0.9.3.
* Upgrade to Gradle 3.4.
* Upgrade to Mockito 2.7.9.

### 1.5.7 (08 Feb 2017)
* Make name clash with language keywords a warning rather than error. Issue [#433](https://github.com/real-logic/simple-binary-encoding/issues/433).
* Capture byte order as a field in the IR for convenience.
* Add missing accessor methods for C++ OTF `Token` and `Encoding`.
* Golang additions in preparation for beta launch.
* Upgrade to Checkstyle 7.5.1.

### 1.5.6 (23 Jan 2017)
* Fix bug with generating composites within composites.
* Fix bug with message `sinceVersion` being over written by schema version id.
* Add `deprecated` version information from the XML to the IR.
* Add Golang support to generator.
* Upgrade to Agrona 0.9.2.
* Upgrade to JMH 1.17.4.
* Upgrade to Checkstyle 7.4.
* Upgrade to sevntu-checks 1.23.0.
* Upgrade to Mockito 2.6.8.
* Upgrade to Gradle 3.3.

### 1.5.5 (20 Dec 2016)
* Correct issues with message extension on types other than basic encoded types.
* Removed unneeded fields from the Java codecs to reduce footprint.
* Capture `sinceVersion` and `description` attributes for all types in the XML schema and store in the IR.
* Expose the buffer and offset as accessor methods in the Java stubs.
* Generate encoding offset and length as static methods for fields in the codec stubs.
* Upgrade to JMH 1.17.3.
* Upgrade to Checkstyle 7.3.
* Upgrade to sevntu-checks 1.22.0.
* Upgrade to Mockito 2.2.29.
* Upgrade to Agrona 0.9.1.
* Upgrade to Gradle 3.2.1.

### 1.5.4 (17 Nov 2016)
* Added optional support for decoding unknown extended enums.
* Upgrade to JMH 1.16.
* Upgrade to sevntu-checks 1.21.1.
* Upgrade to Mockito 2.2.16.
* Upgrade to Agrona 0.9.0.
* Upgrade to Gradle 3.2.

### 1.5.3 (03 Nov 2016)
* Add String convenience accessors to Java generated stubs for char array types.
* Upgrade to Mockito 2.2.9.
* Upgrade to Agrona 0.5.6.
* Upgrade to Checkstyle 7.2.

### 1.5.2 (15 Oct 2016)
* Added checks for extension var data and repeating groups in OTF for Java and C++.
* Added `sinceVerion` and `inActingVersion` to repeating groups for C++.
* Added acting version guard to repeating groups and var data for extensions.
* Added `sinceVersion` metadata methods for all field types in Java generated stubs.
* Expose buffer and offset in fixed flyweights for C++.
* Use static constexpr for C++ constants.
* Upgrade to Agrona 0.5.5.
* Upgrade to Checkstyle 7.1.2.
* Upgrade to JMH 1.15.
* Upgrade to Gradle 3.1.

### 1.5.1 (24 Aug 2016)
* Target CMake 3.0.2 minimum.
* Correctly encode int64 min value in C++.
* Apply constexpr's where possible in C++.
* Upgrade to Agrona 0.5.4.
* Upgrade to Gradle 2.14.1.

### 1.5.0 (22 Jul 2016)
* Reference the 1.0 final SBE schema XSD.
* Generate a `toString()` method for the Java codecs stubs to help with debugging.
* Update to Agrona 0.5.3.
* Update to Checkstyle 7.0.

### 1.4.2-RC2 (29 Jun 2016)
* Added `sbe.cpp.namespaces.collapse` system property so c++ nested namespaces are collapsed to a single namespace, separated by underscores, which defaults to false.
* Move generated interfaces for flyweights to [Agrona](https://github.com/real-logic/Agrona) and allow configuration via `sbe.java.generate.interfaces` system property which defaults to false.
* Update to Agrona 0.5.2.
* Update to Checkstyle 6.19.
* Update to Gradle 2.14.

### 1.4.1-RC4 (23 May 2016)
* Update to Agrona 0.5.1.
* Update to Checkstyle 6.18.
* Update to Gradle 2.13.

### 1.4.0-RC4 (22 Apr 2016)
* Correct examples for illustration of how to do message extension.
* Update On-The-Fly decoding to assign contextual names for types within composites.
* Add feature support for the [RC4 Specification](https://github.com/FIXTradingCommunity/fix-simple-binary-encoding/tree/master/v1-0-RC4) which adds offsets and refs in composites, plus restricted ranges on repeating groups.
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
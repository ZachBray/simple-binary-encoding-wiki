### 1.20.1 (12 Sep 2020)
* Validate enum values are in the range for type considering min and max value, excluding null value.
* Upgrade to Versions 0.31.0.
* Upgrade to Checkstyle 8.36.
* Upgrade to Mockito 3.5.10.
* Upgrade to JMH 1.25.2.
* Upgrade to Agrona 1.7.1.

### 1.20.0 (29 Aug 2020)
* Improve validation and error messages when parsing invalid SBE schemas.
* Generate SBE header constants in C++ codecs so they can be used more easily in switch statements.
* Generate C++ style casts rather than C style casts in C++ codecs.
* Generate enum literals beyond `int` range correctly for C and C++. 
* Enum get methods for C++ codecs should not be `noexcept`. [PR #806](https://github.com/real-logic/simple-binary-encoding/pull/806).
* Improve javadoc.
* Update Gradle build script so IDEA can more easily get the dependencies correct for generated code.
* Upgrade to Gradle 6.6.1.
* Upgrade to Mockito 3.5.7.
* Upgrade to JMH 1.25.1.
* Upgrade to Checkstyle 8.35.
* Upgrade to Version 0.29.0.
* Upgrade to Agrona 1.7.0.

### 1.19.0 (14 Jul 2020)
* Improve edge case generation for `JsonPrinter` when groups have num in group of zero. [Issue #800](https://github.com/real-logic/simple-binary-encoding/issues/800).
* Improve edge case generation for `JsonPrinter` when messages are empty. [Issue #799](https://github.com/real-logic/simple-binary-encoding/issues/799).
* Fix case of generated variables in C++ `computeLength` method which could cause compilation errors. [Issue #796](https://github.com/real-logic/simple-binary-encoding/issues/796).
* Enable Java 14 build in CI.
* Improve Javadoc.
* Remove use of `constexpr` from `computeLength` generated methods in C++ codecs to address compilation errors. 
* Validate usage of group and var data types within composite elements when XSDs are not used.
* Improve C# samples.
* Improve C++ samples.
* Reduce warnings in C++ build on Windows.
* Upgrade to .net core 3.1 LTS for for CI.
* Upgrade to Mockito 3.4.0.
* Upgrade to Checkstyle 8.34.
* Upgrade to Shadow 6.0.0.
* Upgrade to Gradle 6.5.1.
* Upgrade to Agrona 1.6.0.

### 1.18.2 (27 May 2020)
* Fix SbeMarshalling.go file generation based on template. [Issue #789](https://github.com/real-logic/simple-binary-encoding/issues/789).
* Fix numerous MSVC warnings about C and C++ codec generation and testing.

### 1.18.1 (23 May 2020)
* Fix case of importing buffers when var data is used in nested groups for Java codecs.

### 1.18.0 (23 May 2020)
* Only generate imports for Java codecs when required to address warnings.
* Access fixed length arrays as Spans in C# codecs. [PR #780](https://github.com/real-logic/simple-binary-encoding/pull/780).
* Add `SbeSchemaId` and `SbeSchemaVersion` as constants in fixed flyweights for C# codecs.
* Generate source docs from schema descriptions for C# codecs. [PR #778](https://github.com/real-logic/simple-binary-encoding/pull/778).
* Add offset and wrap methods to C# codecs for ease of use. [PR #777](https://github.com/real-logic/simple-binary-encoding/pull/777).
* Support non-standard message headers form the C++ codecs. [PR #775](https://github.com/real-logic/simple-binary-encoding/pull/775).
* Fix version support for enums in C codecs. [Issue #773](https://github.com/real-logic/simple-binary-encoding/issues/773).
* Improve formatting of generated C codecs.
* Require a strict dependency on Agrona.
* Upgrade to Agrona 1.5.0.
* Upgrade to javadoc-links 5.1.0.
* Upgrade to JUnit 5.6.2.
* Upgrade to Gradle 6.4.1.

### 1.17.0 (28 Mar 2020)
* Remove redundant generated casts from Java codecs. [Issue #766](https://github.com/real-logic/simple-binary-encoding/issues/766).
* Add ability to compute the length of an encoded message before encoding for C++ codecs. [PR #761](https://github.com/real-logic/simple-binary-encoding/pull/761).
* Remove Rust support due to lack of community support.
* Use `sinceVersion` from field which overrides type in C# codecs. [Issue #763](https://github.com/real-logic/simple-binary-encoding/issues/763).
* Upgrade to JUnit 5.6.1.
* Upgrade to Gradle 6.3.
* Upgrade to Agrona 1.4.1.

### 1.16.3 (28 Feb 2020)
* Re-generate Java IR codecs.
* Make Java codec `toString()` methods version aware so the wrapped `actingVersion` and `actingBlockLength` are accounted for.

### 1.16.2 (27 Feb 2020)
* Variable data string improvements in C++ codecs. [PR #758](https://github.com/real-logic/simple-binary-encoding/pull/758).
* Short circuit `toString` and `appendTo` in Java codes when buffer is null to avoid debugging issues.
* CLang Tidy fixes for C++ codecs. [PR #757](https://github.com/real-logic/simple-binary-encoding/pull/757).
* Eliminate allocation when calling `appendTo` on set based fields in Java codecs. [PR #755](https://github.com/real-logic/simple-binary-encoding/pull/755).
* Support optional groups which is a non-standard feature. [Issue #754](https://github.com/real-logic/simple-binary-encoding/issues/754).
* Fix issue with populating optional arrays in Go codecs. [PR #750](https://github.com/real-logic/simple-binary-encoding/pull/750).
* Migrate to maven-publish plugin for Gradle.
* C++ codec `sbePosition()` method should be SBE_NOEXCEPT.
* Upgrade to Versions 0.28.0.
* Upgrade to Mockito 3.3.0.
* Upgrade to Agrona 1.4.0.
* Upgrade to Gradle 6.2.1.
* Upgrade to JMH 1.23.

### 1.16.1 (21 Jan 2020)
* Simplify the generation of `MetaAttribute` lookup on fields in C++ codecs to reduce code size.
* Allow setting a count for groups and then later setting it to the value of index. This allows for streaming operations when the count is not known but can be limited and then later set to index for what is encoded. Applied to Java, C++, and C# codecs. [PR #746](https://github.com/real-logic/simple-binary-encoding/pull/746).
* Fix generation of Java Decoder names when message names start with lowercase. [Issue #745](https://github.com/real-logic/simple-binary-encoding/issues/745).
* Upgrade to Agrona 1.3.0.
* Upgrade to JUnit 5.6.0.

### 1.16.0 (10 Jan 2020)
* Generate simpler `MetaAttribute` lookup in Java Codecs which avoids the generation of a synthetic class.
* Don't default timeunit and epoch for var data fields when parsing. This results in more compact IR.
* Move CI build to GitHub Actions.
* Track `initialOffset` on Java codecs so `toString()` can be called on the decoder at any time during decoding. This allows SBE decoding to be debugged.
* Use skip methods to avoid unused warning in C++ codecs.
* Upgrade to Agrona 1.2.0.
* Upgrade to Checkstyle 8.28.
* Upgrade to JUnit 5.6.0-RC1.
* Upgrade to javadoc-links 4.1.6.
* Upgrade to Mockito 3.2.4.
* Upgrade to Gradle 6.0.1.
* Upgrade to gtest 1.10.0.

### 1.15.0 (21 Nov 2019)
* Add skip methods for var data fields in C++ and Java codecs.
* Handle encoding of Nan, Infinity, and -Infinity for floating point values in Java `JsonPrinter`.
* Fix handling of `sinceVersion` in IR, Java codecs, and C++ codecs so that the max value is taken from type in field.
* Set javadoc encoding to UTF-8.
* Upgrade to javadoc-links 4.1.4.
* Upgrade to Hamcrest 2.2.
* Upgrade to Checkstyle 8.26.
* Upgrade to Shadow 5.2.0.
* Upgrade to Agrona 1.1.0.

### 1.14.1 (06 Nov 2019)
* Regenerate the Java IR Codecs to benefit for less allocation when pretty printing.
* Add null enum value lookup into the switch for Java codecs. [PR #732](https://github.com/real-logic/simple-binary-encoding/pull/732).
* Upgrade to JMH 1.22.
* Upgrade to Agrona 1.0.11.

### 1.14.0 (27 Oct 2019)
* Provide consistent API for constant char arrays to be compatible with mutable char arrays.
* Cast length type for memcpy to address warning. [PR #730](https://github.com/real-logic/simple-binary-encoding/pull/730).
* Reduce string copying to improve performance of C++ codec generators.
* Provide property accessor methods for getting string values Json escaped in C++ codecs.
* Escape Json string in C++ codec pretty printing. [Issue #718](https://github.com/real-logic/simple-binary-encoding/issues/718).
* Escape Json strings in Java `JsonTokenListener`.
* Upgrade to javadoc-links 4.1.3.
* Upgrade to Checkstyle 8.25.
* Upgrade to Gradle 5.6.3.
* Upgrade to Agrona 1.0.9.

### 1.13.3 (08 Oct 2019)
* Fix type conversion warnings in C++ codecs. [PR #725](https://github.com/real-logic/simple-binary-encoding/pull/725).
* Allow setting of raw value for bit sets in C++ codecs. [PR #724](https://github.com/real-logic/simple-binary-encoding/pull/724).
* Fix initialisation of C++ codecs samples for printing as JSON.
* Print non-printable char in C++ codecs as int. [PR #716](https://github.com/real-logic/simple-binary-encoding/pull/716).
* Fix unsigned conversation warning and use better default value for group index initialisation in C++ codecs. [Issue #714](https://github.com/real-logic/simple-binary-encoding/issues/714).
* Declare primitive accessors as `noexcept` for C++ codecs. [PR #709](https://github.com/real-logic/simple-binary-encoding/pull/709).
* Upgrade to Checkstyle 8.24.
* Upgrade to javadoc-links 4.1.2.
* Upgrade to Mockito 3.1.0.
* Upgrade to Gradle 5.6.2.
* Upgrade to Agrona 1.0.8.

### 1.13.2 (16 Aug 2019)
* Cope with optional composite values being pretty printed in Java codecs. [Issue #708](https://github.com/real-logic/simple-binary-encoding/issues/708).

### 1.13.1 (14 Aug 2019)
* Reduce allocation and copying in Java codec generator to make stub generation more efficient.
* Update C# README.
* Generate package-info.java for codec Javadoc. [Issue #703](https://github.com/real-logic/simple-binary-encoding/issues/703).
* Upgrade to javadoc-links 3.8.4.
* Upgrade to Agrona 1.0.7.

### 1.13.0 (02 Aug 2019)
* [C#] Add system property to allow squashing of namespace generation. [PR #702](https://github.com/real-logic/simple-binary-encoding/pull/702).
* [C++] Use [[nodiscard]] attribute where applicable. [PR #700](https://github.com/real-logic/simple-binary-encoding/pull/700).
* [C#] Use ThrowHelper and simplify bounds checks to improve inlining. [PR #695](https://github.com/real-logic/simple-binary-encoding/pull/695).
* [Java] Javadoc to `MetaAttribute` generation.
* Upgrade to Agrona 1.0.6.
* Upgrade to javadoc-links 3.8.1.
* Upgrade to Checkstyle 8.23.
* Upgrade to Mockito 2.30.0.
* Upgrade to Gradle 5.5.1.

### 1.12.8 (12 Jul 2019)
* More efficient approach for truncating an existing file when generating IR.
* Improve Javadoc for IR.
* More efficient implementation for appending to an `Appendable` for var-data in Java codecs.
* Use raw string literals in C++ codecs. [PR #685](https://github.com/real-logic/simple-binary-encoding/pull/688).
* Enhancements to Rust codecs. [PR #685](https://github.com/real-logic/simple-binary-encoding/pull/688).
* Generate pretty printers for encoded data inspection for C++ codecs. [PR #683](https://github.com/real-logic/simple-binary-encoding/pull/683).
* Test C++ codecs with a greater range of compilers.
* Upgrade to javadoc-links 3.7.5.
* Upgrade to Shadow 5.1.0.
* Upgrade to Checkstyle 8.22.
* Upgrade to Mockito 2.28.2.
* Upgrade to Agrona 1.0.3.

### 1.12.7 (26 May 2019)
* Truncate existing serialised IR file when generating to avoid garbage at the end when new IR is shorter. [Issue #682](https://github.com/real-logic/simple-binary-encoding/issues/682).

### 1.12.6 (25 May 2019)
* Remove the need for dimensions member in C codecs. [PR #681](https://github.com/real-logic/simple-binary-encoding/pull/681).
* Improve Rust codec generator. [PR #679](https://github.com/real-logic/simple-binary-encoding/pull/679).
* Upgrade to javadoc-links 3.6.4 to allow for offline builds.
* Upgrade to hamcrest 2.1.
* Upgrade to Agrona 1.0.1.

### 1.12.5 (03 May 2019)
* Fix warnings in generated Rust codecs. [PR #676](https://github.com/real-logic/simple-binary-encoding/pull/676).
* Generate null values for enums in Rust codecs. [PR #675](https://github.com/real-logic/simple-binary-encoding/pull/675).
* Reduce footprint of repeating group by replacing field with stack allocated object for dimensions in C++ codecs. [PR #674](https://github.com/real-logic/simple-binary-encoding/pull/674).
* Don't generate an encode method for a constant enum in Java codecs. [Issue #670](https://github.com/real-logic/simple-binary-encoding/issues/670).
* Remove redundant code for put string methods on arrays in C++ codecs. [Issue #668](https://github.com/real-logic/simple-binary-encoding/issues/668).
* Generate overload with a 2-4 arg methods for setting array values to support the likes of UUIDs and cryptographic hashes efficiently for Java and C++ codecs.
* Add missing storage class specifier for C codecs. [PR #666](https://github.com/real-logic/simple-binary-encoding/pull/666).
* Create a executable binary for C# benchmarks. [PR #664](https://github.com/real-logic/simple-binary-encoding/pull/664).
* Always return a value bit set type in C# codec. [PR #663](https://github.com/real-logic/simple-binary-encoding/pull/663).
* Add the ability to query length of var data from C# codecs. [PR #622](https://github.com/real-logic/simple-binary-encoding/pull/622).
* Provide output on failure for ctest.
* Upgrade to io.freefair.javadoc-links 3.2.1.
* Upgrade to Checkstyle 8.20.
* Upgrade to Mockito 2.27.0.
* Upgrade to Gradle 5.4.1.
* Upgrade to Agrona 1.0.0.

### 1.12.4 (28 Mar 2019)
* Reduce module warnings when using Java 11.
* Fix incremental build due to annotation processor directory clash with generated code directory.
* Various clean ups to C and C++ codecs.
* Cast Java message header flyweight returns to int if interfaces are used. [PR #635](https://github.com/real-logic/simple-binary-encoding/pull/635).
* Add a C codec generator. [PR #638](https://github.com/real-logic/simple-binary-encoding/pull/638).
* Improve C++ generated codecs to use member initialisation lists for cleaner and easier to optimise code. [PR #650](https://github.com/real-logic/simple-binary-encoding/pull/650).
* Fix Javadoc links so they use HTTPS and work with OpenJDK and Java 11.
* Upgrade Checkstyle DTD and apply checkstyle to method Javadoc.
* Upgrade to Agrona 0.9.35.
* Upgrade to Mockito 2.25.1.
* Upgrade to Gradle 5.3.1.

### 1.12.3 (10 Mar 2019)
* Include links when generating Javadoc.
* Update examples so they use relative path for XInclude.
* Set `systemId` for message schemas so that XInclude can have a relative path. [PR #649](https://github.com/real-logic/simple-binary-encoding/pull/649).
* Fix generated Java codec for optional fixed length character arrays. [PR #648](https://github.com/real-logic/simple-binary-encoding/pull/648).
* Trim strings decoded from fixed length character arrays for C++ codec. [Issue #646](https://github.com/real-logic/simple-binary-encoding/issues/646).
* Fix padding on fixed length character arrays so they are null padded in C++ codec. [Issue #646](https://github.com/real-logic/simple-binary-encoding/issues/646).
* Fix generated include files. [PR #644](https://github.com/real-logic/simple-binary-encoding/pull/644).
* Upgrade to Checkstyle 8.18.
* Upgrade to Mockito 2.25.0.
* Upgrade to Gradle 5.2.1.
* Upgrade to Shadow 5.0.0.
* Upgrade to Agrona 0.9.34.

### 1.12.2 (16 Feb 2019)
* Add `wrapAndAppyHeader` method for ease of use for C++ codecs.
* Generate `sbe.h` for C++ client to avoid namespace clash and dependencies. [Issue #626](https://github.com/real-logic/simple-binary-encoding/issues/626).
* Make C++ IR decode use params. [Issue #](https://github.com/real-logic/simple-binary-encoding/issues/643).
* Upgrade to Agrona 0.9.33.

### 1.12.1 (05 Feb 2019)
* Don't generate id methods for members of a composite. [Issue #641](https://github.com/real-logic/simple-binary-encoding/issues/641).
* Fix broken code generation that resulted from merging [PR #631](https://github.com/real-logic/simple-binary-encoding/pull/631).
* Add ability to get var data members as a `string_view` to remove copy with C++17. [Issue #550](https://github.com/real-logic/simple-binary-encoding/issues/550).
* Add `Automatic-Module-Name` to help support modular Java systems that include SBE.
* Update C++ build to be more inline with Aeron. [PR #635](https://github.com/real-logic/simple-binary-encoding/pull/635).
* Upgrade to Mockito 2.24.0.
* Upgrade to Agrona 0.9.32.

### 1.12.0 (30 Jan 2019)
* Fixes and improves to CMake build. [PR #632](https://github.com/real-logic/simple-binary-encoding/pull/632).
* Add support for getting and setting a `string_view` on char array fields when C++17. [PR #631](https://github.com/real-logic/simple-binary-encoding/pull/631).
* Simplify C++ generator and modernise for C++11. [PR #630](https://github.com/real-logic/simple-binary-encoding/pull/630).
* Allow other types than `UINT16` for message header fields but issue a warning. [PR #629](https://github.com/real-logic/simple-binary-encoding/pull/629).
* Change Golang codecs to no longer assume ordering of fields in group header. [PR #627](https://github.com/real-logic/simple-binary-encoding/pull/627). 
* Upgrade to Google Test 1.8.0.
* Upgrade to Checkstyle 8.17.
* Upgrade to Agrona 0.9.31.

### 1.11.0 (21 Jan 2019)
* Better handing of null passed for string encoding to Java codecs.
* Provide a fluent API for encoding array elements in Java and C++ codecs.
* Add support for XInclude so common definitions can be included into a schema and update samples to reflect usage. [Issue #624](https://github.com/real-logic/simple-binary-encoding/issues/624).
* Upgrade to Shadow 4.0.4.
* Upgrade to Agrona 0.9.30.

### 1.10.2 (06 Jan 2019)
* Only generate lower bounds check in C++ codecs when necessary to add compile warning.
* Improve C++ OTF code to be more strict and eliminate build warnings.
* Improve build for optional build and include JAR dependency. [PR #623](https://github.com/real-logic/simple-binary-encoding/pull/623).
* Upgrade to Checkstyle 8.15.
* Upgrade to Agrona 0.9.29.

### 1.10.1 (16 Dec 2018)
* Warnings clean up in code base.
* Add OS X to Travis for native build.
* Fix golang generated codec for metadata attributes. [Issue #620](https://github.com/real-logic/simple-binary-encoding/issues/620).
* Add `Span` support to generated C# codecs. [PR #589](https://github.com/real-logic/simple-binary-encoding/pull/589).
* Upgrade to Checkstyle 8.15.
* Upgrade to Gradle 4.10.3.
* Upgrade to Agrona 0.9.28.

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
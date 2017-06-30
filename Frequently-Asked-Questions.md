**1. Why can't I randomly access fields within a message?**

One of the [design principles](Design-Principles) for SBE is to stream through messages in a ascending sequential access pattern for performance reasons.

**2. What are the targets for C++?**

There are a number of ant targets for C++ that use cpptask for compilation. The ant `cpp:test` target compiles and run tests on Linux (Ubuntu 14.10) fine. There is some compiler warns on the generated code about signed/unsigned compares. Those are OK for now and will be cleaned up. Here are the various targets related to `cpp`.

* `cpp`: compile OTF files, tests, run tests, generate Doxygen doc, and package doc into zip
 * `cpp:test`: compile cpp OTF files, compile Google Test files, run unit tests for OTF decoder
 * `cpp:doc`: generate Doxygen doc for OTF decoder
 * `cpp:dist`: generate Doxygen doc for OTF decoder and package into zip

* `examples:cpp`: compiles and runs generated and OTF decoder examples
 * `cpp:examples:gen`: compile and run cpp example
 * `cpp:examples:otf`: compile and run cpp OTF decoder example

NOTE: Doxygen is required. If you don't have it, the `cpp` target will not succeed. But `cpp:test` should still work.

**3. What if a 3rd party schema contains names that are language keywords?**

Some 3rd parties have not considered that the names they use could cause a clash with language keywords such as Java `final`. Generated property names that clash with keywords can have a string appended, such as the `_` character, by setting the `aeron.keyword.append.token` system property when running the `SbeTool`. The `templates_FixBinary.xml` from the CME is such an example.
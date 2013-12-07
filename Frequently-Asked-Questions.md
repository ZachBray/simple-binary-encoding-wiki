**1. Why cannot I randomly access fields within a message?**

One of the [design principles](Design-Principles) for SBE is to stream through messages in a ascending sequential access pattern for performance reasons.

**2. What are the `ant` targets for C++?**

There are a number of ant targets for C++ that use cpptask for compilation. The ant `cpp:test` target compiles and run tests on Linux (Ubuntu 13.10) fine. There is some compiler warns on the generated code about signed/unsigned compares. Those are OK for now and will be cleaned up. Here are the various targets related to `cpp99`.

* `cpp`: compile OTF files, tests, run tests, generate Doxygen doc, and package doc into zip
 * `cpp:test`: compile cpp99 OTF files, compile Google Test files, run unit tests for OTF decoder
 * `cpp:doc`: generate Doxygen doc for OTF decoder
 * `cpp:dist`: generate Doxygen doc for OTF decoder and package into zip

* `cpp:examples`: compiles and runs generated and OTF decoder examples
 * `cpp:examples:gen`: compile and run cpp99 example
 * `cpp:examples:otf`: compile and run cpp99 OTF decoder example

NOTE: Doxygen is required. If you don't have it, the `cpp` target will not succeed. But `cpp:test` should still work.

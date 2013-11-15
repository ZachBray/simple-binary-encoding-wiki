Welcome to the simple-binary-encoding wiki. Here you will find details of how to use the SBE (Simple Binary Encoding) tool for encoding and decoding SBE messages in binary format based on schemas. Messages are the units of information which get composed via protocols for communication between systems. The systems communicating may be the same system which is communicating with itself by persisting a message from the past for future use.

The SBE tool can be used as a library enabling on-the-fly decoding of messages, or for ultimate performance it can generate compilable stubs to directly encode or decode messages.

### Contents

1. [History of SBE](wiki/Home#History-of-SBE)
1. [Why Low-Latency?](wiki/Why-Low-Latency)
1. [Principles behind the design of the SBE Codecs](wiki/Design-Principles)
1. [How to use the SBE Tool](wiki/Sbe-Tool-Guide)
1. [Java Users Guide](wiki/Java-Users-Guide)
1. [C++99 Users Guide](wiki/Cpp99-Users-Guide)
1. [Frequently Asked Questions](wiki/Frequently-Asked-Questions)

### History of SBE

SBE originates from the _"High Performance Work Group"_ within the [FIX Protocol Limited (FPL)](http://old.fixprotocol.org/) organisation. The goal was to produce an binary encoding representation suitable for low-latency financial trading. The statement below is from the working group at the outset.

> Date	2013-06-03 13:21

> Name	FPL Program Office

> Subject	Simple Binary Encoding specification Release Candidate 1

> This proposal from the High Performance Working Group entails the use of an FPL designed Simple Binary Encoding to produce fast and compact encodings of FIX messages. Simple Binary Encoding (SBE) provides different characteristics than other binary encodings. It is optimized for low latency. This new FPL binary encoding complements the existing only binary encoding developed in 2005 (FAST) with a focus on reducing bandwidth utilization for market data. In addition, the encoding is also defined and controlled within FPL only in contrast to the binary encodings proposals to encode FIX with Google Protocol Buffers and ASN.1. 

Further details of the discussions behind SBE can be found [here](http://www.fixtradingcommunity.org/pg/discussions/topicpost/168327/).
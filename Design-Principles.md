To achieve low-latency with minimal variance it is important to define a set of design principles that guide the development. When a trade off must be made then the design principles help guide the decision making between alternate implementations.

Many design principles come to bear on any implementation. Not all design principles require trade-offs. The following set of design principles are key to the design of SBE and the likely to drive most trade-offs.

### Intermediate Copy Free

Networks and Storage systems deal with buffers in which the messages are encoded and decoded. The principle of copy free is to not employ any intermediate buffers for the encoding or decoding of messages. If an intermediate buffer is employed then the costs escale due to the copying of bytes multiple times.

SBE codecs take the approach of encoding and decoding directly from the underlying buffer. This has the limitation that messages greater in size than the transfer buffers are not directly supported. For messages larger than the transfer buffer size then a fragmentation protocol is required to fragment and reassemble large messages.

### Allocation Free

constant memory footprint

### Streaming Access



### Native Type Mapping





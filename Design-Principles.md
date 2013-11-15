To achieve low-latency with minimal variance it is important to define a set of design principles that guide the development. When a trade off must be made then the design principles help guide the decision in choosing between competing alternatives.

The results of applying the following design principles can be seen in the [benchmark results](https://github.com/real-logic/message-codec-bench).

Many design principles come to bear on any implementation. Not all design principles require trade-offs but many do. The following set of design principles are key to the design of SBE and the likely to drive most trade-offs.

1. [Copy-Free](Design-Principles#Copy-Free)
1. [Native Type Mapping](Design-Principles#Native-Type-Mapping)
1. [Allocation-Free](Design-Principles#Allocation-Free)
1. [Streaming Access](Design-Principles#Streaming-Access)
1. [Word Aligned Access](Design-Principles#Word-Aligned-Access)
1. [Backwards Compatibility](Design-Principles#Backwards-Compatibility)

### Copy-Free

Networks and Storage systems deal with buffers in which the messages are encoded and decoded. The principle of copy-free is to not employ any intermediate buffers for the encoding or decoding of messages. If an intermediate buffer is employed then the costs escale due to the copying of bytes multiple times.

SBE codecs take the approach of encoding and decoding directly to/from the underlying buffer. This has the limitation that messages greater in size than the transfer buffers are not directly supported. For messages larger than the transfer buffer size then a fragmentation protocol is required to fragment and reassemble large messages.

### Native Type Mapping

A copy-free design is greatly benefited by encoding the data as native types in the underlying buffer. For example, a 64-bit integer can be encoded directly to the underlying buffer as a single x86_64 MOV assembly instruction. If the [byte order](http://www.ietf.org/rfc/ien/ien137.txt) of the types is defined to be that of another CPU architecture then the bytes can be swapped in a register before being stored to the underlying buffer by the x86 BSWAP instruction.

By taking a native mapping approach the fields can be accessed for a similar cost to class or struct fields in a high-level language such as C++ or Java.

### Allocation-Free

The allocation of objects can result in CPU cache churn which reduces efficiency. These allocated objects then have to be collected and deleted. For Java the collection is done by the garbage collector which typically has to do this by a stop-the-world pause that happens frequently but with varying duration, thus creating variance. C++ is better but still has issues when memory is returned to central pools that may employ locks that introduce cost and latency variance.

The design of SBE codecs are allocation-free by employing the flyweight pattern. The flyweight windows over the underlying buffer for direct encoding and decoding of messages. The flyweight of the appropriate type is selected based on the message header template id field. If fields form the message need to be retained beyond the scope of processing a message then they must be stored separately.

### Streaming Access

Modern memory sub-systems have become evermore complex. The [patterns of access](http://mechanical-sympathy.blogspot.co.uk/2012/08/memory-access-patterns-are-important.html) an algorithm makes to memory can greatly dictate performance and consistency. The best performance, and most consistent latency, is gained by taking a streaming based approach that addresses memory in a ascending sequential access pattern.

The SBE codecs are designed to encode and decode messages based on a forward progression of the position in the underlying buffer. It is possible to backtrack to a degree within messages but this is highly discouraged from a performance and latency perspective.

### Word Aligned Access

Many CPU architectures exhibit significant performance issues when words are accessed on non word sized boundaries. That is the starting address of a word should be a multiple of its size in bytes. 64-bit integers should only begin on byte address divided by 8, 32-bit integers should only begin on byte addresses divided by 4, and so on.

SBE schemas support the concept of an offset that defines the starting position of a field within a message. It is assumed the messages are encapsulated within a framing protocol on 8 byte boundaries. To achieve compact and efficient messages the fields should be sorted in order by type and descending size.

### Backwards Compatibility

In a large enterprise, or across enterprises, it is not always possible to upgrade all systems at the same time. For communication to continue working the message formats have to be backwards compatible, i.e. an older system should be able to read a newer version of the same message and vice versa. 

An extension mechanism is designed into SBE which allows for the introduction of new optional fields within a message that the new systems can use while the older systems ignore them until upgrade. If new mandatory fields are required or a fundamental structural change is required then a message type must be employed because it is no longer a semantic extension of an existing message type.

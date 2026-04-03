# 05 - NIO & High-Performance I/O

For high-throughput systems (like web servers, trading algorithms), traditional blocking I/O (`java.io`) becomes a bottleneck.

---

## 🏗 Java IO vs NIO
- **Standard I/O (`java.io`)**: Stream-oriented, Blocking APIs. A thread sits idle waiting for disk/network reads.
- **Java NIO (`java.nio`)**: Buffer-oriented, Non-Blocking APIs. A single thread can manage thousands of connections via Selectors.

### Core Concepts of NIO
1. **Buffers**: Data is read into a buffer and written from a buffer (e.g., `ByteBuffer`).
2. **Channels**: A bi-directional pathway (e.g., `FileChannel`, `SocketChannel`).
3. **Selectors**: A multiplexor. A single thread checks the Selector, which tracks multiple Channels to see which ones are ready for reading/writing.

## 🏗 ByteBuffer Internals
A `ByteBuffer` has three critical properties:
- `capacity`: Total size.
- `position`: Current read/write index.
- `limit`: The boundary.
**Important**: You must call `flip()` to switch a buffer from write mode to read mode.

## 🏗 Memory-Mapped Files (Zero Copy)
For extreme performance, use `MappedByteBuffer`.
- It maps the file directly into the OS's virtual memory space.
- Bypasses the JVM Heap entirely (reducing GC load).
- Perfect for reading/writing very large files (like Kafka logs or databases).

```java
try (FileChannel channel = FileChannel.open(path, StandardOpenOption.READ)) {
    // Map a 1GB file directly to memory
    MappedByteBuffer buffer = channel.map(FileChannel.MapMode.READ_ONLY, 0, channel.size());
    // ... read directly from buffer extremely fast
}
```

### Interview Question
*What is "Zero-Copy" in Java Networking?*
Zero-Copy (`FileChannel.transferTo()`) allows transferring data from disk to the network socket directly via the OS kernel space, bypassing the JVM application memory (user space). This saves CPU cycles and memory bandwidth.

---
[⬅ Back to Interview Roadmap](../README.md)

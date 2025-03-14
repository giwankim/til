# Java I/O

## API

Java I/O is a set of classes that give access to external resources including file systems and the network.

### Main Concepts

#### Accessing a File

There are two main concepts in Java I/O: locating the resource you need to access, and opening a stream to this resource.

There are two ways to access files: the first one uses the `File` class and second one uses the `Path` interface.

The `File` class was introduced in Java SE 1.0: it represents the legacy way to access files. You can see this class as a wrapper on a string of characters representing a path on your default file system.

Starting with Java SE 7, the `Path` interface was introduced, as part of the Java NIO2 API. The role of this interface is to fix several drawbacks that the `File` class has:

- Many methods of the `File` class do not throw exceptions when they fail, making it impossible to obtain useful error message.
- The rename method doesn't work consistently across platforms.
- There is no real support for symbolic links.
- More support for metadata is desired, such as file permission, file owner, and other security attributes.
- Accessing file metadata is inefficient.
- Many of the `File` methods do not scale.
- It is not possible to write reliable code that can recursively walk a file tree and respond appropriately if there are circular symbolic links.

All these issues are fixed by the `Path` interface. So using the `File` class is not recommended anymore.

#### I/O Streams

An _I/O Stream_ represent an input source and output destination.

All streams present the same simple model to programs that use them: a stream is a sequence of data.

Java I/O API defines two kinds of content for a resource:

- character content, think of a text file, a XML or JSON document,
- and byte content, think of an image or a video.

It also defines two operations on this content: reading and writing.

Following this, the Java I/O API defines four base classes, that are abstract, each modeling a type of I/O stream and a specific operation.

|   | Reading | Writing |
| - | ------- | ------- |
| Stream of character | `Reader` | `Writer` |
| Streams of bytes | `InputStream` | `OutputStream` |

All byte streams are descended from `InputStream` and `OutputStream`.

All character stream classes are descended from `Reader` and `Writer`.

# Common I/O Tasks in Modern Java

- Reading and writing text files
- Reading text, images, JSON from the web
- Visiting files in a directory
- Reading a ZIP file
- Creating a temporary file or directory

Focus on API improvements since Java 8. In particular:

- UTF-8 is the default for I/O since Java 18 (since [JEP 400: UTF-8 by Default](https://openjdk.org/jeps/400))
- `java.nio.file.Files` class, which first appeared in Java 7, added useful methods in Java 8, 11, and 12
- `java.io.InputStream` gained useful methods in Java 9, 11, and 12
- `java.io.File` and `java.io.BufferedReader` classes are now thoroughly obsolete, even though they appear frequently in web searches and AI chats.

## Reading from an Input Stream

If you need to set request headers or read response headers, use the [HttpClient](https://docs.oracle.com/en/java/javase/23/docs/api/java.net.http/java/net/http/HttpClient.html):

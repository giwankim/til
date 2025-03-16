

## 2. File System Basics

### 2.1 Accessing Resources using Paths

#### 2.1.1 Legacy `File` Class

There are two ways to model a file or a path on a file system in Java. `File` class is mentioned here, with a word of warning. You should favor the use of the `Path` interface, along with the factory method from the `Files`.

An instance of the `File` class can represent anything on a file system: a file, a directory, a symbolic link, a relative path, or an absolute path.

An instance of a `File` does not allow you to access the content of the file it represents. With this instance, you can check if this file exists or is readable (among other things).

#### 2.1.2 `Path` Interface

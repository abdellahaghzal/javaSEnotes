# Chapter 14: I/O

## Path Symbols (Table 14.1)
- `.` — A reference to the current directory
- `..` — A reference to the parent of the current directory

## Common Causes of Methods Throwing IOException
- Loss of communication to the underlying file system
- File or directory exists but cannot be accessed or modified
- File exists but cannot be overwritten
- File or directory is required but does not exist

## Common NIO.2 Method Arguments (Table 14.4)
- **LinkOption.NOFOLLOW_LINKS** — Do not follow symbolic links
- **StandardCopyOption.ATOMIC_MOVE** — Move file as atomic file system operation
- **StandardCopyOption.COPY_ATTRIBUTES** — Copy existing attributes to new file
- **StandardCopyOption.REPLACE_EXISTING** — Overwrite file if it already exists
- **StandardOpenOption.APPEND** — If file is already open for write, append to the end
- **StandardOpenOption.CREATE** — Create new file if it does not exist
- **StandardOpenOption.CREATE_NEW** — Create new file only if it does not exist; fail otherwise
- **StandardOpenOption.READ** — Open for read access
- **StandardOpenOption.TRUNCATE_EXISTING** — If file is already open for write, erase file and append to beginning
- **StandardOpenOption.WRITE** — Open for write access
- **FileVisitOption.FOLLOW_LINKS** — Follow symbolic links

## Differences Between Byte and Character I/O Streams
- Byte I/O streams read/write binary data (0s and 1s) and have class names that end in `InputStream` or `OutputStream`
- Character I/O streams read/write text data and have class names that end in `Reader` or `Writer`

## How to Make a Class Serializable
- The class must be marked `Serializable`
- Every instance member of the class must be serializable, marked `transient`, or have a null value at the time of serialization

## Selecting a Search Strategy (Directory Traversal)
- **Depth-first search** — traverses the structure from the root to an arbitrary leaf and then navigates back up toward the root, traversing fully any paths it skipped along the way
- **Breadth-first search** — starts at the root and processes all elements of each particular depth before proceeding to the next depth level

## Key APIs (Table 14.13)
- **File** — I/O representation of location in file system
- **Files** — Helper methods for working with Path
- **Path** — NIO.2 representation of location in file system
- **Paths** — Contains factory methods to get Path
- **InputStream** — Superclass for reading files based on bytes
- **OutputStream** — Superclass for writing files based on bytes
- **Reader** — Superclass for reading files based on characters
- **Writer** — Superclass for writing files based on characters

## Summary — Key Distinctions Between I/O Streams
- Byte versus character streams
- Input versus output streams
- Low-level versus high-level streams

## Exam Essentials
- **Understand files and directories.** Files are records that store data within a persistent storage device, such as a hard disk drive, that is available after the application has finished executing. Files are organized within a file system in directories, which in turn may contain other directories. The root directory is the topmost directory in a file system.
- **Be able to use File and Path.** An I/O File instance is created by calling the constructor. It contains a number of instance methods for creating and manipulating a file or directory. An NIO.2 Path instance is an immutable object that is created from the factory method `Path.of()`. The Path interface includes many instance methods for reading and manipulating the path value.
- **Distinguish between types of I/O streams.** I/O streams are categorized by byte/character, input/output, and low-level/high-level. Byte streams operate on binary data and have names that end with Stream, while character streams operate on text data and have names that end in Reader or Writer. The InputStream and Reader classes are the topmost abstract classes that receive data, while the OutputStream and Writer classes are the topmost abstract classes that send data. A low-level stream is one that operates directly on the underlying resource, such as a file or network connection. A high-level stream operates on a low-level or other high-level stream to filter data, convert data, or improve performance.
- **Understand how to use Java serialization.** A class is considered serializable if it implements the `java.io.Serializable` interface and contains instance members that are either serializable or marked transient. All Java primitives and the String class are serializable. The ObjectInputStream and ObjectOutputStream classes can be used to read and write a Serializable object from and to an I/O stream, respectively.
- **Be able to interact with the user.** Be able to interact with the user using the system streams (System.out, System.err, and System.in) as well as the Console class. The Console class includes special methods for formatting data and retrieving complex input such as passwords.
- **Manage file attributes.** The NIO.2 Files class includes many methods for reading single file attributes, such as its size or whether it is a directory, a symbolic link, hidden, etc. NIO.2 also supports reading all of the attributes in a single call. An attribute type is used to support operating system–specific views. Finally, NIO.2 supports updatable views for modifying selected attributes.

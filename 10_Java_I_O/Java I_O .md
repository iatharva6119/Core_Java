# Java I/O

# Java I/O and Serialization — Interview Notes

## 1. What is Java I/O?

**I/O (Input/Output)** is the mechanism Java provides for reading data from a source and writing data to a destination.

Examples of sources:

```
Keyboard
File
Network
Memory
```

Examples of destinations:

```
Console
File
Network
Memory
```

Basic flow:

```
Input:
Source → Java Program

Output:
Java Program → Destination
```

Java provides two major families:

```
Java I/O
   |
   ├── Byte Streams
   │     ├── InputStream
   │     └── OutputStream
   │
   └── Character Streams
         ├── Reader
         └── Writer
```

---

## 2. InputStream

`InputStream` is the abstract base class for **reading raw bytes** from an input source.

Package:

```
java.io
```

Hierarchy:

```
InputStream
   ├── FileInputStream
   ├── ByteArrayInputStream
   ├── BufferedInputStream
   └── ...
```

Important methods:

```
read()
read(byte[] b)
read(byte[] b,int off,int len)
close()
```

Example:

```java
InputStream input = System.in;
int data = input.read();
```

`read()` returns:

- `0–255` → byte value
- `1` → end of stream

Important:

> `InputStream` is designed for byte-oriented input, not character-oriented text processing.
> 

---

## 3. OutputStream

`OutputStream` is the abstract base class for **writing raw bytes** to an output destination.

Hierarchy:

```
OutputStream
   ├── FileOutputStream
   ├── ByteArrayOutputStream
   ├── BufferedOutputStream
   └── ...
```

Important methods:

```
write(int b)
write(byte[] b)
write(byte[] b,int off,int len)
flush()
close()
```

Example:

```java
OutputStream output = System.out;
output.write(65);
```

Output:

```
A
```

Because `65` represents the byte value for `A` in ASCII/UTF-8-compatible contexts.

---

## 4. Reader

`Reader` is the abstract base class for **reading character data**.

It is designed for text rather than raw binary data.

Hierarchy:

```
Reader
   ├── FileReader
   ├── BufferedReader
   ├── InputStreamReader
   └── ...
```

Important methods:

```
read()
read(char[] cbuf)
read(char[] cbuf,int off,int len)
close()
```

Example:

```java
Reader reader = new FileReader("data.txt");
int ch = reader.read();
```

Unlike `InputStream`, `Reader` works with **characters**.

---

## 5. Writer

`Writer` is the abstract base class for **writing character data**.

Hierarchy:

```
Writer
   ├── FileWriter
   ├── BufferedWriter
   ├── OutputStreamWriter
   └── ...
```

Important methods:

```
write()
append()
flush()
close()
```

Example:

```java
Writer writer = new FileWriter("data.txt");
writer.write("Hello Java");
writer.close();
```

---

## 6. InputStream vs Reader

Very common interview question.

| InputStream | Reader |
| --- | --- |
| Byte-oriented | Character-oriented |
| Reads bytes | Reads characters |
| Suitable for binary data | Suitable for text |
| Base class for byte input | Base class for character input |
| Example: `FileInputStream` | Example: `FileReader` |

Use:

```
Images/PDFs/audio/etc. → InputStream
Text files             → Reader
```

---

## 7. OutputStream vs Writer

| OutputStream | Writer |
| --- | --- |
| Byte-oriented | Character-oriented |
| Writes bytes | Writes characters |
| Suitable for binary data | Suitable for text |
| Example: `FileOutputStream` | Example: `FileWriter` |

Simple rule:

```
Binary → Stream
Text   → Reader/Writer
```

---

## 8. File

`File` represents a **file or directory pathname**.

Package:

```
java.io.File
```

Example:

```java
File file = new File("data.txt");
```

Important:

> `File` represents a filesystem path and provides operations/metadata access; it is not itself a stream for reading or writing file contents.
> 

Useful methods:

```
exists()
createNewFile()
delete()
mkdir()
mkdirs()
getName()
getPath()
getAbsolutePath()
isFile()
isDirectory()
length()
```

Example:

```java
File file = new File("data.txt");

if (file.exists()) {
    System.out.println(file.length());
}
```

Modern Java also provides the **NIO.2** APIs (`Path`, `Files`) for many filesystem operations, but `File` remains important for interviews and legacy code.

---

## 9. FileInputStream

`FileInputStream` is used to **read raw bytes from a file**.

Hierarchy:

```
InputStream
     ↓
FileInputStream
```

Example:

```java
FileInputStream fis = new FileInputStream("data.txt");

int data;

while ((data = fis.read()) != -1) {
    System.out.print((char) data);
}

fis.close();
```

For every `read()`:

```
File → FileInputStream → Java Program
```

It is suitable for:

- Binary files
- Images
- PDFs
- Audio
- Raw byte data

For large files, reading one byte at a time is inefficient; buffering is generally preferable.

---

## 10. FileOutputStream

`FileOutputStream` is used to **write raw bytes to a file**.

Hierarchy:

```
OutputStream
     ↓
FileOutputStream
```

Example:

```java
FileOutputStream fos = new FileOutputStream("data.txt");

fos.write(65);
fos.write(66);
fos.write(67);

fos.close();
```

Output:

```
ABC
```

By default, opening a `FileOutputStream` this way generally **overwrites** an existing file.

To append:

```java
FileOutputStream fos =
    new FileOutputStream("data.txt", true);
```

The `true` means append mode.

---

## 11. BufferedReader

`BufferedReader` reads characters efficiently by using an **internal character buffer**.

Hierarchy:

```
Reader
   ↓
BufferedReader
```

Example:

```java
BufferedReader br =
    new BufferedReader(new FileReader("data.txt"));

String line;

while ((line = br.readLine()) != null) {
    System.out.println(line);
}

br.close();
```

The major advantage is:

```
readLine()
```

which reads an entire line.

It also reduces the number of underlying I/O operations by buffering input.

Common methods:

```
read()
readLine()
ready()
close()
```

---

## 12. BufferedWriter

`BufferedWriter` writes character data using an **internal buffer**.

Hierarchy:

```
Writer
   ↓
BufferedWriter
```

Example:

```java
BufferedWriter bw =
    new BufferedWriter(new FileWriter("data.txt"));

bw.write("Hello Java");
bw.newLine();
bw.write("Buffered I/O");

bw.close();
```

Important method:

```
newLine()
```

Advantages:

- More efficient character output
- Reduces direct I/O operations
- Provides convenient `newLine()`

---

## 13. Scanner

`Scanner` is a utility class used to read and parse input into different data types.

Package:

```
java.util.Scanner
```

It can read from:

- Keyboard
- Files
- Strings
- Other readable sources

Example:

```java
Scanner sc = new Scanner(System.in);

int age = sc.nextInt();
String name = sc.next();
```

It supports methods such as:

```
nextInt()
nextDouble()
next()
nextLine()
hasNext()
hasNextInt()
```

Example:

```java
Scanner sc = new Scanner(System.in);

System.out.print("Enter age: ");
int age = sc.nextInt();

System.out.print("Enter name: ");
String name = sc.next();
```

---

## 14. Scanner vs BufferedReader

Very common interview question.

| Scanner | BufferedReader |
| --- | --- |
| `java.util` | `java.io` |
| Parses primitive types directly | Primarily reads characters/text |
| `nextInt()`, `nextDouble()` etc. | `readLine()` |
| Convenient | Generally faster for raw text input |
| Uses parsing/tokenization | Buffered character input |
| Common for simple console programs | Common for efficient text input |

Example:

```java
Scanner sc = new Scanner(System.in);
int n = sc.nextInt();
```

With `BufferedReader`:

```java
BufferedReader br =
    new BufferedReader(new InputStreamReader(System.in));

int n = Integer.parseInt(br.readLine());
```

For placement coding problems, `BufferedReader` is often preferred when input size is large.

---

## 15. Important Java I/O Hierarchy

You should remember this structure:

```
                  Java I/O
                    |
           ┌─────────┴─────────┐
           ↓                   ↓
      Byte Streams        Character Streams
           |                   |
     InputStream             Reader
           |                   |
    FileInputStream      BufferedReader
    BufferedInputStream  FileReader
           |
     OutputStream
           |
    FileOutputStream
    BufferedOutputStream
           |
          Writer
           |
    BufferedWriter
    FileWriter
```

The core rule to remember:

```
InputStream / OutputStream
        ↓
      BYTES
        ↓
   Binary data
```

```
Reader / Writer
        ↓
    CHARACTERS
        ↓
     Text data
```

And:

```
FileInputStream
→ reads bytes from file

FileOutputStream
→ writes bytes to file

BufferedReader
→ efficiently reads characters/lines

BufferedWriter
→ efficiently writes characters

Scanner
→ reads + parses input conveniently
```

---

# Modern Java I/O — `java.nio`

## 16. What is `java.nio`?

`java.nio` stands for **New I/O**. It provides a more modern and flexible API for handling files, paths, buffers, channels, and other I/O operations.

For file-system operations, the most important classes are:

```
java.nio
   |
   └── file
        ├── Path
        ├── Paths
        └── Files
```

Compared with older `java.io.File`:

```
Old I/O:
File
FileInputStream
FileOutputStream
Reader
Writer

Modern I/O:
Path
Files
```

For modern Java applications, `Path` + `Files` is generally preferred for filesystem operations.

---

## 17. What is `Path`?

`Path` represents the **location/path of a file or directory** in the filesystem.

Package:

```
java.nio.file.Path
```

Example:

```java
Path path = Path.of("data.txt");
```

It represents:

```
data.txt
```

It does **not** itself read or write the file.

You use `Files` to perform operations on the `Path`.

```java
Path path = Path.of("data.txt");

String content = Files.readString(path);
```

Useful methods:

```
getFileName()
getParent()
getRoot()
toAbsolutePath()
resolve()
normalize()
```

Example:

```java
Path path = Path.of("data", "students.txt");

System.out.println(path.getFileName());
```

Output:

```
students.txt
```

Important:

> `Path` represents a filesystem path; `Files` provides operations on that path.
> 

---

## 18. What is `Paths`?

`Paths` is a utility class used to create `Path` objects.

Package:

```
java.nio.file.Paths
```

Example:

```java
Path path = Paths.get("data.txt");
```

Multiple path components:

```java
Path path =
    Paths.get("data", "students", "data.txt");
```

Conceptually:

```
Paths.get(...)
       ↓
     Path
       ↓
     Files.*
```

However, in modern Java, you will often see:

```java
Path path = Path.of("data.txt");
```

instead of:

```java
Path path = Paths.get("data.txt");
```

`Path.of()` was introduced in **Java 11**.

Important interview point:

> `Paths` is mainly a factory utility for creating `Path` objects; `Path.of()` is the modern alternative.
> 

---

## 19. What is `Files`?

`Files` is a utility class containing static methods for performing filesystem operations.

Package:

```
java.nio.file.Files
```

It works with `Path`.

Example:

```java
Path path = Path.of("data.txt");

if (Files.exists(path)) {
    System.out.println("File exists");
}
```

Common methods:

```
exists()
createFile()
createDirectory()
delete()
copy()
move()
readString()
writeString()
readAllLines()
readAllBytes()
write()
```

Example:

```java
Path source = Path.of("source.txt");
Path target = Path.of("target.txt");

Files.copy(source, target);
```

The relationship is:

```
Path
 ↓
represents location

Files
 ↓
performs operation
```

---

## 20. `Files.readString()`

`Files.readString()` reads the **entire contents of a file into a `String`**.

Available since **Java 11**.

Example:

```java
Path path = Path.of("data.txt");

String content = Files.readString(path);

System.out.println(content);
```

Conceptually:

```
data.txt
   ↓
Files.readString()
   ↓
String
```

By default, it uses **UTF-8**.

You can also specify a charset:

```java
String content =
    Files.readString(path, StandardCharsets.UTF_8);
```

It throws `IOException`, so it must be handled or declared.

Example:

```java
try {
    String content =
        Files.readString(Path.of("data.txt"));

    System.out.println(content);

} catch (IOException e) {
    e.printStackTrace();
}
```

Important:

> `readString()` is convenient for files whose entire contents can reasonably be held in memory. It is not the right choice for arbitrarily large files.
> 

---

## 21. `Files.writeString()`

`Files.writeString()` writes a `String` to a file.

Available since **Java 11**.

Example:

```java
Path path = Path.of("data.txt");

Files.writeString(path, "Hello Java");
```

Conceptually:

```
String
  ↓
Files.writeString()
  ↓
data.txt
```

It throws `IOException`.

Example:

```java
try {
    Files.writeString(
        Path.of("data.txt"),
        "Hello Java"
    );

} catch (IOException e) {
    e.printStackTrace();
}
```

By default, if the file already exists, its content is **truncated and replaced**.

---

## 22. `Files.writeString()` Append Mode

You can use `StandardOpenOption.APPEND` to append instead of replacing the existing content.

```java
Files.writeString(
    Path.of("data.txt"),
    "\nNew line",
    StandardOpenOption.APPEND
);
```

You can also combine options:

```java
Files.writeString(
    path,
    "Hello",
    StandardOpenOption.CREATE,
    StandardOpenOption.APPEND
);
```

This means:

```
CREATE
  ↓
Create file if necessary

APPEND
  ↓
Add content at the end
```

---

## 23. `Path` + `Files` vs `File`

This is an important interview comparison.

| Old I/O | Modern NIO |
| --- | --- |
| `File` | `Path` |
| `FileInputStream` | `Files.newInputStream()` |
| `FileOutputStream` | `Files.newOutputStream()` |
| `FileReader` | `Files.newBufferedReader()` |
| `FileWriter` | `Files.newBufferedWriter()` |
| Older API | More modern filesystem API |
| Limited filesystem operations | Richer filesystem functionality |

Example:

Old approach:

```java
File file = new File("data.txt");
```

Modern approach:

```java
Path path = Path.of("data.txt");
```

Then:

```java
Files.readString(path);
Files.writeString(path, "Hello");
```

---

## 24. Simple Modern I/O Example

Reading:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class Demo {

    public static void main(String[] args)
            throws IOException {

        Path path = Path.of("data.txt");

        String content = Files.readString(path);

        System.out.println(content);
    }
}
```

Writing:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class Demo {

    public static void main(String[] args)
            throws IOException {

        Path path = Path.of("data.txt");

        Files.writeString(
            path,
            "Hello Java"
        );
    }
}
```

The modern pattern is therefore:

```
Path
 ↓
Files
 ↓
read / write / copy / move / delete
```

### Final Interview Cheat Sheet

```
java.nio.file
     |
     ├── Path
     │    → represents file/directory path
     │
     ├── Paths
     │    → utility for creating Path
     │
     └── Files
          → performs filesystem operations
```

Most important:

```java
Path path = Path.of("data.txt");

String data = Files.readString(path);

Files.writeString(path, "Hello");
```

Remember:

- **`Path`** → represents a path.
- **`Paths`** → legacy-style factory utility for creating `Path`.
- **`Files`** → performs filesystem operations.
- **`Files.readString()`** → reads the entire file into a `String`.
- **`Files.writeString()`** → writes a `String` to a file.
- `readString()` and `writeString()` were introduced in **Java 11**.
- For large files, prefer streaming APIs rather than loading the entire file into memory.

---

# Serialization

## 25. What is Serialization?

**Serialization** is the process of converting a Java object's **state into a byte stream** so that it can be stored or transmitted.

```
Java Object
     ↓
Serialization
     ↓
Byte Stream
     ↓
File / Network / Storage
```

Example:

```java
class Student implements Serializable {

    int id;
    String name;
}
```

The object can then be written using `ObjectOutputStream`.

```java
Student s = new Student();

ObjectOutputStream out =
    new ObjectOutputStream(
        new FileOutputStream("student.ser")
    );

out.writeObject(s);

out.close();
```

The object state is converted into a byte stream.

Common use cases:

- Persisting object state
- Sending objects between systems
- Caching
- Legacy distributed Java applications

Important:

> Serialization serializes the object's state, not the entire object in the conceptual sense of copying its runtime environment.
> 

---

## 26. What is Deserialization?

**Deserialization** is the reverse process: converting a serialized byte stream back into a Java object.

```
Byte Stream
     ↓
Deserialization
     ↓
Java Object
```

Example:

```java
ObjectInputStream in =
    new ObjectInputStream(
        new FileInputStream("student.ser")
    );

Student s = (Student) in.readObject();

in.close();
```

`readObject()` returns `Object`, so a cast is commonly required.

Important:

> Deserialization reconstructs an object from serialized data; constructors of serializable classes are not invoked in the normal way during deserialization.
> 

---

## 27. What is `Serializable`?

`Serializable` is a **marker interface** in:

```
java.io.Serializable
```

A marker interface contains no methods that the implementing class must override.

Example:

```java
import java.io.Serializable;

class Student implements Serializable {

    int id;
    String name;
}
```

Now `Student` objects can participate in Java's standard object serialization mechanism.

Without implementing `Serializable`:

```java
out.writeObject(student);
```

can result in:

```
NotSerializableException
```

Important:

> `Serializable` tells Java's serialization mechanism that instances of the class are eligible for default serialization.
> 

---

## 28. What is `transient`?

`transient` is a Java keyword used to indicate that a field should **not be included in default serialization**.

Example:

```java
class User implements Serializable {

    String username;

    transient String password;
}
```

When serialized:

```
username → serialized
password → NOT serialized
```

After deserialization, the transient field gets its **default value**:

```
int       → 0
boolean   → false
reference → null
```

Example:

```java
class User implements Serializable {

    String username;
    transient String password;
}
```

After deserialization:

```java
System.out.println(user.password);
```

Output:

```
null
```

Common use cases:

- Sensitive fields that should not be serialized
- Temporary/calculated fields
- Fields that should be reconstructed after deserialization
- Resources such as streams or connections

Important security point:

> Do not rely on `transient` alone as a complete security mechanism, but it can prevent a field from being included in default Java serialization.
> 

---

## 29. What is `serialVersionUID`?

`serialVersionUID` is a unique identifier used during Java serialization to verify **serialization compatibility between class versions**.

Example:

```java
class Student implements Serializable {

    private static final long serialVersionUID = 1L;

    int id;
    String name;
}
```

During deserialization, Java checks the `serialVersionUID` of the serialized data against the current class.

If they are incompatible, you can get:

```
InvalidClassException
```

Conceptually:

```
Serialized object
      |
      | serialVersionUID = 1L
      ↓
Current Student class
      |
      | serialVersionUID = 1L
      ↓
Compatible → Deserialize
```

If they don't match:

```
1L ≠ 2L
   ↓
InvalidClassException
```

---

## 30. Why Explicitly Declare `serialVersionUID`?

If you don't declare it, Java can generate one based on class details.

Example:

```java
class Student implements Serializable {

    int id;
}
```

The compiler/JVM can derive a default `serialVersionUID`.

If the class changes, the generated value may change.

Then previously serialized objects may fail to deserialize.

Therefore, it is common to explicitly declare:

```java
private static final long serialVersionUID = 1L;
```

This gives you explicit control over serialization compatibility.

Example:

Version 1:

```java
class Student implements Serializable {

    private static final long serialVersionUID = 1L;

    int id;
}
```

Later:

```java
class Student implements Serializable {

    private static final long serialVersionUID = 1L;

    int id;
    String name;
}
```

Whether the change is actually compatible depends on the serialization rules and class evolution. Keeping the same ID does **not** automatically make every class change safe.

Important:

> Changing `serialVersionUID` intentionally indicates that the new class version should not be considered serialization-compatible with the old version.
> 

---

## 31. Serialization vs Deserialization

| Serialization | Deserialization |
| --- | --- |
| Object → byte stream | Byte stream → object |
| `writeObject()` | `readObject()` |
| Uses `ObjectOutputStream` | Uses `ObjectInputStream` |
| Used for storing/transmitting object state | Used for reconstructing object |
| Happens before storage/transmission | Happens after receiving/loading serialized data |

Simple memory trick:

```
SERIALIZATION
Object → Stream

DESERIALIZATION
Stream → Object
```

---

## 32. Complete Serialization Example

```java
import java.io.*;

class Student implements Serializable {

    private static final long serialVersionUID = 1L;

    int id;
    String name;
    transient String password;

    Student(int id, String name, String password) {
        this.id = id;
        this.name = name;
        this.password = password;
    }
}

public class Demo {

    public static void main(String[] args) throws Exception {

        Student student =
            new Student(101, "Atharva", "12345");

        // Serialization
        ObjectOutputStream out =
            new ObjectOutputStream(
                new FileOutputStream("student.ser")
            );

        out.writeObject(student);
        out.close();

        // Deserialization
        ObjectInputStream in =
            new ObjectInputStream(
                new FileInputStream("student.ser")
            );

        Student result =
            (Student) in.readObject();

        in.close();

        System.out.println(result.id);
        System.out.println(result.name);
        System.out.println(result.password);
    }
}
```

Output:

```
101
Atharva
null
```

Why is password `null`?

Because:

```java
transient String password;
```

prevents it from being included in default serialization.

---

## 33. Important Interview Points

Remember these:

```
Serialization
    ↓
Object → Byte Stream
```

```
Deserialization
    ↓
Byte Stream → Object
```

```
Serializable
    ↓
Marker interface
    ↓
Enables standard Java object serialization
```

```
transient
    ↓
Field excluded from default serialization
```

```
serialVersionUID
    ↓
Serialization compatibility identifier
```

The most common interview questions are:

**Q: Is `Serializable` a functional interface?**

No. It is a **marker interface** and has no abstract methods.

**Q: What happens to a transient field after deserialization?**

It gets its default value unless custom serialization logic restores it.

**Q: What happens if `serialVersionUID` doesn't match?**

`InvalidClassException` can occur.

**Q: Does serialization serialize static variables?**

No. Static fields belong to the class rather than the individual object's state, so they are not serialized as part of the object's default serialized state.

**Q: Are constructors called during deserialization?**

For a `Serializable` class, its serializable class constructors are not invoked during normal deserialization. The first non-serializable superclass's no-argument constructor is invoked.

One modern Java note: **native Java serialization (`ObjectInputStream`/`ObjectOutputStream`) has significant security and compatibility concerns and should not be used blindly for untrusted data.** For new systems, formats such as JSON or Protocol Buffers are often preferable for data interchange.
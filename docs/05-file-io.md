# 05 - File Input & Output (I/O)

Java's File I/O (InputStream/OutputStream) and FileReader/FileWriter provide ways to read and write from/to external files.

---

## 🏗 Basics of File Handling
The `File` class allows manipulation of files and directories.

```java
import java.io.File;

File file = new File("example.txt");
if (file.exists()) {
    System.out.println("File exists!");
} else {
    file.createNewFile(); // Requires try-catch
}
```

## 🏗 FileWriter & FileReader
Writing and reading text character by character.

```java
import java.io.FileWriter;
import java.io.FileReader;

// Writing to a file
FileWriter fw = new FileWriter("example.txt");
fw.write("Hello, Java File I/O!");
fw.close(); // Don't forget to close!

// Reading from a file
FileReader fr = new FileReader("example.txt");
int ch;
while ((ch = fr.read()) != -1) {
    System.out.print((char) ch);
}
fr.close();
```

## 🏎 Efficient Buffered Writing/Reading
Using `BufferedWriter` and `BufferedReader` for better performance.
```java
import java.io.BufferedWriter;
import java.io.BufferedReader;

// Writing with a Buffer
BufferedWriter bw = new BufferedWriter(new FileWriter("example.txt"));
bw.write("Line 1 with buffering!");
bw.newLine();
bw.write("Line 2 with buffering!");
bw.close();

// Reading with a Buffer
BufferedReader br = new BufferedReader(new FileReader("example.txt"));
String line;
while ((line = br.readLine()) != null) {
    System.out.println(line);
}
br.close();
```

## 🖇 Serialization & Deserialization
Process of converting an object state into a byte stream and vice versa.

```java
import java.io.*;

class User implements Serializable {
    String name;
    transient int password; // Won't be serialized
}

// Byte writing
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("user.ser"));
out.writeObject(new User());
out.close();
```

---
[⬅ Back to Roadmap](../README.md)

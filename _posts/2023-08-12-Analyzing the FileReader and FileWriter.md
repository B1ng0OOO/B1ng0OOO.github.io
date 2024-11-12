---
title: Analyzing the FileReader and FileWriter
date: 2023-08-12 15:00:43 +0800
categories: [Java Basic,I/O Stream]
tags: [Java]
author: <author_id>
description: The FileReader class and the FileWriter class are file reading and writing classes for character streams in Java. They specialize in processing text data (characters) rather than byte streams, and are suitable for processing files containing character data (such as .txt files).
---



## Analyzing the FileReader Class

FileReader is a class in the Java IO library designed for reading character streams from files. It inherits from the InputStreamReader class, which itself is responsible for converting byte streams (from InputStream) into character streams based on a specified character encoding.

```java
package java.io;

public class FileReader extends InputStreamReader {
    public FileReader(String fileName) throws FileNotFoundException {
        super(new FileInputStream(fileName));
    }

    public FileReader(File file) throws FileNotFoundException {
        super(new FileInputStream(file));
    }

    public FileReader(FileDescriptor fd) {
        super(new FileInputStream(fd));
    }
}
```
{: .nolineno file="java.io.FileReader" }



Here we can see the structure of the three constructors of the FileReader class. Whenever we create an instance of the FileReader class, we first generate an instance of the FileInputStream class, and then pass this instance as a parameter to the constructor of the InputStreamReader class, the superclass of the FileReader class, through super().

Then call the forInputStreamReader() of the StreamDecoder class to convert the byte stream into a character stream according to the specified character encoding.

forInputStreamReader() is a static factory method of StreamDecoder, which is used to generate a suitable StreamDecoder instance. This method needs to pass in a byte input stream, a lock object for synchronization, and a character encoding, and returns a decoder that can convert a byte stream into a character stream.

```java
public class InputStreamReader extends Reader {
    public InputStreamReader(InputStream in) {
        super(in);
        try {
            sd = StreamDecoder.forInputStreamReader(in, this, (String)null);
        } catch (UnsupportedEncodingException e) {
            throw new Error(e);
        }
    }
}
```
{: .nolineno file="java.io.InputStreamReader" }



So FileReader actually uses InputStreamWriter to achieve character-to-byte conversion:

- FileReader inherits InputStreamReader, and InputStreamReader is a bridge from byte stream to character stream. It is responsible for calling the method of StreamDecoder class to convert the bytes read from FileInputStream into characters according to the specified character encoding.

- This means that FileReader is just a convenient class to simplify the process of creating InputStreamReader and FileInputStream, and it uses the system character set to read files by default.





## Analyzing the FileWriter Class

FileWriter is a class in the Java IO library designed for writing character streams to files. It inherits from the OutputStreamWriter class, meaning it has the capability to convert character data into bytes, which are then written to the underlying file.

```java
package java.io;

public class FileWriter extends OutputStreamWriter {
    public FileWriter(String fileName) throws IOException {
        super(new FileOutputStream(fileName));
    }

    public FileWriter(String fileName, boolean append) throws IOException {
        super(new FileOutputStream(fileName, append));
    }

    public FileWriter(File file) throws IOException {
        super(new FileOutputStream(file));
    }

    public FileWriter(File file, boolean append) throws IOException {
        super(new FileOutputStream(file, append));
    }

    public FileWriter(FileDescriptor fd) {
        super(new FileOutputStream(fd));
    }
}
```
{: .nolineno file="java.io.FileWriter" }



Here are the five constructors of FileWriter. In fact, its construction process is similar to that of FileReader. It also creates an instance of FileOutputStream first, and then passes this instance as a parameter to the superclass OutputStreamWriter of FileWriter through super().

Then in the OutputStreamWriter constructor, call the forOutputStreamWriter() of the StreamEncoder class to convert the character stream into a byte stream according to the specified character encoding.

forOutputStreamWriter() is a static factory method of the StreamEncoder class in Java. It’s used internally to create a StreamEncoder instance. This method needs to pass in the target output stream, a lock object for synchronization, and character encoding, and returns a encoder that can handle character to byte conversion.

```java
public class OutputStreamWriter extends Writer {
    public OutputStreamWriter(OutputStream out) {
        super(out);
        try {
            se = StreamEncoder.forOutputStreamWriter(out, this, (String)null);
        } catch (UnsupportedEncodingException e) {
            throw new Error(e);
        }
    }
}
```
{: .nolineno file="java.io.OutputStreamWriter" }



So FileWriter actually uses OutputStreamWriter to achieve character-to-byte conversion:

- FileWriter inherits from OutputStreamWriter and is used to write character data to files.

- FileOutputStream is responsible for handling the underlying byte writing operation, because the file system can only store byte data.

- FileWriter converts character data into bytes through OutputStreamWriter, a bridge between character stream and byte stream, and then uses FileOutputStream to write bytes to the file.

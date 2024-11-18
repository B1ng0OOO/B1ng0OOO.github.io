---
title: Methods in FileInputStream and FileOutputStream
date: 2023-08-12 09:50:36 +0800
categories: [Java Basics, I/O Stream]
tags: [Java]
author: <author_id>
description: The FileOutputStream class and the FileInputStream class are stream classes used for file input and output in Java. They provide various methods for reading and writing byte data in files.
---



The main methods used in the FileInputStream and FileOutputStream classes are basically read() and write(), but the parameters in each method are different. This article will demonstrate the usage of various methods and their effects.



## Methods in FileInputStream

```
# src/test.txt
Life is a struggle!
keep hungry! keep foolish!
```



### 1. int read()

**Function**: Read a byte (8 bits) from the input stream. This method blocks if no input is yet available.

**Returns**: the next byte of data, or `-1` if the end of the file is reached.

**Throws**: `IOException` - if an I/O error occurs.

```java
public class ReadDemo01 {
    public static void main(String[] args) {
        String filePath = "src/test.txt";
        try (FileInputStream fis = new FileInputStream(filePath)) {
            int byteData;
            while ((byteData = fis.read()) != -1) {
                System.out.print((char) byteData);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
{: .nolineno file="src/ReadDemo01.java"}

```
# Output
Life is a struggle!
keep hungry! keep foolish!
```



### 2. int read(byte[] b)

**Function**: Reads up to `b.length` bytes of data from this input stream into an array of bytes. This method blocks until some input is available.

**Parameters**: `b` - the buffer into which the data is read.

**Returns**: the next byte of data, or `-1` if the end of the file is reached.

**Throws**: `IOException` - if an I/O error occurs.

```java
public class ReadDemo02 {
    public static void main(String[] args) {
        String filePath = "src/test.txt";
        try (FileInputStream fis = new FileInputStream(filePath)) {
            byte[] buffer = new byte[8];
            int bytesRead;
            while ((bytesRead = fis.read(buffer)) != -1) {
                System.out.write(buffer, 0, bytesRead);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
{: .nolineno file="src/ReadDemo02.java"}

```
# Output
Life is a struggle!
keep hungry! keep foolish!
```



### 3. int read(byte[] b, int off, int len)3

**Function**: Reads up to `len` bytes of data from this input stream into an array of bytes. If `len` is not zero, the method blocks until some input is available; otherwise, no bytes are read and `0` is returned.

**Parameters**: 

- `b` - the buffer into which the data is read.

- `off` - the start offset in the destination array b.

- `len` - the maximum number of bytes read.

**Returns**: the total number of bytes read into the buffer, or `-1` if there is no more data because the end of the file has been reached.

**Throws**:

- `NullPointerException` - If b is null.

- `IndexOutOfBoundsException` - If off is negative, len is negative, or len is greater than b.length - off.

- `IOException` - if an I/O error occurs.

```java
public class ReadDemo03 {
    public static void main(String[] args) {
        String filePath = "src/test.txt";
        try (FileInputStream fis = new FileInputStream(filePath)) {
            byte[] buffer = new byte[8];
            int bytesRead;
            while ((bytesRead = fis.read(buffer, 0, buffer.length)) != -1)
                System.out.write(buffer, 0, bytesRead);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
{: .nolineno file="src/ReadDemo03.java"}

```
# Output
Life is a struggle!
keep hungry! keep foolish!
```



## Methods in FileOutputStream

### 1. void write(int b)

**Function**: Writes the specified byte to this file output stream. This method writes the specified integer b as a byte, but actually writes only the lower 8 bits of the integer, i.e., the lowest byte of b.

**Parameters**: `b` - the byte to be written.

**Throws**: `IOException` - if an I/O error occurs.

```java
public class WriteDemo01 {
    public static void main(String[] args) {
        String filePath = "src/test.txt";
    	try (FileOutputStream fos = new FileOutputStream(filePath)) {
            fos.write(65);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
{: .nolineno file="src/WriteDemo01.java"}

```
A
```



### 2. void write(byte[] b)

**Function**: Writes `b.length` bytes from the specified byte array to this file output stream.

**Parameters:** `b` - the data.

**Throws:** `IOException` - if an I/O error occurs.

```java
public class WriteDemo02 {
    public static void main(String[] args) {
        String filePath = "src/test.txt";
    	try (FileOutputStream fos = new FileOutputStream(filePath)) {
            String content = "Hello World";
            fos.write(content.getBytes());
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
{: .nolineno file="src/WriteDemo02.java"}

```
Hello World
```



### 3. void wirte(byte[] b, int off, int len)

**Function**: Writes `len` bytes from the specified byte array starting at offset `off` to this file output stream.

**Parameters:**

- `b` - the data.
- `off` - the start offset in the data.
- `len` - the number of bytes to write.

**Throws:** `IOException` - if an I/O error occurs.

```java
public class WriteDemo03 {
    public static void main(String[] args) {
        String filePath = "src/test.txt";
    	try (FileOutputStream fos = new FileOutputStream(filePath)) {
            String content = "Hello World";
            fos.write(content.getBytes(), 6, content.length() - 6);
            System.out.println("Write successful!");
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
{: .nolineno file="src/WriteDemo03.java"}

```
World
```



## Append data

In Java, append writing usually refers to adding data to the end of a file instead of overwriting the existing content in the file. To implement file appending, we can use the second parameter in the constructor FileOutputStream(String name, boolean append) and set it to `true` to enable append mode when writing.

```java
public class Append {
    public static void main(String[] args) {
        String dataToAppend = "\nAppending this line.";
        String filePath = "src/test.txt";
        try (FileOutputStream fos = new FileOutputStream(filePath, true)) {
            fos.write(data.getBytes());
            System.out.println("Append Successfully!");
        } catch (IOException e) {
            throw new RuntimeException();
        }
    }
}
```
{: .nolineno file="src/Append.java"}

```
World
Appending this line.
```



## Copy data

Copying data in Java means reading data from one stream (input stream) and writing it to another stream (output stream). We can use FileInputStream and FileOutputStream to complete the file copy operation. These two classes are used to read files and write files respectively. In order to improve performance, buffered streams (BufferedInputStream and BufferedOutputStream) are usually used in data copying to reduce the number of disk I/O times.

```java
public class Copy {
    public static void main(String[] args) {
        String sourceFile = "src/source.txt";
        String destinationFile = "src/destination.txt";
        copyData(sourceFile, destinationFile);
    }

    private static void copyData(String sourceFile, String destinationFile) {
        try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream(sourceFile));
             BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destinationFile));){
            byte[] buffer = new byte[1024];
            int bytesRead;
            while ((bytesRead = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, bytesRead);
            }
            System.out.println("Copied " + sourceFile + " to " + destinationFile);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
{: .nolineno file="src/Copy.java"}


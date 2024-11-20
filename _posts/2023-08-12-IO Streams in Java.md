---
title: I/O Streams in Java
date: 2023-08-12 08:30:36 +0800
categories: [Java Basics, I/O Stream]
tags: [Java]
author: <author_id>
description: In Java, I/O Streams (Input/Output Streams) are one of the basic tools for processing data reading and writing. Whether reading data from a file, sending a request to the network, or getting input from the console, I/O Streams are the core way that Java programs interact with external data sources.
---



According to the different data units processed, it is divided into two types of classes, i.e., `ByteStream Classes` and `CharacterStream Classes`.



## ByteStream Classes

The ByteStream Classes are used to process binary data, i.e., reading and writing data in bytes. It is suitable for processing any type of data, including text, image, audio, video and other files. Especially when we need to process non-text files, they are the first choice. 

The ByteStream classes are divided into two types of classes, i.e., `InputStream Class` and `OutputStream Class`. These classes are abstract and the super classes of all the Input/Output stream classes.



### InputStream Class

The InputStream class provides methods to read bytes from a file, console or memory. It is an abstract class and can't be instantiated; however, various classes inherit the InputStream class and override its methods. The commonly used subclasses of InputStream class are given in the following table.

| SN   | Class                | Description                                                  |
| :--- | :------------------- | :----------------------------------------------------------- |
| 1    | FileInputStream      | This class provides methods to read bytes from the file.     |
| 2    | ByteArrayInputStream | This class provides methods to read bytes from the byte array. |
| 3    | BufferedInputStream  | This class provides methods to read bytes from the buffer.   |



### OutputStream Class

The OutputStream is an abstract class that is used to write 8-bit bytes to the stream. It is the superclass of all the output stream classes. This class can't be instantiated; however, various classes inherit the OutputStream class and override its methods. The commonly used subclasses of OutputStream class are given in the following table.

| SN   | Class                 | Description                                                  |
| ---- | --------------------- | ------------------------------------------------------------ |
| 1    | FileOutputStream      | This class provides methods to write the bytes to the file.  |
| 2    | ByteArrayOutputStream | This class provides methods to write the bytes to the byte array. |
| 3    | BufferedOutputStream  | This class provides methods to write the bytes to the buffer. |



## CharacterStream Classes

The CharacterStream Classes are mainly used to read characters from the source and write them to the destination. CharacterStream Classes can automatically handle character encoding issues (such as UTF-8, UTF-16, etc.), and are suitable for reading and writing text files. CharacterStream Classes use an internal buffer mechanism to combine multiple bytes into characters for operation. The CharacterStream classes are divided into two types of classes, i.e., `Reader class` and `Writer class`.



### Reader Class

Reader class is used to read the 16-bit characters from the input stream. However, it is an abstract class and can't be instantiated, but there are various subclasses that inherit the Reader class and override the methods of the Reader class. All methods of the Reader class throw an IOException. The commonly used subclasses of the Reader class are given in the following table.

| SN   | Class             | Description                                                  |
| ---- | ----------------- | ------------------------------------------------------------ |
| 1    | FileReader        | This class provides methods to read characters from the file. |
| 2    | BufferedReader    | This class provides methods to read characters from the buffer. |
| 3    | InputStreamReader | This class provides methods to convert bytes to characters.  |



### Writer Class

Writer class is used to write 16-bit Unicode characters to the output stream. The methods of the Writer class also throw an IOException. Like Reader class, Writer class is also an abstract class that cannot be instantiated; therefore, the subclasses of the Writer class are used to write the characters onto the output stream. The commonly used subclasses of the Writer class are given in the below table.

| SN   | Class              | Description                                                  |
| ---- | ------------------ | ------------------------------------------------------------ |
| 1    | FileWriter         | This class provides methods to write characters to the file. |
| 2    | BufferedWriter     | This class provides methods to write characters to the buffer. |
| 3    | OutputStreamWriter | This class provides methods to convert from bytes to characters. |



## ByteStream Classes vs CharacterStream Classes

| Comparison Points    | ByteStream                                                 | CharacterStream                                              |
| :------------------- | :--------------------------------------------------------- | :----------------------------------------------------------- |
| Data processing unit | In bytes (8 bits)                                          | In characters (16 bits)                                      |
| Applicable scenarios | Processing binary data such as images, audio, videos, etc. | Processing text data, such as text files, character data, etc. |
| Base Class           | InputStream, OutputStream                                  | Reader, Writer                                               |
| Common Class         | FileInputStream, FileOutputStream                          | FileReader, FileWriter                                       |
| Encoding processing  | Not handling character encoding issues.                    | Automatically handle character encoding issues.              |
| Common scenarios     | Process non-text files, such as images, audio, and video.  | Processing text files, such as `.txt` files.                 |


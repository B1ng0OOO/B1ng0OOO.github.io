---
title: Serialization and Deserialization in Java
date: 2023-08-19 14:13:29 +0800
categories: [Java Basics, Deserialization]
tags: [Java]
author: <author_id>
description: Serialization is a mechanism of converting the state of an object into a byte stream. Deserialization is the reverse process where the byte stream is used to recreate the actual Java object in memory. This mechanism is used to persist the object.
---



Reason for the deserialization vulnerability: When deserializing an object through `ObjectInputStream`, the JVM will first create the object based on the byte stream, and then call the `readObject()`. The code in `readObject()` will be automatically run immediately, which gives the attacker the ability to run the code on the server. If the deserialized data comes from an untrusted source, the attacker can trigger the malicious logic in the `readObject()` by constructing malicious serialized data.



## Serialization

**Definition**: Serialization is the process of converting data structures or object states into a format that can be stored or transmitted. This format is usually platform-independent, such as JSON, XML, binary format, etc., so that it can be exchanged in different systems or environments. The serialized data can be stored in a file or sent to other systems over the network.

**Purpose**: 

- **Data Persistence**: Serialization can convert objects into byte streams and then store them in files or databases so that the data can be reloaded after the program is closed or the system is restarted.

- **Network Transmission**: Serialization enables objects to be transmitted between different JVMs (Java virtual machines) over the network. Through serialization, objects can be converted into byte streams and sent to remote systems, and the receiver can restore the original object through deserialization.
- **Object Deep Copy**: Deep copy of objects can be achieved by writing objects to memory through serialization and then deserializing to create a brand new object. (When a completely independent copy of the object is required, rather than a simple copy of the reference.)



## Deserialization

**Definition**: Deserialization is the reverse process of serialization, which is to convert the serialized byte sequence data (such as JSON, XML, binary format, etc.) back to the original data structure or object state. This process allows the object to be reconstructed when needed, using its original data.

**Purpose**: 

- **Data Recovery:** The state of an object can be saved at different time points through serialization, and then restored through deserialization. To restore the state before the program was run.
- **Data Reception:** In network communication, the received serialized data needs to be deserialized into objects for use in the local system.



When an object is deserialized, Java will try to reconstruct the object from the byte stream. The process is as follows:

1. **Create a new instance**: Java will create an instance of the class by calling the reflection API.
2. **Initialize field**: Set the value of the field to the stored value by reading the data stored in the byte stream.

Note: During Java deserialization, do not need to call the class constructor (including the no-argument constructor) to create an instance of the class. However, if the parent class of the class does not implement the `Serializable`, the parent class needs to provide a no-argument constructor, otherwise deserialization will fail.



## Serialized information

In the Java serialization process, when converting an object into a byte stream, serialization not only stores the field data of the object, but also includes additional information.



### 1. Class metadata information

- **Fully Qualified Name**: The full name of the class, the path to the package containing the class.
- **serialVersionUID**: The unique identifier of the class, used to verify the consistency of the class version during deserialization.



### 2. Field Information

- **Non-transient and non-static fields**: During serialization, Java saves all non-transient and non-static fields in the object. Primitive data types (such as int, double) save their values directly, while reference data types (such as String, arrays, collections, and custom classes) save their contents recursively. Custom classes also need to implement the Serializable interface, otherwise the object cannot be instantiated.
- **Static fields**: Because static fields belong to the class itself, not a specific object, the value of the static field will not be saved during serialization.
- **Transient fields**: Fields modified by transient are not serialized. During deserialization, these fields are set to default values (such as null, 0, etc.). If you have customized the serialization process of the object, you can choose to manually serialize the transient field in the writeObject(). During deserialization, use the readObject() to restore the values of these fields.



### 3. Custom serialization and deserialization methods

If the class defines the `writeObject(ObjectOutputStream oos)` and `readObject(ObjectInputStream ois)` methods, these methods will be called first during serialization and deserialization to customize the serialization logic.

- **writeObject**: During serialization, the `writeObject` method will be called to control how the object's fields are serialized and to perform additional operations, such as encrypting sensitive data.

- **readObject**: During deserialization, the `readObject` method will be called to customize the process of restoring fields. This method can be used to verify data or perform other custom operations.



### 4. Fields in inheritance hierarchies

Serialization not only serializes the fields of the subclass, but also serializes all non-transient fields of the superclass. If the parent class implements the `Serializable` interface, the fields of the superclass will also be serialized.

- **Superclass implements Serializable**: If the superclass also implements the `Serializable` interface, the non-transient fields of the superclass will be serialized and restored during deserialization. The transient fields of the superclass can also be custom serialized in the custom serialization method and restored during deserialization.
- **Superclass doesn't implement Serializable**: If the superclass does not implement `Serializable`, the fields of the superclass will not be serialized. During deserialization, the superclass part will be initialized by calling the superclass's no-argument constructor.



### 5. Externalized `Externalizable` interface

If the class implements the `Externalizable` interface instead of the `Serializable` interface, the `writeExternal(ObjectOutput out)` method will be called during serialization, and the `readExternal(ObjectInput in)` method will be called during deserialization. Unlike `Serializable`, `Externalizable` does not automatically serialize the fields of the class, and the content to be serialized must be manually specified in the `writeExternal()` and `readExternal()` methods.



### 6. Serialization tag information

The serialized stream also contains some auxiliary marker information to help parse the byte stream. These markers include:

- The start and end markers of the object.
- The marker of the `null` object (if the field is `null`, the `null` marker will be recorded).
- The reference identifier of the recursive object (handling circular references).

These marker information ensure that the object structure can be correctly parsed and restored during deserialization.





## Java serialization and deserialization methods

**Note**:

- JDK's built-in serialization method is generally not used because of its poor performance, large amount of serialized data, low serialization and deserialization efficiency, and uncontrollable serialization process, which is prone to security issues. For application scenarios that pursue performance and efficiency, binary serialization protocols are usually preferred, such as `Kryo`, `Protobuf`, `Hessian`, etc. Although text serialization schemes such as JSON and XML are readable, they are generally only used in some lightweight scenarios or configuration file processing due to performance limitations.
- **Serialization and deserialization are processed strictly in order**. When an object is serialized, all fields (or fields in a custom serialization method) are written to the output stream in the order in the code. The order of writing is the same as the order of calling. When deserializing, it must be read in strict accordance with the order of serialization. Each `read` operation must match the order of `write` operations during serialization. Otherwise, data errors will occur or exceptions will be thrown.



## Implementing the Serializable Interface

Java's built-in serialization mechanism primarily relies on the **Serializable** interface, which serves as the default serialization method in Java. The `Serializable` interface is a marker interface with no methods to implement.

```java
public interface Serializable {
}
```
{: .nolineno}



- **Implementation Details**: Objects must implement the `java.io.Serializable` interface to enable serialization. The `ObjectOutputStream` class is used for serialization to convert objects into byte streams, while `ObjectInputStream` is used for deserialization to reconstruct objects from byte streams.

- **Advantages**:

  - Built-in support by Java.
  - Simple and easy to use.

- **Disadvantages**:
  - Relatively low performance.
  - Produces large byte streams.
  - Unsuitable for cross-language environments.
  - Security vulnerabilities (e.g., deserialization attacks).

**Example: Serializable Class**

```java
public class Person implements Serializable {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```
{: .nolineno}

**Example: Serialization and Deserialization**

```java
public class Main {
    public static void main(String[] args) {
        try {
            Person person = new Person("John", 30);

            // Serialize the object
            try (FileOutputStream fileOut = new FileOutputStream("person.ser");
                 ObjectOutputStream out = new ObjectOutputStream(fileOut)) {
                out.writeObject(person);
                System.out.println("Serialized data is saved in person.ser");
            }

            // Deserialize the object
            try (FileInputStream fileIn = new FileInputStream("person.bin");
                 ObjectInputStream in = new ObjectInputStream(fileIn)) {
                Person deserializedPerson = (Person) in.readObject();
                System.out.println("Deserialized Person: " + deserializedPerson);
            }
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```
{: .nolineno}

**Output**:

```
Serialized data is saved in person.bin
Deserialized Person: Person{name='John', age=30}
```
{: .nolineno}



## Implementing the Externalizable Interface

The `java.io.Externalizable` interface provides developers full control over the serialization and deserialization processes, unlike the default `Serializable` interface.

- **Implementation Details**: Classes implementing `Externalizable` must override two methods:
  - `writeExternal(ObjectOutput out)`: Defines how the object's data is serialized.
  - `readExternal(ObjectInput in)`: Defines how the object's data is deserialized.

- **Advantages**: 
  - Complete control over the serialization process.
  - Can optimize performance and storage by excluding unnecessary fields.
- **Disadvantages**: 
  - More complex to implement than `Serializable`.
  - The developer must explicitly handle each field's serialization and deserialization, which can lead to errors.



**Example: Implementing Externalizable**

```java
import java.io.*;

public class Externalization implements Externalizable {
    private String instanceVariable;
    private transient String transientVariable; // Will not be automatically serialized
    private static final long serialVersionUID = 7938278525774498274L;

    // Mandatory no-arg constructor for deserialization
    public Externalization() {
        System.out.println("No-arg constructor called");
    }

    public Externalization(String instanceVariable, String transientVariable) {
        this.instanceVariable = instanceVariable;
        this.transientVariable = transientVariable;
    }

    @Override
    public void writeExternal(ObjectOutput out) throws IOException {
        System.out.println("writeExternal() called");
        out.writeObject(instanceVariable);  // Serialize instance variable
        out.writeObject(transientVariable); // Serialize transient variable manually
    }

    @Override
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
        System.out.println("readExternal() called");
        instanceVariable = (String) in.readObject();  // Deserialize instance variable
        transientVariable = (String) in.readObject(); // Deserialize transient variable manually
    }

    @Override
    public String toString() {
        return "Instance Variable: " + instanceVariable + ", Transient Variable: " + transientVariable;
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Externalization originalObject = new Externalization("Instance Value", "Transient Value");

        // Serialize the object
        try (FileOutputStream fos = new FileOutputStream("externalization.per");
             ObjectOutputStream oos = new ObjectOutputStream(fos)) {
            oos.writeObject(originalObject);
        }

        // Deserialize the object
        try (FileInputStream fis = new FileInputStream("externalization.per");
             ObjectInputStream ois = new ObjectInputStream(fis)) {
            Object deserializedObject = ois.readObject();
            System.out.println("Deserialized Object: " + deserializedObject);
        }
    }
}
```
{: .nolineno}



**Output**:

```
writeExternal() called
No-arg constructor called
readExternal() called
Deserialized Object: Instance Variable: Instance Value, Transient Variable: Transient Value
```
{: .nolineno}



**Key Points**:

- **Custom Serialization**: Unlike `Serializable`, the developer must explicitly define how fields are handled during serialization and deserialization.
- **Transient Variables**: These are not serialized by default but can be manually handled within `writeExternal` and `readExternal`.
- **Mandatory No-Arg Constructor**: The deserialization process requires a no-argument constructor to create an instance of the class before populating its fields.

Using `Externalizable` provides flexibility and efficiency but requires careful handling to avoid bugs or security issues.



## JSON Serialization Libraries

Using JSON for serialization is ideal for cross-language data transmission. Popular libraries include **Gson** and **Jackson**, both of which make it straightforward to convert Java objects to JSON strings and vice versa.

- **Implementation Details**: Leverage libraries like Gson or Jackson to serialize Java objects into JSON strings and deserialize JSON strings back into Java objects.
- **Advantages**: 
  - Lightweight and human-readable.
  - Excellent cross-language support.

- **Disadvantages**: 
  - Performance may lag behind native serialization.
  - Not suitable for complex objects requiring precise control.




### 1. Gson 

Gson is a library developed by Google, known for its simplicity and ease of use.

**Maven Dependency**:

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.9</version>
</dependency>
```
{: .nolineno}

**Example: Using Gson**

```java
import com.google.gson.Gson;

class Person {
    private String name;
    private int age;
    private transient String password;  // Ignored during serialization

    public Person(String name, int age, String password) {
        this.name = name;
        this.age = age;
        this.password = password;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + ", password='" + password + "'}";
    }
}

public class GsonExample {
    public static void main(String[] args) {
        Gson gson = new Gson();

        // Serialize object to JSON
        Person person = new Person("Alice", 30, "secret_password");
        String json = gson.toJson(person);
        System.out.println("Serialized JSON: " + json);

        // Deserialize JSON to object
        Person deserializedPerson = gson.fromJson(json, Person.class);
        System.out.println("Deserialized Object: " + deserializedPerson);
    }
}
```
{: .nolineno}

**Output**:

```
Serialized JSON: {"name":"Alice","age":30}
Deserialized Object: Person{name='Alice', age=30, password='null'}
```
{: .nolineno}



### 2. Jackson

Jackson is a widely-used library that supports advanced features and complex use cases.

**Maven Dependency**:

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13.3</version>
</dependency>
```
{: .nolineno}

**Example: Using Jackson**

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.annotation.JsonIgnore;

class User {
    private String username;
    private int age;
    @JsonIgnore  // Ignored during serialization
    private String password;

    public User(String username, int age, String password) {
        this.username = username;
        this.age = age;
        this.password = password;
    }

    public User() { // Default constructor required for deserialization
    }

    @Override
    public String toString() {
        return "User{username='" + username + "', age=" + age + ", password='" + password + "'}";
    }
}

public class JacksonExample {
    public static void main(String[] args) throws Exception {
        ObjectMapper objectMapper = new ObjectMapper();

        // Serialize object to JSON
        User user = new User("Bob", 25, "my_password");
        String json = objectMapper.writeValueAsString(user);
        System.out.println("Serialized JSON: " + json);

        // Deserialize JSON to object
        User deserializedUser = objectMapper.readValue(json, User.class);
        System.out.println("Deserialized Object: " + deserializedUser);
    }
}
```
{: .nolineno}

**Output**:

```
Serialized JSON: {"username":"Bob","age":25}
Deserialized Object: User{username='Bob', age=25, password='null'}
```
{: .nolineno}



### 3. Comparison of Gson and Jackson

| Feature                 | Gson                        | Jackson                                |
| ----------------------- | --------------------------- | -------------------------------------- |
| **JSON Formatting**     | Automatic, no configuration | Requires explicit configuration        |
| **Field Ignoring**      | Use `transient` keyword     | Use `@JsonIgnore` annotation           |
| **Serialization Speed** | Fast for simple use cases   | Optimized for complex scenarios        |
| **Feature Support**     | Simple object mapping       | Advanced features (e.g., data binding) |
| **Library Size**        | Smaller                     | Larger                                 |
| **Best Use Case**       | Simple JSON serialization   | 复杂场景（如 REST API、数据绑定）      |



## XML Serialization with JAXB

XML serialization in Java is often done using **JAXB (Java Architecture for XML Binding)**, which allows seamless conversion between Java objects and XML.

**Maven Dependencies**: 

```xml
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
</dependency>
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
    <version>2.3.1</version>
</dependency>
```
{: .nolineno}

**Example: XML Serialization with JAXB**

```java
import javax.xml.bind.*;
import java.io.*;

// Class mapped to XML with JAXB annotations
@XmlRootElement
class Person {
    private String name;
    private int age;
    private String email;

    // Default constructor required for JAXB
    public Person() {}

    public Person(String name, int age, String email) {
        this.name = name;
        this.age = age;
        this.email = email;
    }

    @XmlElement
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @XmlElement
    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @XmlElement
    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + ", email='" + email + "'}";
    }
}

public class JAXBExample {
    public static void main(String[] args) throws JAXBException {
        // Create an object for serialization
        Person person = new Person("Alice", 30, "alice@example.com");

        // Serialize the object to XML
        StringWriter xmlWriter = new StringWriter();
        JAXBContext context = JAXBContext.newInstance(Person.class);
        Marshaller marshaller = context.createMarshaller();

        // Enable formatted XML output
        marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        marshaller.marshal(person, xmlWriter);
        String xmlContent = xmlWriter.toString();
        System.out.println("Serialized XML:\n" + xmlContent);

        // Deserialize the XML back into a Java object
        Unmarshaller unmarshaller = context.createUnmarshaller();
        StringReader xmlReader = new StringReader(xmlContent);
        Person deserializedPerson = (Person) unmarshaller.unmarshal(xmlReader);

        System.out.println("Deserialized Object: " + deserializedPerson);
    }
}
```
{: .nolineno}

**Output**:

```
Serialized XML:
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<person>
    <age>30</age>
    <email>alice@example.com</email>
    <name>Alice</name>
</person>

Deserialized Object: Person{name='Alice', age=30, email='alice@example.com'}
```
{: .nolineno}



## Using the Kryo Serialization Library

`Kryo` is a flexible and highly efficient serialization library designed for fast and efficient conversion of Java objects to binary data and vice versa. It is particularly suitable for Java applications requiring high-performance serialization. By leveraging class registration and optimized byte processing, Kryo significantly improves data transfer efficiency, especially when dealing with large data volumes or complex objects.

- **How it Works**
  - **Object Registration**: Before using Kryo for serialization, the classes to be serialized must be registered. Registered classes can be quickly located using Kryo's built-in identifiers.
  - **Serialization Process**: Converts the object's type informatiSerialized XML:n and field values into a byte stream. During serialization, Kryo uses identifiers instead of full class names to reduce the size of the transmitted data.
  - **Deserialization Process**: Reads the byte stream and reconstructs the object. Using the registered classes and field information, Kryo restores the object's state.

- **Features**

  - **High Speed**: Kryo is several times faster than Java's native serialization, particularly when handling large datasets.

  - **Memory Efficiency**: Kryo's serialization format is more compact, effectively reducing the size of serialized data and saving memory.

  - **Supports Circular References**: Kryo can handle circular references between objects, which is crucial for complex object graphs.

  - **Version Control**: Kryo provides versioning capabilities, allowing developers to handle class version differences during serialization and deserialization.



**Adding Kryo Dependency**:

Add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>com.esotericsoftware</groupId>
    <artifactId>kryo</artifactId>
    <version>5.2.0</version>
</dependency>
```
{: .nolineno}

**Simple `User` Class Definition**:

```java
package com.B1ng0OOO;

public class User {
    private String name;
    private int age;

    public User() {}

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + '}';
    }
}
```
{: .nolineno}

**Using Kryo for Serialization and Deserialization**:

```java
package com.B1ng0OOO;

import com.esotericsoftware.kryo.Kryo;
import com.esotericsoftware.kryo.io.Input;
import com.esotericsoftware.kryo.io.Output;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class KryoDemo {
    public static void main(String[] args) throws IOException {
        Kryo kryo = new Kryo();

        // Register the User class
        kryo.register(User.class);

        // Create a User object
        User user = new User("B1ng0OOO", 18);

        // Serialization
        try (FileOutputStream fos = new FileOutputStream("kryo.per");
             Output output = new Output(fos)) {
            kryo.writeObject(output, user);
        }

        // Deserialization
        try (FileInputStream fis = new FileInputStream("kryo.per");
             Input input = new Input(fis)) {
            User deserializedUser = kryo.readObject(input, User.class);
            // Print the deserialized object
            System.out.println(deserializedUser);
        }
    }
}
```
{: .nolineno}

**Output**:

```
User{name='B1ng0OOO', age=18}
```
{: .nolineno}



**Advantages**:

1. **Superior Performance**: Faster and more efficient than Java's built-in serialization mechanism.
2. **Handles Complex Objects**: Can serialize nested objects and circular references.
3. **Customizability**: Allows for customized serialization strategies and optimized class registration.
4. **Cross-Language Support**: Compatible with other programming languages (e.g., Scala).



**Disadvantages**:

1. **Increased Complexity**: Requires manual class registration, which may lead to runtime errors if classes are forgotten.
2. **Limited Compatibility**: Does not support all Java types, such as certain Java 8 features or dynamically generated proxy classes.
3. **Third-Party Dependency**: Being a third-party library, Kryo might introduce versioning or compatibility issues in long-term projects.





## Using the Hessian Serialization Library

`Hessian` is an efficient, cross-language binary serialization protocol, ideal for distributed systems and RPC applications requiring fast data transfer. With its simple API and effective serialization mechanisms, Hessian facilitates seamless data exchange across multiple programming languages.

- **How it Works**
  - **Data Encoding**: Converts object data into a binary format, including the object's type, fields, and values, represented as a sequence of bytes.
  - **Data Transmission**: Sends the encoded binary data over the network to the target system.
  - **Data Decoding**: On the receiving end, decodes the binary data back into the original object, reconstructing its state.

- **Features**
  - **Ease of Use**: Provides a straightforward API for serialization and deserialization operations.
  - **Compact Format**: The binary format reduces the size of transmitted data, enhancing network transmission efficiency.
  - **Support for Complex Data Structures**: Capable of serializing complex objects, arrays, collections, and maps.
  - **Version Compatibility**: Enables handling of version differences during serialization and deserialization processes.



**Adding Hessian Dependency**:

Add the following dependency in your `pom.xml`:

```xml
<dependency>
    <groupId>com.caucho</groupId>
    <artifactId>hessian</artifactId>
    <version>4.0.63</version>
</dependency>
```
{: .nolineno}



**Simple `User` Class Definition**:

```java
import java.io.Serializable;

public class User implements Serializable {
    public String name;
    public int age;

    public User() {}

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{name='" + name + ", age=" + age + '}';
    }
}
```
{: .nolineno}

**Using Hessian for Serialization and Deserialization**:

```java
package com.B1ng0OOO;

import com.caucho.hessian.io.Hessian2Input;
import com.caucho.hessian.io.Hessian2Output;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class HessianDemo {
    public static void main(String[] args) throws IOException {
        User user = new User("B1ng0OOO", 18);

        // Serialization
        try (FileOutputStream fos = new FileOutputStream("hessian.ser");
             Hessian2Output h2o = new Hessian2Output(fos)) {
            h2o.writeObject(user);
        }

        // Deserialization
        try (FileInputStream fis = new FileInputStream("hessian.ser");
             Hessian2Input h2i = new Hessian2Input(fis)) {
            user = (User) h2i.readObject();
        }

        // Output the deserialized object
        System.out.println(user);
    }
}
```
{: .nolineno}

```
User{name='B1ng0OOO', age=18}
```
{: .nolineno}

**Advantages**:

1. **Efficient Binary Serialization**: Optimized for quick data transfer in networked applications.
2. **Cross-Language Compatibility**: Supports multiple programming languages, making it suitable for cross-language scenarios.
3. **Simplicity**: Easy to use and integrate into existing systems.





**Disadvantages**:

1. **Limited Readability**: Being a binary protocol, it lacks the human-readable nature of formats like JSON or XML, making debugging less convenient.
2. **Manual Version Control**: May require extra handling for object versioning in complex serialization scenarios.
3. **Performance Considerations**: While fast, it may not outperform other high-performance libraries like Kryo in certain use cases.



## static and transient keywords

### 1. Restrictions on the static keyword

**static variables** exist only once in memory and belong to the class itself, not to the object instance, so they are not serialized. When serializing an object, the serialization process only saves the state of the object instance, not the state of the class, so no matter which serialization method is used, static variables will not be included in the serialized state of the object.



### 2. Restrictions on the transient keyword

**transient keyword** is used to indicate that a variable should be ignored during serialization, and its state will not be saved during serialization. It is suitable for some sensitive information or non-serializable resources. This keyword is mainly used to protect some fields that do not need or do not want to be serialized, such as sensitive information (password), unimportant temporary states, or resources that cannot be serialized (such as file handles, thread objects, etc.). When an object is deserialized, the transient variable will be set to its default value (null for objects, 0 or false for basic data types).



### 3. transient in special cases

Transient fields can be serialized in some special cases, such as using custom serialization methods (through writeObject() and readObject()), we can choose to manually serialize transient fields in the writeObject() method. When deserializing, use the readObject() method to restore the values of these fields.

```java
import java.io.*;

class User implements Serializable {
    private String username;
    private transient String password; // Will not be serialized

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    // Custom serialization and deserialization
    private void writeObject(ObjectOutputStream oos) throws IOException {
        oos.defaultWriteObject(); // Default serialization
        oos.writeObject(password); // Manually serialize the password field
    }

    private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
        ois.defaultReadObject(); // Default serialization
        password = (String) ois.readObject(); // Manually restore the password field
    }

    @Override
    public String toString() {
        return "User{username='" + username + "', password='" + password + "'}";
    }
}

public class TransientExample {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        User user = new User("Alice", "secret_password");

        // serialization
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);
        oos.writeObject(user);
        oos.close();

        // Deserialization
        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bis);
        User deserializedUser = (User) ois.readObject();
        ois.close();

            // Output the deserialized object
        System.out.println("The deserialized object:" + deserializedUser);
    }
}
```
{: .nolineno}



## serialVersionUID

serialVersionUID is a unique identifier used to identify class versions in the Java serialization mechanism. It is a special static field. It is used to ensure version compatibility during serialization and deserialization. When a class is serialized into a byte stream, serialVersionUID is stored as part of the serialized data. During deserialization, Java compares the stored serialVersionUID with the serialVersionUID of the current class. If they do not match, an InvalidClassException is thrown, indicating that the class of the object during deserialization is incompatible with that during serialization.



### 1. Particularity

- **`static` keyword**: Static fields belong to the class, not to the object. Therefore, static fields are not included in the object serialization process. So, when you serialize an object, all static variables are not serialized as part of the object state.
- **serialVersionUID is metadata of the class**: The Java serialization mechanism uses serialVersionUID to represent the version information of a class. Although it is a static field, it does not need to be serialized like ordinary object fields. It is processed implicitly by the Java mechanism during the serialization and deserialization process and stored in the serialization stream to ensure version consistency. When Java serializes an object, it writes the metadata of the class (including serialVersionUID, class name, etc.) into the serialization stream. Therefore, serialVersionUID is stored as **class description information** in the serialization stream, rather than as an instance field.



### 2. Serialization process

- When an object is serialized, Java first writes the relevant metadata of the class to which the object belongs into the serialization stream. These metadata include the class name, structural information of the class (such as fields and methods), and the serialVersionUID.
- The Java serialization system reads this serialVersionUID and **uses it to confirm whether the version of the current class during deserialization is consistent with the version of the class during serialization.**



## Deserialization Vulnerability

### 1. What conditions should be met ?

1. **Common Requirement**: The class must implement the `Serializable` interface.
2. **Entry Point (source)**: Override the `readObject` method to trigger a well-known gadget chain, preferably one with wide compatibility and leveraging JDK-provided classes.
3. **Gadget Chain**: Use a gadget chain composed of JDK-provided classes to exploit deserialization behavior and trigger the vulnerability.
4. **Sink Class (sink)**: Execute malicious actions such as Remote Code Execution (RCE), Server-Side Request Forgery (SSRF), or writing files during deserialization.





### 2. Mitigation Measures  

1. **Avoid Using Native Java Serialization**:  Opt for safer serialization mechanisms like JSON, XML, or Google's Protocol Buffers.  
   
2. **Enforce Class Whitelisting**:  Use the `resolveClass()` method of `ObjectInputStream` to restrict deserialization to a predefined list of allowed classes.  
   
3. **Strictly Limit Classpath**:  Minimize dependencies on third-party libraries and tightly control class availability in the classpath.  
   
4. **Implement Sandboxing**:  Execute deserialization code in a controlled sandbox environment to restrict the permissions of potentially malicious code.  
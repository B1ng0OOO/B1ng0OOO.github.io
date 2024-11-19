---
title: Introduction to Reflection in Java
date: 2023-08-15 10:37:06 +0800
categories: [Java Basics, Reflection]
tags: [Java]
author: <author_id>
description: Java Reflection is a powerful feature in Java that allows us to inspect and manipulate classes, methods, fields, and constructors at runtime, even if we don’t know their names or types at compile time. The reflection API is part of the java.lang.reflect package and provides a way to dynamically access and modify program elements.Additionally, we can instantiate new objects, invoke methods and get or set field values using reflection.
---





Each class is represented by an object of the Class in the JVM. When a Java program loads a class, the JVM creates a Class object for the class, which stores all the information about the class. Through the Class object, we can get the class's constructor, member variables, and methods, and even access private fields and methods.



To use Java reflection, we do not need to include any special jar packages, any special configuration or Maven dependencies. JDK has a set of Built-in Classes in the [java.lang.reflect](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/reflect/package-summary.html) package.

- `java.lang.Class`: Represents an object of a class or interface. We can get the Class object through methods such as Class.forName() or Object.getClass().

- `java.lang.reflect.Method`: Represents a method in a class. We can call the method dynamically through Method.invoke().
- `java.lang.reflect.Field`: Represents a field in a class. We can access and modify the field through Field.get() and Field.set().
- `java.lang.reflect.Constructor`: Represents the constructor of a class. We can create a new class instance through Constructor.newInstance().



## Core of Java Reflection

The Class class is the core part of the reflection in Java. It represents the runtime structure of a class. Whenever a Java program loads a class, the JVM generates a unique Class object for the class, which represents the structure and behavior information of the class. Through the Class class, we can obtain relevant information about the class at runtime and operate on the class instance.

Can get the class of the Class object:

- Common Class (Classes with `public`, `protected`, `private`, and `default` access permissions)
- Interface
- Array Type
- Primitive Types (The primitive types also have corresponding Class objects)
- Enum
- Annotation
- Generics (Although Java Generics are type-erased at runtime, we can still get the Class object of the Generic.)

---



The Class object is the basis of reflection. All reflection operations need to be performed through the Class object. The following are some common ways:

- **Class.forName**()

Using Class.forName() is a dynamic class loading method, usually used to load class at runtime, suitable for use when the Class name is known but the class has not been compiled.

```java
Class<?> clazz = Class.forName("com.example.MyClass");
```
{: .nolineno}

- **obj.getClass**()

By instantiating an object and calling the getClass() method, we can get the Class object corresponding to its runtime type.

```java
MyClass obj = new MyClass();
Class<?> clazz = obj.getClass();
```
{: .nolineno}


- **Class.class**

This method is static. The class name is known at compile time, and the .class attribute of the class is used directly to obtain the Class object.

```java
Class<MyClass> clazz = MyClass.class;
```
{: .nolineno}



## Methods in Class

### 1. Class<?> forName(String className)

**Function**: Load the corresponding Class object according to the fully qualified name (including Package Name) of the specified class.

**Parameters**: `className` - the fully qualified name of the desired class.

**Return**: the `Class` object for the class with the specified name.

```java
Class<?> clazz = Class.forName("com.b1ng0ooo.reflection.MyClass");
```
{: .nolineno}



### 2. T newInstance()

**Function**: Creates a new instance of the class represented by this `Class` object.

**Returns**: a newly allocated instance of the class represented by this object.

```java
Object obj = clazz.newInstance();
```
{: .nolineno}



### 3. String getName()

**Function**: Returns the name of the entity (class, interface, array class, primitive type, or void) represented by this `Class` object, as a `String`.

**Returns**: the name of the class or interface represented by this object.

```java
String className = clazz.getName();
```
{: .nolineno}



### 4. Class<?>[] getInterfaces()

**Function**: Determines the interfaces implemented by the class or interface represented by this object.

**Returns**: an array of interfaces implemented by this class.

```java
Class<?>[] interfaces = clazz.getInterfaces();
```
{: .nolineno}



### 5. Class<? super T> getSuperclass()

**Function**: Returns the `Class` representing the superclass of the entity (class, interface, primitive type or void) represented by this `Class`. If this `Class` represents either the `Object` class, an interface, a primitive type, or void, then null is returned. If this object represents an array class then the `Class` object representing the `Object` class is returned.

**Returns**: the superclass of the class represented by this object.

```java
Class<?> superClass = clazz.getSuperclass();
```
{: .nolineno}



### 6. Field getField(String name)

**Function**: Returns a `Field` object that reflects the specified public member field of the class or interface represented by this `Class` object. The `name` parameter is a `String` specifying the simple name of the desired field.

**Parameters**: `name` - the field name

**Returns**: the `Field` object of this class specified by `name`

```java
Field field = clazz.getField("string1");
```
{: .nolineno}



### 7. Field[] getFields()

**Function**: Returns an array containing `Field` objects reflecting all the accessible public fields of the class or interface represented by this `Class` object.

**Returns**: the array of `Field` objects representing the public fields

```java
Field[] fields = clazz.getFields();
```
{: .nolineno}



### 8. Field getDeclaredField(String name)

**Function**: Returns a `Field` object that reflects the specified declared field of the class or interface represented by this `Class` object. The `name` parameter is a `String` that specifies the simple name of the desired field.

**Parameters**: `name` - the name of the field

**Returns**: the `Field` object for the specified field in this class.

```java
// private String myField;
Field declaredField = clazz.getDeclaredField("myField");
declaredField.setAccessible(true);
```
{: .nolineno}




### 9. Field[] getDeclaredFields()

**Function**: Returns an array of `Field` objects reflecting all the fields declared by the class or interface represented by this `Class` object. This includes public, protected, default (package) access, and private fields, but excludes inherited fields.

**Returns**: the array of `Field` objects representing all the declared fields of this class.

```java
Field[] declaredFields = clazz.getDeclaredFields();
for (Field field : declaredFields) {
    System.out.println("Declared Field: " + field.getName());
}
```
{: .nolineno}



### 10. Method getMethod(String name, Class<?>... parameterTypes)

**Function**: Returns a Method object that reflects the specified public member method of the class or interface represented by this Class object.

**Parameters**: 

- `name` - the name of the method

- `parameterTypes` - the list of parametersat matches the specified `name` and `parameterTypes`.

```java
// public void myMethod(String str, int num) { ... }
Method method = clazz.getMethod("methodName", String.class, int.class);
```
{: .nolineno}



### 11. Method[] getMethods()

**Function**: Returns an array containing `Method` objects reflecting all the public methods of the class or interface represented by this `Class` object, including those declared by the class or interface and those inherited from superclasses and superinterfaces.

**Returns**: the array of `Method` objects representing the public methods of this class.

```java
Method[] methods = clazz.getMethods();
for (Method method : methods) {
    System.out.println("Public Method: " + method.getName());
}
```
{: .nolineno}



### 12. Method getDeclaredMethod(String name, Class<?>... parameterTypes)

**Function**: Returns a `Method` object that reflects the specified declared method of the class or interface represented by this `Class` object.

**Parameters**: 

- `name` - the name of the method
- `parameterTypes` - the parameter array

**Returns**: the `Method` object for the method of this class matching the specified name and parameters

```java
// private void myMethod(String param1, int param2) { ... }
Method method = clazz.getDeclaredMethod("myMethod", String.class, int.class);
method.setAccessible(true);
```
{: .nolineno}



### 13. Method[] getDeclaredMethods()

**Function**: Returns an array containing `Method` objects reflecting all the declared methods of the class or interface represented by this `Class` object, including public, protected, default (package) access, and private methods, but excluding inherited methods.

**Returns**: the array of `Method` objects representing all the declared methods of this class

```java
Method[] declaredMethods = clazz.getDeclaredMethods();
for (Method method : declaredMethods) {
    System.out.println("Declared Method: " + method.getName());
}
```
{: .nolineno}



### 14. Constructor\<T\> getConstructor(Class<?>... parameterTypes)

**Function**: Returns a `Constructor` object that reflects the specified public constructor of the class represented by this `Class` object.

**Parameters**: `parameterTypes` - the parameter array

**Returns**: the `Constructor` object of the public constructor that matches the specified `parameterTypes`.

```java
// public MyClass(String param) { ... }
Constructor<?> constructor = clazz.getConstructor(String.class);
```
{: .nolineno}



### 15. Constructor<?>[] getDeclaredConstructors()

**Function**: Returns an array of `Constructor` objects reflecting all the constructors declared by the class represented by this `Class` object.

**Returns**: the array of `Constructor` objects representing all the declared constructors of this class.

```java
Constructor<?>[] constructors = clazz.getConstructors();
for (Constructor<?> constructor : constructors) {
    System.out.println("Public Constructor: " + constructor.getName());
}
```
{: .nolineno}



### 16. Constructor\<T\> getDeclaredConstructor(Class<?>... parameterTypes)

**Function**: Returns a `Constructor` object that reflects the specified constructor of the class or interface represented by this `Class` object.

**Parameters**: `parameterTypes` - the parameter array

**Returns**: The `Constructor` object for the constructor with the specified parameter list.

```java
// private MyClass(String param) { ... }
Constructor<?> constructor = clazz.getDeclaredConstructor(String.class);
constructor.setAccessible(true);
```
{: .nolineno}



### 17. Constructor<?>[] getDeclaredConstructors()

**Function**: Returns an array of `Constructor` objects reflecting all the constructors declared by the class represented by this `Class` object. These are public, protected, default (package) access, and private constructors. The elements in the array returned are not sorted and are not in any particular order. If the class has a default constructor, it is included in the returned array. This method returns an array of length 0 if this `Class` object represents an interface, a primitive type, an array class, or void.

**Returns**: the array of `Constructor` objects representing all the declared constructors of this class.

```java
Constructor<?>[] declaredConstructors = clazz.getDeclaredConstructors();
for (Constructor<?> constructor : declaredConstructors) {
    System.out.println("Declared Constructor: " + constructor.getName());
}
```
{: .nolineno}

The above methods are the methods in the `Class` class that are commonly used in reflection. The `Class` class has other methods. You can read the [Java.lang.class](https://docs.oracle.com/javase/8/docs/api/?java/lang/Class.html) source code yourself.



## Methods in Field

The `Field` class is part of the Java reflection API and is used to represent fields in a class or interface. It allows the attribute values of a class to be dynamically obtained and manipulated at runtime.

### 1. Object get(Object obj)

**Function**: Returns the value of the field represented by this `Field`, on the specified object. The value is automatically wrapped in an object if it has a primitive type.

**Parameters**: `obj` - object from which the represented field's value is to be extracted

**Returns**: the value of the represented field in object `obj`; primitive values are wrapped in an appropriate object before being returned.

```java
Field field = clazz.getField("fieldName");
Object value = field.get(objectInstance);
```
{: .nolineno}



### 2. void set(Object obj, Object value)

**Function**: Sets the field represented by this `Field` object on the specified object argument to the specified new value. The new value is automatically unwrapped if the underlying field has a primitive type.

**Parameters**: 

- `obj` - the object whose field should be modified
- `value` - the new value for the field of `obj` being modified

```java
Field field = clazz.getField("fieldName");
field.set(objectInstance, "newValue");
```
{: .nolineno}



### 3. String getName()

**Function**: Returns the name of the field represented by this `Field` object.

**Returns**: the simple name of the underlying member.

```java
String fieldName = field.getName();
```
{: .nolineno}



### 4. Class<?> getType()

**Function**: Returns a `Class` object that identifies the declared type for the field represented by this `Field` object.

**Returns**: a `Class` object identifying the declared type of the field represented by this object.

```java
Class<?> fieldType = field.getType();
```
{: .nolineno}



### 5. void setAccessible(boolean flag)

**Function**: Set the `accessible` flag for this object to the indicated boolean value. A value of `true` indicates that the reflected object should suppress Java language access checking when it is used. A value of `false` indicates that the reflected object should enforce Java language access checks.

**Parameters**: `flag` - the new value for the `accessible` flag

> The setAccessible() is defined in the AccessibleObject, and the Field, Constructor, and Method classes all inherit AccessibleObject, so they can all use this method.

```java
field.setAccessible(true);
```
{: .nolineno}



### 6. int getModifiers()

**Function**: Returns the Java language modifiers for the field represented by this `Field` object, as an integer. The `Modifier` class should be used to decode the modifiers.

**Returns**: the Java language modifiers for the underlying member.

```
int modifiers = field.getModifiers();
```

> Supplement: Each modifier constant has a corresponding integer value, and multiple modifiers can be combined using bitwise operations. If a field is `public static final`, the value returned by the `getModifiers()` method is the bitwise OR result of the integer values corresponding to these modifiers:
>
> ```java
> int modifiers = Modifier.PUBLIC | Modifier.STATIC | Modifier.FINAL; // 1 | 8 | 16 = 25
> ```
>
> | Modifier Constant       | Integer value | Description                                                  |
> | ----------------------- | ------------- | ------------------------------------------------------------ |
> | `Modifier.PUBLIC`       | 1             | Indicates that the field is `public` and visible to all classes. |
> | `Modifier.PRIVATE`      | 2             | Indicates that the field is `private` and is only visible within this class. |
> | `Modifier.PROTECTED`    | 4             | Indicates that the field is `protected` and is visible to the same package and subclasses. |
> | `Modifier.STATIC`       | 8             | Indicates that the field is `static`, belonging to the class rather than the instance. |
> | `Modifier.FINAL`        | 16            | Indicates that the field is final and its value cannot be modified. |
> | `Modifier.VOLATILE`     | 32            | Indicates that the field is `volatile`, used for multi-threaded operations. |
> | `Modifier.TRANSIENT`    | 64            | Indicates that the field is `transient` and is ignored during serialization. |
> | `Modifier.SYNCHRONIZED` | 128           | Indicates that the field is `synchronized`, used for thread synchronization. |

The above methods are the methods in the `Field` class that are commonly used in reflection. The `Field` class has other methods. You can read the [Java.lang.reflect.Field](https://docs.oracle.com/javase/8/docs/api/?java/lang/reflect/Field.html) source code yourself.



## Methods in Method

The `Method` class is part of the Java reflection API and is used to represent methods in a class or interface. It allows methods of a class to be obtained and called dynamically at runtime.

### 1. String getName()

**Function**: Returns the name of the method represented by this `Method` object, as a `String`.

**Returns**: the simple name of the underlying member

```java
String methodName = method.getName();
```
{: .nolineno}



### 2. Class<?>[] getParameterTypes()

**Function**: Returns an array of `Class` objects that represent the formal parameter types, in declaration order, of the executable represented by this object. Returns an array of length 0 if the underlying executable takes no parameters.

**Returns**: the parameter types for the executable this object represents.

```java
Class<?>[] parameterTypes = method.getParameterTypes();
```
{: .nolineno}



### 3. Class<?> getReturnType()

**Function**: Returns a `Class` object that represents the formal return type of the method represented by this `Method` object.

**Returns**: the return type for the method this object represents.

```java
Class<?> returnType = method.getReturnType();
```
{: .nolineno}



### 4. Object invoke(Object obj, Object... args)

**Function**: Invokes the underlying method represented by this `Method` object, on the specified object with the specified parameters. Individual parameters are automatically unwrapped to match primitive formal parameters, and both primitive and reference parameters are subject to method invocation conversions as necessary.

**Parameters**: 

- `obj` - the object the underlying method is invoked from
- `args` - the arguments used for the method call

**Returns**: the result of dispatching the method represented by this object on `obj` with parameters `args`

```java
Object result = method.invoke(objectInstance, methodArgs);
```
{: .nolineno}

The above methods are the methods in the `Method` class that are commonly used in reflection. The `Method` class has other methods. You can read the [Java.lang.reflect.Method](https://docs.oracle.com/javase/8/docs/api/?java/lang/reflect/Method.html) source code yourself.



## Methods in Constructor

The `Constructor` class is part of the Java reflection API and represents the constructor of a class. It allows us to dynamically obtain information about the constructor at runtime and use it to instantiate objects even without knowing the class name or construction parameters.

### 1. T newInstance(Object... initargs)

**Function**: Uses the constructor represented by this `Constructor` object to create and initialize a new instance of the constructor's declaring class, with the specified initialization parameters. Individual parameters are automatically unwrapped to match primitive formal parameters, and both primitive and reference parameters are subject to method invocation conversions as necessary.

**Parameters**: `initargs` - array of objects to be passed as arguments to the constructor call; values of primitive types are wrapped in a wrapper object of the appropriate type (e.g. a `float` in a [`Float`](https://docs.oracle.com/javase/8/docs/api/java/lang/Float.html))

**Returns**: a new object created by calling the constructor this object represents

```java
Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
Object instance = constructor.newInstance("John", 25);
```
{: .nolineno}



### 2. Class<?>[] getParameterTypes()

**Function**: Returns an array of `Class` objects that represent the formal parameter types, in declaration order, of the executable represented by this object. Returns an array of length 0 if the underlying executable takes no parameters.

**Returns**: the parameter types for the executable this object represents

```java
Class<?>[] paramTypes = constructor.getParameterTypes();
```
{: .nolineno}

The above methods are the methods in the `Constructor` class that are commonly used in reflection. The `Constructor` class has other methods. You can read the [Java.lang.reflect.Constructor](https://docs.oracle.com/javase/8/docs/api/?java/lang/reflect/Constructor.html) source code yourself.



## Demonstrate basic reflection operations

Create objects, get and call class methods, and access class fields through reflection.

```java
public class Employee {
    // Private fields
    private String name;
    private double salary;
    private int age;

    // Public Constructor
    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    // Private Constructor
    private Employee(String name, double salary, int age) {
        this.name = name;
        this.salary = salary;
        this.age = age;
    }

    // Public Method
    public void raiseSalary(double percentage) {
        this.salary += this.salary * percentage / 100;
        System.out.println(name + "'s new salary: " + salary);
    }

    // Private Method
    private void displayAge() {
        System.out.println(name + " is " + age + " years old.");
    }

    @Override
    public String toString() {
        return "Employee{name='" + name + "', salary=" + salary + ", age=" + age + "}";
    }
}
```
{: .nolineno}



```java
import com.b1ng0ooo.reflect.Employee;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ComplexReflectionExample {
    public static void main(String[] args) throws Exception {
        // 1. Get the class object
        Class<?> employeeClass = Class.forName("Employee");

        // 2. Get and call the public constructor to create an instance
        Constructor<?> publicConstructor = employeeClass.getConstructor(String.class, double.class);
        Object employeeInstance = publicConstructor.newInstance("B1ng0OOO", 50000.0);

        // 3. Get and call the private constructor to create an instance
        Constructor<?> privateConstructor = employeeClass.getDeclaredConstructor(String.class, double.class, int.class);
        privateConstructor.setAccessible(true);
        Object employeeInstance2 = privateConstructor.newInstance("Google", 60000.0, 30);

        // 4. Get and call public methods
        Method raiseSalaryMethod = employeeClass.getMethod("raiseSalary", double.class);
        raiseSalaryMethod.invoke(employeeInstance, 10.0);

        // 5. Accessing private fields and modifying them
        Field nameField = employeeClass.getDeclaredField("name");
        nameField.setAccessible(true);
        nameField.set(employeeInstance, "B1ng0OOO");

        Field salaryField = employeeClass.getDeclaredField("salary");
        salaryField.setAccessible(true);
        salaryField.set(employeeInstance, 55000.0);

        // 6. Calling private methods
        Method privateMethod = employeeClass.getDeclaredMethod("displayAge");
        privateMethod.setAccessible(true);
        privateMethod.invoke(employeeInstance2);
        
        // 7. Print the modified object
        System.out.println(employeeInstance);
        System.out.println(employeeInstance2);
    }
}
```
{: .nolineno}

```
B1ng0OOO's new salary: 55000.0
Google is 30 years old.
Employee{name='B1ng0OOO', salary=55000.0, age=0}
Employee{name='Google', salary=60000.0, age=30}
```
{: .nolineno}


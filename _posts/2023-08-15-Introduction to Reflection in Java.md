---
title: Introduction to Reflection in Java
date: 2023-08-15 10:37:06 +0800
categories: [Java Basic, Reflection]
tags: [Java]
author: <author_id>
description: Java Reflection is a powerful feature in Java that allows us to inspect and manipulate classes, methods, fields, and constructors at runtime, even if we don’t know their names or types at compile time. The reflection API is part of the java.lang.reflect package and provides a way to dynamically access and modify program elements.
---



# 1. 反射

## 1.1 什么是反射？

Java 反射是一种动态获取类、方法、属性等信息并调用的机制。通过反射，程序可以在运行时获取对象的类型信息，并动态调用类的方法或访问属性，哪怕这些类或方法在编译时并不知道。

每个类在 Java 虚拟机中都由一个 `Class` 对象表示。当 Java 程序加载一个类时，虚拟机会为这个类创建一个 `Class `对象，这个 `Class` 对象就保存了类的所有信息。通过 `Class` 对象，可以获取类的构造函数、成员变量和方法，甚至可以访问私有的字段和方法。

---



## 1.2 常用的反射相关类和方法

**使用 Java 反射，不需要包含任何特殊的 jar 包、任何特殊的配置或 Maven 依赖项。JDK 内置了一组类在 [java.lang.reflect](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/reflect/package-summary.html) 包中。**

- **`java.lang.Class` 类**：表示类或接口的对象，可以通过 `Class.forName()`、`对象.getClass()` 等方法获取 `Class` 对象。
- **`java.lang.reflect.Method` 类**：表示类中的方法，可以通过 `Method.invoke()` 动态调用方法。
- **`java.lang.reflect.Field` 类**：表示类中的字段，可以通过 `Field.get()` 和 `Field.set()` 访问和修改字段。
- **`java.lang.reflect.Constructor` 类**：表示类的构造函数，可以通过 `Constructor.newInstance()` 创建新的类实例。

---



# 2. Class 类

`Class` 类在 Java 中是反射机制的核心部分，它表示的是类的运行时结构。每当一个 Java 程序加载类时，JVM 都会为这个类生成一个唯一的 `Class` 对象，代表这个类的结构和行为信息。通过 `Class` 类，我们可以在运行时获取类的相关信息，并且对类的实例进行操作。

**可以获取 `Class` 对象的类**：

- **普通类**（包括 `public`、`protected`、`private` 和 `default` 访问权限的类）
- **接口**（Interface）
- **数组类型**
- **基本类型（Primitive Types）** 基本类型本身也有对应的 `Class` 对象
- **枚举（Enum）**
- **注解类型（Annotation）**
- **泛型类（Generic Types）** 虽然在运行时 Java 的泛型会进行类型擦除，但仍可以获取泛型类的 `Class` 对象。

---

`Class` 对象是反射的基础，所有反射操作都需要通过 `Class` 对象进行。以下是常见的几种方式：

- **通过 `Class.forName()` 方法**

使用 `Class.forName()` 是动态加载类的方式，通常用于在运行时加载类，适合在名称字符串已知但类未编译时使用。

```java
Class<?> clazz = Class.forName("com.example.MyClass");
```

- **通过对象的 `getClass()` 方法**

通过实例化对象调用 `getClass()` 方法，获取其运行时类型对应的 `Class` 对象。

```java
MyClass obj = new MyClass();
Class<?> clazz = obj.getClass();
```

- **通过类的 `.class` 属性**

这种方式是静态的，编译时已经知道类名，直接使用类的 `.class` 属性获取 `Class` 对象。

```java
Class<MyClass> clazz = MyClass.class;
```

---



## 2.1 forName(String)

**作用**：根据指定的类的全限定名（包括包名）加载对应的 `Class` 对象。

**参数**：`className`：`String` 类型，表示类的全限定名（包括包名）

**返回值**：`Class<?>` 对象，表示类的运行时类型。

```java
Class<?> clazz = Class.forName("com.b1ng0ooo.reflection.MyClass");
```

---



## 2.2 newInstance()

**作用**：调用类的无参构造函数，创建该类的实例。

**返回值**：`Object` 对象，表示类的实例。

```java
Object obj = clazz.newInstance();
```

---



## 2.3 getName()

**作用**：返回此 `Class` 对象表示的实体的名称（全限定名）。

**返回值**：`String`，类的全限定名。

```java
String className = clazz.getName();
```

---



## 2.4 getInterfaces()

**作用**：获取该 `Class` 对象表示的类或接口所实现的接口数组。

**返回值**：`Class<?>[]`，包含类实现的所有接口。

```java
Class<?>[] interfaces = clazz.getInterfaces();
```

---



## 2.5 getSuperClass()

**作用**：返回此 `Class` 对象表示的类的父类。

**返回值**：`Class<?>`，类的父类。

```java
Class<?> superClass = clazz.getSuperclass();
```

---



## 2.6 getField(String)

**作用**：获取当前类及其父类中指定的 `public` 字段。

**参数**：`name`：`String`，表示要获取的字段名称。

**返回值**：`Field` 对象，表示获取到的 `public` 字段。

```java
Field[] fields = clazz.getFields();
for (Field field : fields) {
    System.out.println("Public Field: " + field.getName());
}
```

---



## 2.7 getFields()

**作用**：返回当前类及其父类中所有的 `public` 字段。

**返回值**：`Field[]` 数组，包含所有的 `public` 字段。

```java
Field[] fields = clazz.getFields();
```

---



## 2.8 getDeclaredField(String)

**作用**：获取当前类或接口中声明的指定字段（包括 `private`、`protected` 和 `public` 字段），不包括继承自父类的字段。

**参数**：`name`：`String`，表示要获取的字段名称。

**返回值**：`Field` 对象，表示获取到的字段。

```java
// private String myField;
Field declaredField = clazz.getDeclaredField("myField");
declaredField.setAccessible(true);  // 允许访问私有字段
```

---



## 2.9 getDeclaredFields()

**作用**：获取当前类或接口中声明的所有字段（包括 `private`、`protected` 和 `public` 字段），不包括继承自父类的字段。

**返回值**：`Field[]`，字段对象数组。

```java
Field[] declaredFields = clazz.getDeclaredFields();
for (Field field : declaredFields) {
    System.out.println("Declared Field: " + field.getName());
}
```

---



## 2.10 getMethod(String , Class<?>...)

**作用**：返回当前类及其父类中的指定 `public` 方法（包括继承的方法）。

**参数**：

- `name`：`String` 类型，表示要获取的方法名。

- `parameterTypes`：`Class<?>...`，可变长度参数，表示方法的参数类型，依次按方法签名顺序指定。如果方法没有参数，则传入空参数或忽略该参数。

**返回值**：`Method` 对象，表示该方法。

```java
// public void myMethod(String str, int num) { ... }
Method method = clazz.getMethod("methodName", String.class, int.class);
```

---



## 2.11 getMethods()

**作用**：返回当前类及其父类中的所有 `public` 方法（包括继承的方法）。

**返回值**：`Method[]` 数组，包含所有的 `public` 方法。

```java
Method[] methods = clazz.getMethods();
for (Method method : methods) {
    System.out.println("Public Method: " + method.getName());
}
```

---



## 2.12 getDeclaredMethod(Stirng, Class<?>...)

**作用**：返回当前类中声明的指定方法，包括 `private` 和 `protected` 方法，但不包括父类中的方法。

**参数**：

- `name`：`String`，表示要获取的方法名称。
- `parameterTypes`：`Class<?>...`，可变长度参数，表示方法的参数类型（按顺序）。

**返回值**：`Method` 对象，表示获取到的指定方法。

```java
// private void myMethod(String param1, int param2) { ... }
Method method = clazz.getDeclaredMethod("myMethod", String.class, int.class);
method.setAccessible(true);  // 允许访问私有方法
```

---



## 2.13 getDeclaredMethods()

**作用**：返回当前类中声明的所有方法（包括 `private`、`protected`、`public` 方法），不包括继承的方法。

**返回值**：`Method[]` 数组，包含类中声明的所有方法。

```java
Method[] declaredMethods = clazz.getDeclaredMethods();
for (Method method : declaredMethods) {
    System.out.println("Declared Method: " + method.getName());
}
```

---



## 2.14 getConstructor(Class<?>...)

**作用**：获取当前类中 **指定的 `public` 构造函数**，该构造函数的参数类型与提供的 `parameterTypes` 参数匹配。

**参数**：`parameterTypes`：`Class<?>...`，可变长度参数，表示构造函数的参数类型（按顺序）。

**返回值**：`Constructor<?>` 对象，表示获取到的指定 `public` 构造函数。

```java
// public MyClass(String param) { ... }
Constructor<?> constructor = clazz.getConstructor(String.class);
```

---



## 2.15 getConstructors()

**作用**：返回当前类中所有的 **`public` 构造函数**。

**返回值**：`Constructor<?>[]` 数组，包含类的所有 `public` 构造函数。

```java
Constructor<?>[] constructors = clazz.getConstructors();
for (Constructor<?> constructor : constructors) {
    System.out.println("Public Constructor: " + constructor.getName());
}
```

---



## 2.16 getDeclaredConstructor(Calss<?>)

**作用**：获取当前类中 **指定的构造函数**，无论其访问修饰符（`public`、`private`、`protected`）。

**参数**：`parameterTypes`：`Class<?>...`，可变长度参数，表示构造函数的参数类型（按顺序）。

**返回值**：`Constructor<?>` 对象，表示获取到的指定构造函数。

```java
// private MyClass(String param) { ... }
Constructor<?> constructor = clazz.getDeclaredConstructor(String.class);
constructor.setAccessible(true);  // 允许访问私有构造函数
```

---



## 2.17 getDeclaredConstructors()

**作用**：返回当前类中声明的 **所有构造函数**，无论其访问修饰符（`public`、`private`、`protected`）。

**返回值**：`Constructor<?>[]` 数组，包含类中声明的所有构造函数。

```java
Constructor<?>[] declaredConstructors = clazz.getDeclaredConstructors();
for (Constructor<?> constructor : declaredConstructors) {
    System.out.println("Declared Constructor: " + constructor.getName());
}
```

以上这些方法就是反射中常用到的 `Class` 类中的方法，`Class` 类还有其他方法可以自行阅读 `Java.lang.Class` 源码。

---



# 3. Field 类

`Field` 类是 Java 反射 API 的一部分，用于表示类或接口中的字段（属性）。它允许在运行时动态获取和操作类的属性值。

## 3.1 get(Object obj)

**作用**：获取指定对象的字段值。

**参数**：`Object obj`：要从中获取字段值的对象实例。

**返回值**：返回字段的值，类型为 `Object`。

```java
Field field = clazz.getField("fieldName");
Object value = field.get(objectInstance);
```

---



## 3.2 set(Object obj, Object value)

**作用**：设置指定对象的字段值。

**参数**：

- `Object obj`：要设置字段值的对象实例。
- `Object value`：要设置的字段值。

```java
Field field = clazz.getField("fieldName");
field.set(objectInstance, "newValue");
```

---



## 3.3 getName()

**作用**：返回字段的名称。

**返回值**：返回字段的名称，类型为 `String`。

```java
String fieldName = field.getName();
```

---



## 3.4 getType()

**作用**：返回字段的类型，作为 `Class<?>` 对象。

**返回值**：返回字段的类型，类型为 `Class<?>`。

```java
Class<?> fieldType = field.getType();
```

---



## 3.5 setAccessible(boolean)

**作用**：设置字段的可访问性。`setAccessible(true)` 允许访问私有或受保护的字段。

**参数**：`boolean flag`：如果设置为 `true`，则允许访问私有字段；如果为 `false`，则禁止访问。

> setAccessible 方法定义在 AccessibleObject 类中，而 Field 、Constructor 、Method 类都继承了 AccessibleObject，所以它们都可以使用此方法。
>

```java
field.setAccessible(true);  // 允许访问私有字段
```

---



## 3.6 getModifiers()

**作用**：返回字段的修饰符（如 `public`、`private`、`static` 等），作为 `int` 类型。

**返回值**：返回字段的修饰符，类型为 `int`。

```
int modifiers = field.getModifiers();
```

> 修饰符补充：每个修饰符常量都有一个对应的整数值，使用位运算可以组合多个修饰符。如果一个字段是 `public static final`，那么 `getModifiers()` 方法返回的值是这些修饰符对应整数值的按位或（OR）结果：
>
> ```java
> int modifiers = Modifier.PUBLIC | Modifier.STATIC | Modifier.FINAL; // 1 | 8 | 16 = 25
> ```
>
> | 修饰符常量              | 整数值 | 描述                                       |
> | ----------------------- | ------ | ------------------------------------------ |
> | `Modifier.PUBLIC`       | 1      | 表示字段是 `public`，对所有类可见。        |
> | `Modifier.PRIVATE`      | 2      | 表示字段是 `private`，只在本类可见。       |
> | `Modifier.PROTECTED`    | 4      | 表示字段是 `protected`，对同包和子类可见。 |
> | `Modifier.STATIC`       | 8      | 表示字段是 `static`，属于类而非实例。      |
> | `Modifier.FINAL`        | 16     | 表示字段是 `final`，其值不能被修改。       |
> | `Modifier.VOLATILE`     | 32     | 表示字段是 `volatile`，用于多线程操作。    |
> | `Modifier.TRANSIENT`    | 64     | 表示字段是 `transient`，在序列化时忽略。   |
> | `Modifier.SYNCHRONIZED` | 128    | 表示字段是 `synchronized`，用于线程同步。  |

以上这些方法就是反射中常用到的 `Field` 类中的方法，`Field` 类还有其他方法可以自行阅读 `Java.lang.reflect.Field` 源码。

---



# 4. Method 类

`Method` 类是 Java 反射 API 的一部分，用于表示类或接口中的方法。它允许在运行时动态获取和调用类的方法。

## 4.1 getName()

**作用**：返回方法的名称。

**返回值**：返回方法的名称，类型为 `String`。

```java
String methodName = method.getName();
```

---



## 4.2 getParameterTypes()

**作用**：返回方法的参数类型数组。

**返回值**：返回一个 `Class<?>[]` 数组，表示方法的参数类型。

```java
Class<?>[] parameterTypes = method.getParameterTypes();
```

---



## 4.3 getReturnType()

**作用**：返回方法的返回类型。

**返回值**：返回一个 `Class<?>` 对象，表示方法的返回类型。

```java
Class<?> returnType = method.getReturnType();
```

---



## 4.4 getModifiers()

**作用**：返回方法的修饰符（如 `public`、`private`、`static` 等），作为 `int` 类型。

**返回值**：返回方法的修饰符，类型为 `int`。

```java
int modifiers = method.getModifiers();
```

---



## 4.5 invoke(Object obj, Object... args)

**作用**：在指定对象上调用该方法。

**参数**：

- `Object obj`：要调用方法的对象实例。
- `Object... args`：方法参数的可变数组。

**返回值**：返回方法的返回值，类型为 `Object`。

```java
Object result = method.invoke(objectInstance, methodArgs);
```

---



## 4.6 setAccessible(boolean flag)

- **作用**：设置方法的可访问性。通过 `setAccessible(true)` 可以访问私有或受保护的方法。
- **参数**：`boolean flag`：如果设置为 `true`，则允许访问私有方法；如果为 `false`，则禁止访问。

```java
method.setAccessible(true);  // 允许访问私有方法
```

以上这些方法就是反射中常用到的 `Method` 类中的方法，`Method` 类还有其他方法可以自行阅读 `Java.lang.reflect.Method` 源码。

---



# 5. Constructor 类

`Constructor` 类是 Java 反射 API 的一部分，表示类的构造方法。它允许我们在运行时动态获取构造方法的信息，并可以通过它来实例化对象，即便是在不知道类名或构造参数的情况下。

## 5.1 newInstance(Object... initargs)

**作用**：通过反射创建一个新对象实例。

**参数**：`Object... initargs`，构造方法所需的参数列表。

**返回值**：返回新创建的对象，类型为 `Object`。

```java
Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
Object instance = constructor.newInstance("John", 25);
```

---



## 5.2 getModifiers()

- **作用**：返回构造方法的修饰符，以 `int` 类型的位掩码表示。
- **返回值**：返回构造方法的修饰符，类型为 `int`。

```java
int modifiers = constructor.getModifiers();
```

---



## 5.3 getParameterTypes()

**作用**：返回构造方法的参数类型数组。

**返回值**：返回一个 `Class<?>[]` 数组，表示构造方法的参数类型。

```java
Class<?>[] paramTypes = constructor.getParameterTypes();
```

---



## 5.4 **`setAccessible(boolean flag)`**

**作用**：设置构造方法的可访问性。通过 `setAccessible(true)` 可以访问私有或受保护的构造方法。

**参数**：`boolean flag`，如果设置为 `true`，允许访问私有构造方法；否则恢复默认访问控制。

```java
constructor.setAccessible(true);  // 允许访问构造方法
```

以上这些方法就是反射中常用到的 `Constructor` 类中的方法，`Constructor` 类还有其他方法可以自行阅读 `Java.lang.reflect.Constructor` 源码。

---



# 6. 演示反射基本操作

通过反射创建对象、获取和调用类的方法、访问类的字段。

```java
public class Employee {
    // 私有字段
    private String name;
    private double salary;
    private int age;

    // 公有构造方法
    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    // 私有构造方法
    private Employee(String name, double salary, int age) {
        this.name = name;
        this.salary = salary;
        this.age = age;
    }

    // 公有方法
    public void raiseSalary(double percentage) {
        this.salary += this.salary * percentage / 100;
        System.out.println(name + "'s new salary: " + salary);
    }

    // 私有方法
    private void displayAge() {
        System.out.println(name + " is " + age + " years old.");
    }

    @Override
    public String toString() {
        return "Employee{name='" + name + "', salary=" + salary + ", age=" + age + "}";
    }
}
```



```java
import com.b1ng0ooo.reflect.Employee;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ComplexReflectionExample {
    public static void main(String[] args) throws Exception {
        // 1. 获取类对象
        Class<?> employeeClass = Class.forName("Employee");

        // 2. 获取并调用公有构造方法，创建实例
        Constructor<?> publicConstructor = employeeClass.getConstructor(String.class, double.class);
        Object employeeInstance = publicConstructor.newInstance("xiaohong", 50000.0);

        // 3. 获取并调用私有构造方法，创建实例
        Constructor<?> privateConstructor = employeeClass.getDeclaredConstructor(String.class, double.class, int.class);
        privateConstructor.setAccessible(true);  // 允许访问私有构造方法
        Object employeeInstance2 = privateConstructor.newInstance("xiaoming", 60000.0, 30);

        // 4. 获取并调用公有方法
        Method raiseSalaryMethod = employeeClass.getMethod("raiseSalary", double.class);
        raiseSalaryMethod.invoke(employeeInstance, 10.0);  // xiaohong 的工资加了 10%

        // 5. 访问私有字段并修改
        Field nameField = employeeClass.getDeclaredField("name");
        nameField.setAccessible(true);  // 允许访问私有字段
        nameField.set(employeeInstance, "xiaohong");

        Field salaryField = employeeClass.getDeclaredField("salary");
        salaryField.setAccessible(true);
        salaryField.set(employeeInstance, 55000.0);

        // 6. 调用私有方法
        Method privateMethod = employeeClass.getDeclaredMethod("displayAge");
        privateMethod.setAccessible(true);  // 允许访问私有方法
        privateMethod.invoke(employeeInstance2);  // 输出 xiaoming 的年龄

        // 7. 打印修改后的对象
        System.out.println(employeeInstance);   // 输出修改后的 xiaohong 的信息
        System.out.println(employeeInstance2);  // 输出 xiaoming 的信息
    }
}
```

```makefile
xiaohong's new salary: 55000.0
xiaoming is 30 years old.
Employee{name='xiaohong', salary=55000.0, age=0}
Employee{name='xiaoming', salary=60000.0, age=30}
```


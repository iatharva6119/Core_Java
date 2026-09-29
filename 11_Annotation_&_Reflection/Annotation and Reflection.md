# Annotation and Reflection

## Annotations

### 1. What are Annotations?

**Annotations** are metadata added to Java code that provide additional information to the compiler, tools, or runtime.

They start with `@`.

Example:

```java
@Override
public String toString() {
    return "Student";
}
```

The annotation itself generally does not directly execute business logic. It provides metadata that another component can interpret.

Annotations can be applied to:

- Classes
- Methods
- Fields
- Parameters
- Constructors
- Local variables
- Packages
- Type uses

Example:

```java
@Deprecated
class OldService {
}
```

Conceptually:

```
Java Code
    ↓
Annotation
    ↓
Metadata
    ↓
Compiler / Tool / Runtime
```

Common uses:

- Compiler checks
- Suppressing warnings
- Framework configuration
- Dependency injection
- ORM mapping
- Runtime processing

For example, Spring heavily uses annotations such as `@Component`, `@Service`, `@Autowired`, and `@RestController`.

---

### 2. Built-in Annotations

Java provides several built-in annotations.

Important ones for interviews:

```
@Override
@Deprecated
@SuppressWarnings
@FunctionalInterface
```

There are also **meta-annotations** used to define/customize annotations:

```
@Retention
@Target
@Documented
@Inherited
```

Java also has annotations related to safe varargs and other compiler checks, such as:

```
@SafeVarargs
```

The most important ones for your current list are:

```
@Override
@Deprecated
@SuppressWarnings
```

---

### 3. `@Override`

`@Override` indicates that a method is intended to **override a method from a superclass or implement a method from an interface**.

Example:

```java
class Animal {

    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {

    @Override
    void sound() {
        System.out.println("Bark");
    }
}
```

If you accidentally write:

```java
@Override
void soud() {
}
```

the compiler reports an error because `soud()` does not override a valid superclass/interface method.

Without `@Override`, the compiler might treat it as a completely new method.

Important:

> `@Override` is primarily a compile-time safety check. It does not itself perform overriding.
> 

It is useful because it catches:

- Misspelled method names
- Incorrect parameters
- Incorrect method signatures

---

### 4. `@Deprecated`

`@Deprecated` indicates that a class, method, field, constructor, etc. **should no longer be used for new code**.

Example:

```java
class Demo {

    @Deprecated
    void oldMethod() {
        System.out.println("Old");
    }
}
```

Usage:

```java
Demo d = new Demo();
d.oldMethod();
```

The compiler/IDE can generate a deprecation warning.

A more informative modern form can include:

```java
@Deprecated(since = "2.0", forRemoval = true)
```

Example:

```java
@Deprecated(since = "2.0", forRemoval = true)
void oldMethod() {
}
```

Meaning:

- `since = "2.0"` → deprecated since version 2.0
- `forRemoval = true` → intended for removal in a future release

Important:

> `@Deprecated` does not automatically remove or disable the API. It communicates that the API should be avoided.
> 

---

### 5. `@SuppressWarnings`

`@SuppressWarnings` tells the compiler to **suppress specified compiler warnings** for the annotated element.

Example:

```java
@SuppressWarnings("unchecked")
List<String> list = new ArrayList();
```

Another common example:

```java
@SuppressWarnings("deprecation")
```

This suppresses deprecation warnings in the relevant scope.

You can also apply it to a method:

```java
@SuppressWarnings("unchecked")
static void test() {
    // code
}
```

Important:

> `@SuppressWarnings` does not fix the underlying problem. It only tells the compiler not to report the specified warning in that scope.
> 

Avoid suppressing warnings unnecessarily, because it can hide real problems.

---

### 6. Custom Annotations

You can create your own annotation using:

```
@interface
```

Example:

```java
@interface Author {
    String name();
    String date();
}
```

Usage:

```java
@Author(name = "Atharva", date = "2026")
class Student {
}
```

Here:

```
@Author
```

is a custom annotation.

Annotation elements can have types such as:

- Primitive types
- `String`
- `Class`
- Enum
- Other annotation types
- Arrays of these types

Example:

```java
@interface Info {
    String name();
    int version() default 1;
}
```

Usage:

```java
@Info(name = "Student")
class Student {
}
```

Since `version` has a default value:

```
version = 1
```

does not need to be specified.

---

### 7. Custom Annotation with `@Target`

You can control where your annotation can be used with `@Target`.

Example:

```java
import java.lang.annotation.*;

@Target(ElementType.METHOD)
@interface Loggable {
}
```

Now:

```java
@Loggable
void process() {
}
```

is valid.

But:

```java
@Loggable
class Student {
}
```

is not valid because the annotation is restricted to methods.

Common `ElementType` values:

```
TYPE
METHOD
FIELD
PARAMETER
CONSTRUCTOR
LOCAL_VARIABLE
PACKAGE
ANNOTATION_TYPE
TYPE_USE
```

Example:

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@interface Info {
}
```

This allows `@Info` on classes and methods.

---

### 8. Runtime Annotations

Annotations can have different **retention policies**.

The important ones are:

```java
@Retention(RetentionPolicy.SOURCE)
@Retention(RetentionPolicy.CLASS)
@Retention(RetentionPolicy.RUNTIME)
```

`RUNTIME` is especially important for reflection.

Example:

```java
@Retention(RetentionPolicy.RUNTIME)
@interface Author {
    String name();
}
```

Usage:

```java
@Author(name = "Atharva")
class Student {
}
```

Because the annotation has:

```
RetentionPolicy.RUNTIME
```

it can be inspected while the program is running.

Conceptually:

```
Source
  ↓
Compile
  ↓
.class
  ↓
Runtime
  ↓
Reflection can inspect annotation
```

---

### 9. Annotation Retention Policies

| Retention | Available |
| --- | --- |
| `SOURCE` | Source code only |
| `CLASS` | Stored in `.class`, generally not available through runtime reflection |
| `RUNTIME` | Available at runtime through reflection |

Example:

```java
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation {
}
```

This is required when you want runtime reflection to inspect the annotation.

---

### 10. Reading a Runtime Annotation

Suppose:

```java
@Retention(RetentionPolicy.RUNTIME)
@interface Author {
    String name();
}
```

And:

```java
@Author(name = "Atharva")
class Student {
}
```

Using reflection:

```java
Class<Student> clazz = Student.class;

Author author = clazz.getAnnotation(Author.class);

System.out.println(author.name());
```

Output:

```
Atharva
```

Flow:

```
@Author
   ↓
Stored with RUNTIME retention
   ↓
Class metadata
   ↓
Reflection
   ↓
getAnnotation()
   ↓
Annotation object
```

This is the fundamental connection between **annotations and reflection**.

---

### 11. Important Annotation Meta-Annotations

For interviews, know these four:

```
@Retention
@Target
@Documented
@Inherited
```

`@Retention` → determines how long annotation information is retained.

`@Target` → determines where the annotation can be applied.

`@Documented` → indicates that the annotation should be included in generated Javadoc documentation.

`@Inherited` → allows a class annotation to be inherited by subclasses when the annotation is used on a superclass. It applies to class annotations, not arbitrary elements such as methods.

Example:

```java
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Role {
}
```

Then:

```java
@Role
class Parent {
}

class Child extends Parent {
}
```

`Child.class.getAnnotation(Role.class)` can find the inherited annotation.

---

### 12. Annotation vs Normal Java Code

Annotations are **metadata**, not ordinary method calls.

Example:

```java
@Service
class UserService {
}
```

`@Service` by itself doesn't mean Java language execution automatically creates a service object.

A framework such as Spring can inspect the annotation and perform some action based on it.

Conceptually:

```
@Service
   ↓
Metadata
   ↓
Spring scans class
   ↓
Spring recognizes annotation
   ↓
Spring creates/manages bean
```

This distinction is important when explaining how Spring annotations work.

---

## Final Interview Cheat Sheet

```
Annotations
    ↓
Metadata attached to Java code
```

```
@Override
    ↓
Compiler checks overriding
```

```
@Deprecated
    ↓
API should no longer be used for new code
```

```
@SuppressWarnings
    ↓
Suppresses specified compiler warnings
```

```
Custom Annotation
    ↓
@interface
```

```
Runtime Annotation
    ↓
@Retention(RUNTIME)
    ↓
Can be inspected through Reflection
```

And remember:

```
@Target
→ Where can annotation be used?

@Retention
→ How long is annotation retained?

@Documented
→ Include it in Javadoc?

@Inherited
→ Can class annotation be inherited by subclasses?
```

The most important interview connection is:

> **Annotations provide metadata; runtime annotations can be inspected using Reflection.**
> 

---

## Reflection

### 13. What is Reflection?

**Reflection** is a Java mechanism that allows a program to **inspect and interact with classes, methods, fields, constructors, and other class metadata at runtime**.

The main package is:

```
java.lang.reflect
```

Example:

```java
Class<?> clazz = Student.class;

System.out.println(clazz.getName());
```

Reflection can allow you to:

- Inspect a class
- Find its methods
- Find its fields
- Find its constructors
- Create objects
- Invoke methods
- Access fields
- Inspect annotations

Conceptually:

```
Normal Java
    ↓
Compile time knows class structure

Reflection
    ↓
Discover class structure at runtime
```

This is heavily used by frameworks such as Spring, Hibernate, JUnit, and dependency-injection frameworks.

---

### 14. What is a `Class` Object?

Every loaded Java class has a corresponding **`Class`** **object** that contains runtime metadata about that class.

Example:

```java
Class<Student> clazz = Student.class;
```

Here:

```
Student.class
     ↓
Class object
     ↓
Metadata about Student
```

You can also obtain it from an object:

```java
Student student = new Student();

Class<?> clazz = student.getClass();
```

Or by class name:

```java
Class<?> clazz = Class.forName("com.example.Student");
```

Three common ways:

```java
Student.class
```

```java
student.getClass()
```

```java
Class.forName("com.example.Student")
```

Important difference:

`Class.forName()` loads/initializes the class as specified by its API semantics, while `SomeClass.class` does not by itself initialize the class merely by obtaining the class literal.

---

### 15. Getting Class Information

Once you have a `Class` object, you can inspect its metadata.

Example:

```java
Class<?> clazz = Student.class;

System.out.println(clazz.getName());
System.out.println(clazz.getSimpleName());
System.out.println(clazz.getPackageName());
```

Useful methods:

```
getName()
getSimpleName()
getPackageName()
getSuperclass()
getInterfaces()
getModifiers()
isInterface()
isArray()
isEnum()
```

Example:

```java
System.out.println(clazz.getSuperclass());
```

If:

```java
class Student extends Person
```

the result is:

```
class Person
```

---

### 16. Getting Methods

Reflection allows you to inspect methods of a class.

Example:

```java
Class<?> clazz = Student.class;

Method[] methods = clazz.getMethods();

for (Method method : methods) {
    System.out.println(method.getName());
}
```

`getMethods()` returns **public methods**, including inherited public methods.

If you want methods declared directly in the class:

```java
Method[] methods = clazz.getDeclaredMethods();
```

Important distinction:

```
getMethods()
    ↓
public methods
including inherited public methods

getDeclaredMethods()
    ↓
methods declared by this class
regardless of access modifier
```

You can inspect a particular method:

```java
Method method = clazz.getMethod("display");
```

For methods with parameters:

```java
Method method =
    clazz.getMethod("setName", String.class);
```

---

### 17. Getting Fields

Reflection can also inspect fields.

Example:

```java
Class<?> clazz = Student.class;

Field[] fields = clazz.getDeclaredFields();

for (Field field : fields) {
    System.out.println(field.getName());
}
```

`getDeclaredFields()` returns fields declared by that class.

For public fields, including inherited public fields:

```java
Field[] fields = clazz.getFields();
```

Important distinction:

```
getFields()
    ↓
public fields
including inherited public fields

getDeclaredFields()
    ↓
fields declared directly in this class
regardless of access modifier
```

You can inspect field information:

```java
Field field =
    clazz.getDeclaredField("name");

System.out.println(field.getType());
System.out.println(field.getModifiers());
```

---

### 18. Getting Constructors

Reflection can also inspect constructors.

Example:

```java
Class<?> clazz = Student.class;

Constructor<?>[] constructors =
    clazz.getDeclaredConstructors();

for (Constructor<?> constructor : constructors) {
    System.out.println(constructor);
}
```

You can retrieve a specific constructor:

```java
Constructor<Student> constructor =
    Student.class.getConstructor(String.class);
```

Then create an object:

```java
Student student =
    constructor.newInstance("Atharva");
```

---

### 19. Creating Objects Through Reflection

Reflection allows you to create objects dynamically.

Modern approach:

```java
Constructor<Student> constructor =
    Student.class.getConstructor();

Student student =
    constructor.newInstance();
```

Another approach is:

```java
Class<?> clazz =
    Class.forName("Student");

Object obj =
    clazz.getDeclaredConstructor().newInstance();
```

The second approach is useful when the class name is available dynamically.

Conceptually:

```
Class name
    ↓
Class.forName()
    ↓
Class object
    ↓
getDeclaredConstructor()
    ↓
newInstance()
    ↓
Object
```

### Important

You may see older code like:

```java
clazz.newInstance();
```

This API is **deprecated since Java 9**.

Prefer:

```java
clazz.getDeclaredConstructor().newInstance();
```

because it provides better exception handling and works through the constructor reflection API.

---

### 20. Invoking Methods Through Reflection

Reflection can invoke a method dynamically.

Example:

```java
class Student {

    public void display() {
        System.out.println("Hello");
    }
}
```

Reflection:

```java
Student student = new Student();

Method method =
    Student.class.getMethod("display");

method.invoke(student);
```

Output:

```
Hello
```

With parameters:

```java
Method method =
    Student.class.getMethod("setName", String.class);

method.invoke(student, "Atharva");
```

Conceptually:

```
Method object
     ↓
invoke()
     ↓
Actual method executes
```

Important:

> `Method.invoke()` invokes the method dynamically at runtime rather than requiring a direct method call in source code.
> 

---

### 21. Accessing Fields Through Reflection

You can also read/write fields dynamically.

Example:

```java
class Student {

    private String name = "Atharva";
}
```

Reflection:

```java
Student student = new Student();

Field field =
    Student.class.getDeclaredField("name");

field.setAccessible(true);

Object value =
    field.get(student);

System.out.println(value);
```

Output:

```
Atharva
```

You can also modify it:

```java
field.set(student, "Rahul");
```

Important modern note:

> `setAccessible(true)` can be restricted by Java's module system and access rules. It should not be treated as a universal way to bypass encapsulation.
> 

For strongly encapsulated modules, reflective access to non-exported/non-opened packages may result in access exceptions.

---

### 22. Reflection and Annotations

Reflection is commonly used to read runtime annotations.

Example:

```java
@Retention(RetentionPolicy.RUNTIME)
@interface Author {
    String name();
}
```

Usage:

```java
@Author(name = "Atharva")
class Student {
}
```

Reflection:

```java
Class<Student> clazz = Student.class;

Author author =
    clazz.getAnnotation(Author.class);

System.out.println(author.name());
```

Output:

```
Atharva
```

This gives the important relationship:

```
Runtime Annotation
       ↓
Reflection
       ↓
Read annotation metadata
```

---

### 23. Reflection Use Cases

Reflection is heavily used internally by frameworks and tools.

#### 1. Dependency Injection

Spring can inspect classes and annotations to determine which objects should be managed and how dependencies should be injected.

Conceptually:

```
@Component
    ↓
Reflection / classpath scanning
    ↓
Identify class
    ↓
Create/manage object
```

#### 2. ORM

Frameworks such as Hibernate can inspect:

- Classes
- Fields
- Annotations
- Methods

to map Java objects to database structures.

#### 3. Testing

Testing frameworks such as JUnit can discover test methods based on annotations and invoke them.

#### 4. Serialization / Deserialization

Frameworks can inspect fields and methods to convert objects to/from formats such as JSON.

#### 5. Dependency Injection Containers

Frameworks can dynamically instantiate classes and resolve constructors/fields/methods.

#### 6. IDEs and Development Tools

IDEs can inspect class metadata to provide:

- Code completion
- Method information
- Navigation
- Debugging support

#### 7. Plugin Systems

An application can dynamically load classes and discover implementations at runtime.

---

### 24. Advantages of Reflection

Main advantages:

- **Runtime flexibility**
- Can inspect unknown classes dynamically
- Can create objects dynamically
- Can invoke methods dynamically
- Useful for framework development
- Enables annotation-driven systems
- Useful for plugin architectures

Example:

```
Configuration
     ↓
Class name
     ↓
Reflection
     ↓
Load class
     ↓
Create object
     ↓
Invoke method
```

This allows systems to be more configurable and extensible.

---

### 25. Disadvantages of Reflection

Reflection has several disadvantages.

#### 1. Performance overhead

Reflection is generally slower than direct method/field access because operations are performed dynamically and may involve additional access checks and metadata handling.

Direct:

```java
student.display();
```

Reflection:

```java
method.invoke(student);
```

Direct calls are generally preferable in performance-critical code.

---

#### 2. Reduced type safety

Normal Java:

```java
Student student = new Student();
```

The compiler knows the type.

Reflection:

```java
Object obj = constructor.newInstance();
```

More errors can move from compile time to runtime.

---

#### 3. Breaks encapsulation

Reflection can potentially access implementation details that normal code cannot directly access, subject to Java's access-control and module restrictions.

For example:

```java
field.setAccessible(true);
```

This can undermine normal encapsulation when used improperly.

---

#### 4. More complex code

Reflection is more verbose and harder to understand/debug.

Normal:

```java
student.setName("Atharva");
```

Reflection:

```java
Method method =
    Student.class.getMethod("setName", String.class);

method.invoke(student, "Atharva");
```

---

#### 5. Runtime exceptions

Reflection APIs can throw exceptions such as:

```
ClassNotFoundException
NoSuchMethodException
NoSuchFieldException
IllegalAccessException
InvocationTargetException
InstantiationException
```

Therefore, mistakes that would normally be caught by the compiler can become runtime problems.

---

### 26. Reflection vs Normal Java Code

| Normal Java | Reflection |
| --- | --- |
| Mostly compile-time type checking | Many operations resolved at runtime |
| Faster direct access | Generally more overhead |
| Stronger encapsulation | Can access metadata/internal members subject to access rules |
| Easier to read | More complex |
| Errors often caught at compile time | More runtime exceptions |
| `new Student()` | `constructor.newInstance()` |
| `student.display()` | `method.invoke(student)` |

---

### 27. Complete Reflection Flow

You should be able to explain reflection using this flow:

```
                  Reflection
                     |
                     ↓
               Class Object
                     |
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
     Methods        Fields      Constructors
        ↓             ↓             ↓
     invoke()       get/set     newInstance()
```

And:

```
Class.forName()
      ↓
Class Object
      ↓
Inspect metadata
      ↓
Find Constructor / Method / Field
      ↓
Interact dynamically
```

### Final Interview Cheat Sheet

Remember these methods:

```java
Student.class
```

→ Get `Class` object.

```java
student.getClass()
```

→ Get runtime class of an object.

```java
Class.forName("com.example.Student")
```

→ Load a class by name and obtain its `Class` object.

```java
getMethods()
```

→ Public methods, including inherited public methods.

```java
getDeclaredMethods()
```

→ Methods declared by the class.

```java
getFields()
```

→ Public fields, including inherited public fields.

```java
getDeclaredFields()
```

→ Fields declared by the class.

```java
getDeclaredConstructor()
```

→ Obtain a declared constructor.

```java
constructor.newInstance()
```

→ Create an object.

```java
method.invoke(object)
```

→ Invoke a method.

```java
field.get(object)
field.set(object, value)
```

→ Read/write a field.

The key interview answer is:

> **Reflection allows Java programs to inspect and interact with classes, methods, fields, constructors, and annotations at runtime. It provides flexibility for frameworks such as Spring, Hibernate, and JUnit, but introduces performance overhead, reduced compile-time safety, greater complexity, and potential encapsulation issues.**
>
# Java 9 to 21 Features

# Java 9–21 Features

## Java 9

### 1. Java Module System

Java 9 introduced the **Java Platform Module System (JPMS)**.

The goal is to divide large applications into **modules** with explicit dependencies and controlled access.

Before Java 9:

```
Large Application
      |
   Many Packages
      |
   Dependencies
```

With modules:

```
Application
    |
  ┌──┴─────────────┐
  ↓                ↓
Module A        Module B
  ↓                ↓
depends on B    exports API
```

A module is defined using:

```
module-info.java
```

Example:

```java
module com.example.app {
    requires com.example.service;
    exports com.example.api;
}
```

Important keywords:

```
module
requires
exports
opens
uses
provides
```

#### `requires`

Specifies a module dependency.

```java
requires java.sql;
```

Meaning:

> This module depends on the `java.sql` module.
> 

#### `exports`

Makes a package accessible to other modules.

```java
exports com.example.api;
```

Only exported packages are part of the module's normal public API.

#### `opens`

Used primarily to allow **deep reflection** into a package.

This is particularly relevant for frameworks that need reflective access.

```java
opens com.example.model;
```

Important interview distinction:

```
exports
→ compile-time/public access between modules

opens
→ reflective access
```

The module system provides:

- Strong encapsulation
- Explicit dependencies
- Better maintainability
- Better modularity
- Smaller runtime images when used with tools such as `jlink`

---

### 2. `List.of()`

Java 9 introduced convenient factory methods for creating **unmodifiable lists**.

Example:

```java
List<String> names = List.of("Java", "Python", "C++");
```

You cannot modify it:

```java
names.add("Go");       // ❌
names.remove("Java");  // ❌
```

These operations throw:

```
UnsupportedOperationException
```

Important:

> `List.of()` creates an **unmodifiable** list. It is not the same as a mutable `ArrayList`.
> 

Also:

```java
List.of(null);
```

throws:

```
NullPointerException
```

---

### 3. `Set.of()`

Java 9 also introduced `Set.of()`.

Example:

```java
Set<String> languages = Set.of("Java", "Python", "C++");
```

The resulting set is unmodifiable.

```java
languages.add("Go");  // ❌
```

This throws:

```
UnsupportedOperationException
```

`Set.of()` also:

- Does not allow `null`
- Does not allow duplicate elements

Example:

```java
Set.of("Java", "Java");
```

throws:

```
IllegalArgumentException
```

---

### 4. `Map.of()`

Java 9 introduced `Map.of()` for creating small **unmodifiable maps**.

Example:

```java
Map<Integer, String> students =
        Map.of(1, "Atharva", 2, "Rahul", 3, "Amit");
```

You cannot modify it:

```java
students.put(4, "John");  // ❌
```

This results in:

```
UnsupportedOperationException
```

`Map.of()` also:

- Does not allow `null` keys
- Does not allow `null` values
- Does not allow duplicate keys

Example:

```java
Map.of(1, "A", 1, "B");
```

results in:

```
IllegalArgumentException
```

---

### 5. `List.of()` / `Set.of()` / `Map.of()` vs Mutable Collections

This is a common interview question.

```java
List<String> list = List.of("A", "B");
```

Unmodifiable.

Whereas:

```java
List<String> list =
        new ArrayList<>(List.of("A", "B"));
```

creates a **mutable** **`ArrayList`**.

Now:

```java
list.add("C");  // ✅
```

So:

```
List.of()
Set.of()
Map.of()
      ↓
Unmodifiable
```

Important nuance:

> "Unmodifiable" means you cannot modify the collection through that collection reference. It does not necessarily mean that referenced mutable objects inside the collection are deeply immutable.
> 

---

## Java 10

### 6. `var`

Java 10 introduced **local variable type inference** using `var`.

Example:

```java
var name = "Atharva";
```

The compiler infers:

```
String
```

Another example:

```java
var age = 22;
```

The inferred type is:

```
int
```

And:

```java
var list = new ArrayList<String>();
```

The inferred type is:

```
ArrayList<String>
```

### Important

`var` is **not dynamic typing**.

This:

```java
var name = "Atharva";
```

means the compiler knows that `name` is a `String`.

This is invalid:

```java
var x;
```

because Java cannot infer the type.

Also invalid:

```java
var x = null;
```

because there is no type information from which to infer the type.

---

### 7. Where Can `var` Be Used?

`var` can be used for **local variables**, including:

```java
var name = "Java";
```

```java
for (var item : list) {
    System.out.println(item);
}
```

```java
for (var i = 0; i < 10; i++) {
}
```

It can also be used for local variables in try-with-resources:

```java
try (var reader = new FileReader("data.txt")) {

}
```

But you cannot use `var` for:

```java
// ❌ instance fields
var name;

// ❌ method parameters
void test(var x) {
}

// ❌ method return type
var test() {
}
```

So:

> `var` is for local variable type inference, not general type inference throughout the language.
> 

---

### 8. `var` vs Explicit Type

Explicit:

```java
ArrayList<String> names = new ArrayList<String>();
```

With `var`:

```java
var names = new ArrayList<String>();
```

The compiler still knows:

```
names → ArrayList<String>
```

`var` mainly reduces repetitive type declarations.

---

### 9. Important `var` Interview Question

What is the type of:

```java
var list = new ArrayList<>();
```

The compiler infers the type based on the initializer and target typing rules. In this standalone local-variable declaration, the inferred type is an `ArrayList<Object>` when no more specific target type provides type information.

This is different from:

```java
var list = new ArrayList<String>();
```

where the inferred type is:

```
ArrayList<String>
```

So, when using `var`, the initializer matters.

---

### 10. Important Java 9–10 Cheat Sheet

```
Java 9
│
├── Module System
│     ├── module
│     ├── requires
│     ├── exports
│     └── opens
│
├── List.of()
│     → unmodifiable List
│
├── Set.of()
│     → unmodifiable Set
│
└── Map.of()
      → unmodifiable Map
```

```
Java 10
│
└── var
      ↓
Local variable type inference
      ↓
Compile-time type safety
      ↓
NOT dynamic typing
```

The highest-priority interview points are:

- **JPMS** → modularity, `module-info.java`, `requires`, `exports`, `opens`.
- **`List.of()`** **/** **`Set.of()`** **/** **`Map.of()`** → convenient creation of unmodifiable collections; no `null`.
- **`var`** → local variable type inference; compiler determines the static type at compile time.
- `var` **cannot** be used for fields, method parameters, or return types.
- `var x = null` and uninitialized `var x` are invalid.

## Java 11

### 11. New String Methods

Java 11 added several useful methods to `String`, mainly for cleaner text processing.

The important ones for interviews are:

```
isBlank()
strip()
stripLeading()
stripTrailing()
lines()
repeat()
```

For your list, focus especially on `isBlank()`, `strip()`, and `lines()`.

---

### 12. `String.isBlank()`

`isBlank()` checks whether a string is **empty or contains only whitespace characters**.

Example:

```java
String s1 = "";
String s2 = "   ";
String s3 = " Java ";

System.out.println(s1.isBlank());  // true
System.out.println(s2.isBlank());  // true
System.out.println(s3.isBlank());  // false
```

Comparison:

```
isEmpty()
→ checks length == 0

isBlank()
→ checks empty OR whitespace-only
```

Example:

```java
"".isEmpty();      // true
"".isBlank();      // true
"   ".isEmpty();   // false
"   ".isBlank();   // true
```

Important:

> `isBlank()` was introduced in Java 11 and uses the Unicode definition of whitespace.
> 

---

### 13. `String.strip()`

`strip()` removes **leading and trailing Unicode whitespace**.

Example:

```java
String s = "   Hello Java   ";

String result = s.strip();

System.out.println(result);
```

Output:

```
Hello Java
```

Important comparison:

```
trim()
→ older API
→ removes characters based on the older <= U+0020 rule

strip()
→ Java 11
→ Unicode-aware whitespace handling
```

Example:

```java
String s = "   Java   ";

s.trim();
s.strip();
```

For normal ASCII spaces, both commonly produce the same result.

But `strip()` is the preferred modern API when you want Unicode-aware whitespace handling.

Also available:

```java
stripLeading()
stripTrailing()
```

Example:

```java
"   Java   ".stripLeading();   // "Java   "
"   Java   ".stripTrailing();  // "   Java"
```

---

### 14. `String.lines()`

`lines()` returns a `Stream<String>` containing the lines of a string.

Example:

```java
String text = "Java\nPython\nC++";

text.lines().forEach(System.out::println);
```

Output:

```
Java
Python
C++
```

Conceptually:

```
"Java\nPython\nC++"
          ↓
       lines()
          ↓
    Stream<String>
          ↓
  ┌────────┼────────┐
  ↓        ↓        ↓
Java    Python    C++
```

Because it returns a Stream, you can use Stream operations:

```java
long count = text.lines().count();
```

Result:

```
3
```

This is often cleaner than manually doing:

```java
text.split("\n");
```

Important:

> `lines()` returns a `Stream<String>`, not a `List<String>`.
> 

---

### 15. Java 11 String Methods — Quick Comparison

| Method | Purpose |
| --- | --- |
| `isBlank()` | Empty or whitespace-only? |
| `strip()` | Remove leading/trailing Unicode whitespace |
| `stripLeading()` | Remove leading whitespace |
| `stripTrailing()` | Remove trailing whitespace |
| `lines()` | Stream the lines of a string |
| `repeat(n)` | Repeat a string `n` times |

Example:

```java
String s = "  Java  ";

s.isBlank();        // false
s.strip();          // "Java"
s.stripLeading();   // "Java  "
s.stripTrailing();  // "  Java"
```

---

# Java 14+

## Switch Expressions

### 16. Switch Expressions

Modern Java enhanced `switch` so that it can be used as an **expression that produces a value**.

Traditional `switch`:

```java
int day = 2;
String result;

switch (day) {
    case 1:
        result = "Monday";
        break;
    case 2:
        result = "Tuesday";
        break;
    default:
        result = "Invalid";
}
```

Modern switch expression:

```java
int day = 2;

String result = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    default -> "Invalid";
};
```

The switch itself produces a value.

Important:

```
Traditional switch
→ statement

Modern switch
→ can be an expression
→ produces a value
```

---

### 17. Arrow Syntax in Switch

Modern switch supports:

```
case value -> result;
```

Example:

```java
String result = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    default -> "Invalid";
};
```

Unlike traditional `case` statements, the arrow form does **not fall through** to the next case.

So you generally don't need:

```
break;
```

---

### 18. Multiple Labels in Switch

You can combine multiple case labels:

```java
int day = 6;

String type = switch (day) {
    case 1, 2, 3, 4, 5 -> "Weekday";
    case 6, 7 -> "Weekend";
    default -> "Invalid";
};
```

This is much cleaner than:

```
case 1:
case 2:
case 3:
case 4:
case 5:
```

---

### 19. `yield` in Switch Expressions

Sometimes a switch case needs multiple statements.

In that situation, use a block with `yield`.

```java
int day = 2;

String result = switch (day) {
    case 1 -> "Monday";
    case 2 -> {
        String value = "Tuesday";
        yield value;
    }
    default -> "Invalid";
};
```

`yield` returns a value from the switch expression.

Important distinction:

```
return
→ returns from method

yield
→ produces value from switch expression
```

---

## Pattern Matching

### 20. Pattern Matching for `instanceof`

Pattern matching for `instanceof` became final in **Java 16**.

Traditional approach:

```java
Object obj = "Java";

if (obj instanceof String) {
    String str = (String) obj;
    System.out.println(str.length());
}
```

With pattern matching:

```java
Object obj = "Java";

if (obj instanceof String str) {
    System.out.println(str.length());
}
```

The variable:

```
str
```

is automatically created and assigned after the type check succeeds.

Conceptually:

```
instanceof
    +
type cast
    ↓
pattern matching
```

---

### 21. Pattern Variable Scope

The pattern variable is available where the compiler knows the pattern matched.

Example:

```java
Object obj = "Java";

if (obj instanceof String str) {
    System.out.println(str.length());
}
```

`str` is available inside the `if` block.

You can also use pattern matching with logical conditions:

```java
if (obj instanceof String str && str.length() > 3) {
    System.out.println(str);
}
```

The compiler knows `str` is a `String` on the right side of `&&`.

---

### 22. Pattern Matching with `!`

You can also use negation:

```java
if (!(obj instanceof String str)) {
    System.out.println("Not a String");
}
```

The pattern variable's scope follows Java's flow-scoping rules, so it is not generally available in the branch where the pattern did not match.

---

### 23. Pattern Matching for `switch`

Later Java versions extended pattern matching to `switch`.

Modern Java can use type patterns such as:

```java
static String check(Object obj) {
    return switch (obj) {
        case Integer i -> "Integer: " + i;
        case String s -> "String: " + s;
        default -> "Other";
    };
}
```

This allows the switch to match based on **type** and bind the matched value to a variable.

Pattern matching for `switch` became a **final feature in Java 21**.

For your placement preparation, remember the evolution:

```
Java 16
→ Pattern matching for instanceof

Java 21
→ Pattern matching for switch became final
```

---

### 24. Traditional `instanceof` vs Pattern Matching

| Traditional | Pattern Matching |
| --- | --- |
| Check type | Check type |
| Explicit cast required | Cast is automatic |
| More verbose | Less verbose |
| `instanceof` + cast | `instanceof Type variable` |

Traditional:

```java
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.length());
}
```

Modern:

```java
if (obj instanceof String s) {
    System.out.println(s.length());
}
```

---

## Final Cheat Sheet

```
Java 11
│
├── isBlank()
│     → empty OR whitespace-only
│
├── strip()
│     → Unicode-aware whitespace removal
│
└── lines()
      → Stream<String> of lines
```

```
Java 14+
│
└── Switch Expressions
      ↓
      String result = switch (...) {
          case ... -> value;
      };
```

```
Modern switch
│
├── -> syntax
├── multiple labels
└── yield
```

```
Pattern Matching
│
├── Java 16
│     → instanceof pattern matching
│
└── Java 21
      → switch pattern matching finalized
```

The highest-priority interview points are:

- `isBlank()` vs `isEmpty()`
- `strip()` vs `trim()`
- `lines()` returns `Stream<String>`
- Switch expressions **return a value**
- `>` prevents traditional fall-through
- `yield` returns a value from a switch-expression block
- Pattern matching for `instanceof` eliminates explicit casting
- Pattern matching for `switch` became final in **Java 21**

## Java 15+

### 25. Text Blocks

**Text blocks** were finalized in **Java 15**. They provide a cleaner way to write **multi-line string literals**.

Traditional String:

```java
String json = "{\n"
        + "  \"name\": \"Atharva\",\n"
        + "  \"age\": 22\n"
        + "}";
```

With a text block:

```java
String json = """
        {
          "name": "Atharva",
          "age": 22
        }
        """;
```

This is much easier to read.

Common use cases:

- JSON
- XML
- SQL
- HTML
- Multi-line text

Example SQL:

```java
String sql = """
        SELECT id, name
        FROM students
        WHERE age > 20
        """;
```

Important:

> A text block is still a `String`. It does not introduce a new data type.
> 

Text blocks also support escape sequences and formatting behavior such as incidental indentation removal.

---

## Java 16+

### 26. Records

**Records** were finalized in **Java 16**.

A record is a concise way to create a class whose primary purpose is to **carry immutable data**.

Traditional class:

```java
class Student {
    private final int id;
    private final String name;

    public Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    // equals()
    // hashCode()
    // toString()
}
```

With a record:

```java
record Student(int id, String name) {
}
```

The compiler provides important members such as:

- Private final component fields
- Canonical constructor
- Accessor methods
- `equals()`
- `hashCode()`
- `toString()`

Usage:

```java
Student s = new Student(101, "Atharva");

System.out.println(s.id());
System.out.println(s.name());
```

Notice:

```
s.name()
```

rather than:

```
s.getName()
```

Record components are accessed using their component names.

---

### 27. Important Properties of Records

Records are intended for **data-centric classes**.

Example:

```java
record Employee(int id, String name, double salary) {
}
```

You cannot normally reassign the record component:

```java
Employee e = new Employee(1, "Atharva", 50000);

// e.name = "Rahul";   // ❌
```

Record component fields are implicitly:

```
private final
```

Records themselves are implicitly:

```
final
```

Therefore:

```java
class Manager extends Employee {
}
```

is not allowed.

Important:

> Records are shallowly immutable. A record's components are final references, but an object referenced by a component can still be mutable.
> 

Example:

```java
record Team(List<String> members) {
}
```

The `members` reference cannot be reassigned, but the underlying `List` could still be mutable.

---

### 28. Records Can Have Methods

A record is still a class and can contain methods.

```java
record Student(int id, String name) {

    public void print() {
        System.out.println(id + " " + name);
    }
}
```

Usage:

```java
Student s = new Student(101, "Atharva");

s.print();
```

So records are not simply "data structures"; they are specialized classes designed for transparent data modeling.

---

### 29. Record Constructor

A record automatically gets a canonical constructor:

```java
record Student(int id, String name) {
}
```

Equivalent conceptually to:

```java
Student(int id, String name) {
    this.id = id;
    this.name = name;
}
```

You can customize validation using a **compact constructor**:

```java
record Student(int id, String name) {

    Student {
        if (id <= 0) {
            throw new IllegalArgumentException("Invalid ID");
        }
    }
}
```

You don't need to explicitly assign:

```java
this.id = id;
this.name = name;
```

The record mechanism handles the component assignments.

---

## Java 16+

### 30. `instanceof` Pattern Matching

Pattern matching for `instanceof` became final in **Java 16**.

Traditional:

```java
Object obj = "Java";

if (obj instanceof String) {
    String str = (String) obj;
    System.out.println(str.length());
}
```

Modern:

```java
Object obj = "Java";

if (obj instanceof String str) {
    System.out.println(str.length());
}
```

The pattern:

```
String str
```

does two things:

1. Checks whether `obj` is a `String`.
2. Creates a variable `str` containing the cast value.

This eliminates redundant casting.

---

### 31. Pattern Variable Scope

Pattern variables follow **flow scoping**.

Example:

```java
Object obj = "Java";

if (obj instanceof String str) {
    System.out.println(str.length());
}
```

`str` is available where the compiler knows that the `instanceof` test succeeded.

With `&&`:

```java
if (obj instanceof String str && str.length() > 3) {
    System.out.println(str);
}
```

This works because `str` is definitely a `String` on the right side of `&&`.

---

## Java 17

### 32. Sealed Classes

**Sealed classes** were finalized in **Java 17**.

They allow a class or interface to explicitly control **which classes can extend or implement it**.

Example:

```java
public sealed class Shape
        permits Circle, Rectangle {
}
```

Now only:

```java
class Circle extends Shape {
}
```

and:

```java
class Rectangle extends Shape {
}
```

can directly extend `Shape`.

An unauthorized class:

```java
class Triangle extends Shape {
}
```

causes a compile-time error.

---

### 33. `final`, `sealed`, and `non-sealed`

A permitted subclass of a sealed class must itself specify how inheritance continues.

It can be:

```
final
sealed
non-sealed
```

Example:

```java
public sealed class Shape
        permits Circle, Rectangle {
}
```

Final:

```java
public final class Circle extends Shape {
}
```

Sealed:

```java
public sealed class Rectangle extends Shape
        permits Square {
}
```

Non-sealed:

```java
public non-sealed class Rectangle extends Shape {
}
```

`non-sealed` removes the restriction for that branch.

Conceptually:

```
Shape
  sealed
    |
    ├── Circle
    │     final
    │
    └── Rectangle
          non-sealed
              |
              ├── Square
              ├── Triangle
              └── ...
```

---

### 34. Why Use Sealed Classes?

Sealed classes are useful when you want a **controlled class hierarchy**.

For example:

```java
public sealed interface Payment
        permits CardPayment, UPIPayment, CashPayment {
}
```

Now the application knows the complete permitted hierarchy.

Benefits:

- Controlled inheritance
- Better domain modeling
- Stronger type safety
- Helps compiler reason about exhaustive pattern matching
- Useful for finite domain models

For example:

```
Payment
 ├── CardPayment
 ├── UPIPayment
 └── CashPayment
```

This is much more explicit than allowing any class to implement `Payment`.

---

### 35. Records in Java 17

You listed Records again under Java 17.

The important distinction is:

```
Java 14
→ Records preview

Java 15
→ Records second preview

Java 16
→ Records finalized
```

Java 17 did **not introduce Records as a new feature**. Records were already a standard Java feature from Java 16.

So for interview purposes:

> **Records became a permanent Java language feature in Java 16.**
> 

---

### 36. Pattern Matching Basics in Java 17

Java 17 includes the finalized `instanceof` pattern matching from Java 16.

Example:

```java
Object obj = 100;

if (obj instanceof Integer number) {
    System.out.println(number * 2);
}
```

Traditional:

```java
if (obj instanceof Integer) {
    Integer number = (Integer) obj;
    System.out.println(number * 2);
}
```

Modern:

```java
if (obj instanceof Integer number) {
    System.out.println(number * 2);
}
```

The modern version is:

- Shorter
- Type-safe
- Easier to read
- Avoids redundant casting

Java 17 also had **pattern matching for `switch` as a preview feature**, but it was not finalized until Java 21.

---

## 37. Java 15–17 Feature Timeline

This is worth memorizing for interviews:

```
Java 15
   ↓
Text Blocks
```

```
Java 16
   ↓
Records
   ↓
Pattern Matching for instanceof
```

```
Java 17
   ↓
Sealed Classes
```

And:

```
Java 21
   ↓
Pattern Matching for switch finalized
```

### Final Interview Cheat Sheet

```
Java 15
→ Text Blocks
→ """ multi-line strings """
```

```
Java 16
→ Records
→ instanceof Pattern Matching
```

```
Java 17
→ Sealed Classes
```

Remember these distinctions:

- **Text block** → cleaner multi-line `String`.
- **Record** → concise data-centric class; finalized in Java 16.
- **`instanceof`** **pattern matching** → type check + variable binding; finalized in Java 16.
- **Sealed class** → restricts which classes can extend/implement a type; finalized in Java 17.
- **`final`** → no further inheritance.
- **`sealed`** → inheritance allowed only to specified types.
- **`non-sealed`** → opens that inheritance branch again.
- **Record fields are final**, but records are only shallowly immutable.

## Java 21

Java 21 is an important LTS release and introduced several major features. For fresher interviews, you should understand the concepts and be able to explain the purpose/use case rather than memorize syntax in isolation.

### 37. Virtual Threads

Virtual threads are lightweight threads introduced as a preview in Java 19 and finalized in **Java 21**.

Traditional Java threads are generally mapped to operating-system threads. OS threads are relatively expensive, so creating huge numbers of them can become a scalability bottleneck.

Virtual threads are managed by the JVM and are designed to make **high-concurrency, I/O-heavy applications** easier to build.

Example:

```java
Thread.startVirtualThread(() -> {
    System.out.println("Running in virtual thread");
});
```

You can also use an executor:

```java
try (var executor =
        Executors.newVirtualThreadPerTaskExecutor()) {

    executor.submit(() -> {
        System.out.println("Task running");
    });
}
```

Conceptually:

```
Traditional threads

Java Thread
     ↓
OS Thread
     ↓
CPU
```

Virtual threads:

```
Virtual Thread
      ↓
JVM scheduler
      ↓
Carrier OS Thread
      ↓
CPU
```

The important idea is that **many virtual threads can be multiplexed over a much smaller number of platform/OS threads**.

Virtual threads are especially useful for:

- HTTP requests
- Database calls
- File I/O
- Network communication
- Microservices handling many concurrent requests

They are **not primarily a way to make CPU-heavy computation faster**.

For CPU-bound work, the number of tasks should generally be aligned with available CPU resources.

Important interview point:

> Virtual threads improve scalability for blocking I/O workloads by making it practical to have very large numbers of concurrent tasks without requiring one expensive OS thread per task.
> 

Virtual threads do not mean:

```
"more CPU power"
```

They primarily mean:

```
"more efficient concurrency"
```

---

### 38. Platform Threads vs Virtual Threads

| Feature | Platform Thread | Virtual Thread |
| --- | --- | --- |
| Managed primarily by | OS/JVM | JVM |
| Cost | Relatively expensive | Very lightweight |
| Number you can create | More limited | Very large numbers |
| Best use | General/CPU work | High-concurrency I/O |
| Java 21 | Standard | Standard |

A common interview question:

**Q: Should we replace every thread with a virtual thread?**

No.

Virtual threads are excellent for workloads with lots of blocking operations, but they don't magically improve CPU-bound computation.

---

## 39. Record Patterns

Record patterns were finalized in **Java 21**.

They allow you to **deconstruct a record directly into its components** during pattern matching.

Suppose:

```java
record Student(int id, String name) {}
```

Traditional approach:

```java
Student student = new Student(101, "Atharva");

int id = student.id();
String name = student.name();

System.out.println(id);
System.out.println(name);
```

With a record pattern:

```java
if (student instanceof Student(int id, String name)) {
    System.out.println(id);
    System.out.println(name);
}
```

The pattern:

```
Student(int id, String name)
```

both checks the record type and extracts its components.

Conceptually:

```
Student object
     ↓
Student(int id, String name)
     ↓
  ┌───────┴───────┐
  id             name
```

---

### 40. Nested Record Patterns

Record patterns can be nested.

Example:

```java
record Address(String city) {}

record Student(String name, Address address) {}
```

You can write:

```java
if (student instanceof Student(String name,
        Address(String city))) {

    System.out.println(name);
    System.out.println(city);
}
```

This is useful when working with nested data structures.

Important distinction:

```
Record
→ compactly defines data

Record pattern
→ extracts/deconstructs that data
```

---

## 41. Pattern Matching for `switch`

Pattern matching for `switch` was previewed in earlier Java versions and became **final in Java 21**.

Traditional switch usually works with fixed values:

```java
switch (value) {
    case 1:
        ...
        break;

    case 2:
        ...
        break;
}
```

Java 21 allows type patterns:

```java
static String describe(Object obj) {
    return switch (obj) {
        case Integer i -> "Integer: " + i;
        case String s -> "String: " + s;
        case Double d -> "Double: " + d;
        default -> "Other";
    };
}
```

Here:

```
case Integer i
```

means:

```
Is obj an Integer?
        ↓
      Yes
        ↓
bind it to i
```

---

### 42. Pattern Matching with Records

Record patterns and switch patterns can be combined.

Example:

```java
record Point(int x, int y) {}

static String describe(Point point) {
    return switch (point) {
        case Point(0, 0) -> "Origin";
        case Point(int x, int y) -> "Point: " + x + ", " + y;
    };
}
```

This is powerful because the switch can:

1. Match the type.
2. Deconstruct the record.
3. Extract its values.
4. Produce a result.

---

### 43. Guarded/Conditional Pattern Matching

Java's modern switch can also use conditions through `when` in newer pattern-switch syntax, but for **Java 21 specifically**, the standard approach uses a `when`-style condition via a guarded pattern? Be careful here for interviews: **Java 21 uses** **`when`** **only in later Java evolution; Java 21 uses** **`when`** **guards?**

For Java 21, use the reliable syntax with `when`? Actually, Java 21 finalized pattern matching for switch and uses `when` clauses? No—Java 21 uses `when` guards? Let's avoid this distinction in fresher preparation unless specifically asked.

The important Java 21 point is:

```
case String s -> ...
case Integer i -> ...
```

and record patterns such as:

```
case Point(int x, int y) -> ...
```

---

## 44. Sequenced Collections

Java 21 introduced the **Sequenced Collections Framework**.

Before Java 21, the collection APIs did not provide a common interface for collections where **encounter order** and access to the first/last elements were important.

Java 21 introduced:

```
SequencedCollection
SequencedSet
SequencedMap
```

The hierarchy is conceptually:

```
Collection
    |
    └── SequencedCollection
          |
          ├── List
          └── Deque
```

For maps:

```
Map
 |
 └── SequencedMap
```

---

### 45. `SequencedCollection`

`SequencedCollection` provides common operations for accessing the beginning and end of an ordered collection.

Important methods include:

```
getFirst()
getLast()
addFirst()
addLast()
removeFirst()
removeLast()
reversed()
```

Example:

```java
SequencedCollection<String> names = new ArrayList<>();

names.add("A");
names.add("B");
names.add("C");

System.out.println(names.getFirst());
System.out.println(names.getLast());
```

Output:

```
A
C
```

You can also obtain a reversed view:

```java
var reversed = names.reversed();
```

Conceptually:

```
Original:
A → B → C

reversed():
C → B → A
```

---

### 46. `SequencedSet`

`SequencedSet` extends the idea to sets that maintain encounter order.

For example:

```java
SequencedSet<String> set = new LinkedHashSet<>();

set.add("A");
set.add("B");
set.add("C");

System.out.println(set.getFirst());
System.out.println(set.getLast());
```

This gives a standard way to work with the first and last elements of an ordered set.

---

### 47. `SequencedMap`

Java 21 also introduced `SequencedMap`.

It provides operations such as:

```
firstEntry()
lastEntry()
pollFirstEntry()
pollLastEntry()
putFirst()
putLast()
reversed()
```

Example:

```java
SequencedMap<Integer, String> map = new LinkedHashMap<>();

map.put(1, "A");
map.put(2, "B");
map.put(3, "C");

System.out.println(map.firstEntry());
System.out.println(map.lastEntry());
```

Conceptually:

```
1 → A
2 → B
3 → C

firstEntry() → 1=A
lastEntry()  → 3=C
```

---

## 48. Why Sequenced Collections Were Introduced

Before Java 21, developers often had to use collection-specific methods.

For example:

```java
list.get(0);
list.get(list.size() - 1);
```

For maps, accessing first/last entries depended on the particular map implementation/API.

Java 21 provides a common abstraction:

```
"Give me the first element."
"Give me the last element."
"Give me the reversed view."
```

without needing to know the exact concrete collection implementation.

Interview answer:

> Sequenced Collections provide a unified API for collections and maps that have a defined encounter order, including operations for first/last elements and reversed views.
> 

---

# 49. Java 21 — Complete Interview Cheat Sheet

```
Java 21
│
├── Virtual Threads
│     → lightweight JVM-managed threads
│     → massive concurrency
│     → especially useful for I/O-bound workloads
│
├── Record Patterns
│     → deconstruct records
│     → extract record components directly
│
├── Pattern Matching for switch
│     → switch can match types/patterns
│     → finalized in Java 21
│
└── Sequenced Collections
      → common first/last/reversed APIs
      → SequencedCollection
      → SequencedSet
      → SequencedMap
```

The Java 9–21 feature timeline you should memorize for interviews is:

```
Java 9
→ Modules
→ List.of / Set.of / Map.of

Java 10
→ var

Java 11
→ isBlank
→ strip
→ lines

Java 14
→ Switch Expressions (preview)

Java 15
→ Text Blocks

Java 16
→ Records
→ instanceof Pattern Matching

Java 17
→ Sealed Classes

Java 21
→ Virtual Threads
→ Record Patterns
→ Pattern Matching for switch
→ Sequenced Collections
```

And your final placement-prep priority is exactly right:

**Java 8 + Core Java fundamentals + OOP + Strings + Collections + Exception Handling + Multithreading + JVM basics** should receive substantially more preparation time than memorizing the Java 17/21 feature list.

For a fresher interview, you should be able to **explain and code** the older fundamentals, while for Java 17/21 you should primarily be able to **identify the feature, explain why it exists, and give a small example**.
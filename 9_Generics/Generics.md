# Generics

### 1. Generic Class

A generic class is a class that works with a type specified when the object is created.

Instead of writing separate classes for `Integer`, `String`, etc., one class can work with multiple types.

Example without generics:

```java
class IntegerBox {
    Integer value;
}
```

With generics:

```java
class Box<T> {
    T value;

    void set(T value) {
        this.value = value;
    }

    T get() {
        return value;
    }
}
```

Usage:

```java
Box<Integer> intBox = new Box<>();
intBox.set(10);

Box<String> stringBox = new Box<>();
stringBox.set("Java");
```

Here `T` is replaced by the actual type.

Benefits:

- Type safety
- No explicit casting
- Reusable code

### 2. Generic Method

A generic method declares its own type parameter.

Syntax:

```java
<T> returnType methodName(...)
```

Example:

```java
class Demo {

    static <T> void print(T value) {
        System.out.println(value);
    }

}
```

Usage:

```java
Demo.print(10);
Demo.print("Java");
Demo.print(3.14);
```

The compiler infers the type.

Another example:

```java
static <T> T getFirst(T[] arr) {
    return arr[0];
}
```

Usage:

```java
Integer[] nums = {1, 2, 3};

System.out.println(getFirst(nums));
```

### 3. Generic Interface

An interface can also be generic.

Example:

```java
interface Repository<T> {
    void save(T value);
    T find();
}
```

Implementation:

```java
class UserRepository implements Repository<String> {

    @Override
    public void save(String value) {
        System.out.println(value);
    }

    @Override
    public String find() {
        return "Atharva";
    }
}
```

`T` becomes `String` for this implementation.

Generic interfaces are common in frameworks like Spring Data.

### 4. Type Parameter

A type parameter is the placeholder used in generic declarations.

Example:

```java
class Box<T> {
}
```

`T` is a type parameter.

Common naming conventions:

| Symbol | Meaning |
| --- | --- |
| `T` | Type |
| `E` | Element |
| `K` | Key |
| `V` | Value |
| `N` | Number |

Example:

```java
Map<K, V>
List<E>
```

These names are conventions, not keywords.

### 5. Multiple Type Parameters

A generic class can have more than one type parameter.

Example:

```java
class Pair<K, V> {

    K key;
    V value;

    Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
}
```

Usage:

```java
Pair<String, Integer> pair =
        new Pair<>("Age", 22);
```

Here:

- `K` = `String`
- `V` = `Integer`

Another example:

```java
Map<String, Integer>
```

### 6. Bounded Type

A bounded type parameter restricts the types that can be used.

Example:

```java
class NumberBox<T extends Number> {

    T value;

    NumberBox(T value) {
        this.value = value;
    }
}
```

Valid:

```java
NumberBox<Integer> a =
        new NumberBox<>(10);

NumberBox<Double> b =
        new NumberBox<>(3.14);
```

Invalid:

```java
NumberBox<String> c =
        new NumberBox<>("Java");
```

because `String` does not extend `Number`.

Syntax:

```java
<T extends SomeClass>
```

### 7. Upper Bound

An upper bound means the type must be a specific class or one of its subclasses.

Example:

```java
<T extends Number>
```

Valid types:

```
Number
Integer
Double
Float
Long
```

Example method:

```java
static <T extends Number>
double sum(T a, T b) {

    return a.doubleValue() +
           b.doubleValue();
}
```

Usage:

```java
sum(10, 20);
sum(2.5, 3.5);
```

This allows using methods defined by `Number`.

### 8. Lower Bound

A lower bounded wildcard means the type must be a specific type or one of its superclasses.

Syntax:

```java
<? super T>
```

Example:

```java
List<? super Integer>
```

Allowed:

```
Integer
Number
Object
```

Not allowed:

```
Double
```

Example:

```java
static void addNumbers(
        List<? super Integer> list) {

    list.add(10);
    list.add(20);
}
```

This is useful when you want to write values into a collection.

### 9. Wildcards

A wildcard is represented by:

```
?
```

It means "unknown type."

There are three main forms.

#### Unbounded Wildcard

```java
<?>
```

Example:

```java
static void print(List<?> list) {

    for (Object obj : list) {
        System.out.println(obj);
    }
}
```

This method works with:

```
List<Integer>
List<String>
List<Double>
```

You can safely read elements as `Object`.

#### Upper Bounded Wildcard

```java
<? extends T>
```

Example:

```java
List<? extends Number>
```

Allowed lists:

```
List<Integer>
List<Double>
List<Long>
```

You can safely read values as `Number`.

Example:

```java
static double sum(
        List<? extends Number> list) {

    double sum = 0;

    for (Number n : list) {
        sum += n.doubleValue();
    }

    return sum;
}
```

You generally cannot add a specific subtype value because the exact subtype is unknown.

#### Lower Bounded Wildcard

```java
<? super T>
```

Example:

```java
List<? super Integer>
```

Allowed lists:

```
List<Integer>
List<Number>
List<Object>
```

You can safely add `Integer` values.

```java
list.add(10);
```

When reading, the result is only guaranteed to be an `Object`.

## PECS Rule

This is one of the most common interview questions.

PECS means:

```
Producer Extends
Consumer Super
```

If a collection produces values for you to read:

```java
<? extends T>
```

If a collection consumes values that you add:

```java
<? super T>
```

Example:

```java
List<? extends Number> producer;
```

You can read:

```java
Number n = producer.get(0);
```

Example:

```java
List<? super Integer> consumer;
```

You can add:

```java
consumer.add(10);
```

## Wildcard Comparison

| Wildcard | Read | Write |
| --- | --- | --- |
| `<?>` | `Object` | No specific value (except `null`) |
| `<? extends T>` | `T` | Generally no |
| `<? super T>` | `Object` | `T` |

## Important Interview Examples

### `List<Integer>` vs `List<Number>`

```java
List<Integer> integers =
        new ArrayList<>();

List<Number> numbers =
        new ArrayList<>();

// numbers = integers; // Compile-time error
```

Even though:

```
Integer extends Number
```

generic types are invariant.

Use:

```java
List<? extends Number> list = integers;
```

This is a very common interview question.

## Final Mental Model

```
Generics
│
├── Generic Class
│     class Box<T>
│
├── Generic Method
│     <T> T method()
│
├── Generic Interface
│     interface Repository<T>
│
├── Type Parameters
│     T, E, K, V
│
├── Bounds
│     T extends Number
│
└── Wildcards
      ├── <?>
      ├── <? extends T>
      └── <? super T>
```

The most important interview points from this section are:

1. Generics provide compile-time type safety.
2. `T extends Number` is an upper bounded type parameter.
3. `? extends T` is used mainly for reading.
4. `? super T` is used mainly for writing.
5. PECS = Producer Extends, Consumer Super.
6. `List<Integer>` is not a subtype of `List<Number>` because Java generics are invariant.

### 10. Unbounded Wildcard — `<?>`

`<?>` represents an **unknown type**.

Example:

```java
List<?> list;
```

This can refer to:

```
List<Integer>
List<String>
List<Double>
```

Example:

```java
static void printList(List<?> list) {

    for (Object value : list) {
        System.out.println(value);
    }
}
```

You can **read** values as `Object`.

You generally cannot add a specific value:

```java
list.add(10);       // ❌
list.add("Java");   // ❌
list.add(null);     // ✅
```

Why?

Because the actual type is unknown.

It could be:

```
List<Integer>
```

or:

```
List<String>
```

So Java cannot safely allow you to add an `Integer` or `String`.

Important:

> `<?>` means "a List of some unknown type," not "a List of Object."
> 

---

### 11. Upper Bounded Wildcard — `<? extends T>`

`<? extends T>` means:

> The unknown type is `T` or a subclass of `T`.
> 

Example:

```java
List<? extends Number> list;
```

It can refer to:

```
List<Integer>
List<Double>
List<Float>
List<Number>
```

Example:

```java
static void printNumbers(
        List<? extends Number> list) {

    for (Number n : list) {
        System.out.println(n);
    }
}
```

You can safely **read**:

```java
Number n = list.get(0);
```

But you generally cannot add:

```java
list.add(10);       // ❌
list.add(3.14);     // ❌
```

because the exact subtype is unknown.

For example, if the actual list is:

```
List<Double>
```

adding an `Integer` would be unsafe.

So:

```
<? extends T>
      ↓
Producer
      ↓
READ
```

---

### 12. Lower Bounded Wildcard — `<? super T>`

`<? super T>` means:

> The unknown type is `T` or a superclass of `T`.
> 

Example:

```java
List<? super Integer> list;
```

It can refer to:

```
List<Integer>
List<Number>
List<Object>
```

You can safely add `Integer`:

```java
list.add(10);
list.add(20);
```

But when reading:

```java
Object value = list.get(0);
```

The safe type is only `Object`.

So:

```
<? super T>
      ↓
Consumer
      ↓
WRITE
```

---

### 13. PECS — Producer Extends, Consumer Super

**PECS = Producer Extends, Consumer Super**

This is one of the most important rules in Java Generics.

> If a generic structure **produces** values for you → use `extends`.
> 

> If a generic structure **consumes** values from you → use `super`.
> 

### Producer → `extends`

```java
static double sum(
        List<? extends Number> numbers) {

    double total = 0;

    for (Number n : numbers) {
        total += n.doubleValue();
    }

    return total;
}
```

The list **produces** `Number` values for us.

Therefore:

```java
<? extends Number>
```

### Consumer → `super`

```java
static void addNumbers(
        List<? super Integer> numbers) {

    numbers.add(10);
    numbers.add(20);
}
```

The list **consumes** `Integer` values.

Therefore:

```java
<? super Integer>
```

### Easy way to remember

```
             PECS

      Producer → Extends
      Consumer → Super
```

Or:

```
READ  → <? extends T>
WRITE → <? super T>
```

This is a very useful interview shortcut, although the full rule is about the variance you need for a particular operation, not simply "read = extends, write = super" in every imaginable API.

---

### 14. PECS Example — `Collections.copy()`

A classic example is:

```java
Collections.copy(destination, source);
```

Its generic signature is conceptually:

```java
<T> void copy(
        List<? super T> dest,
        List<? extends T> src
)
```

Why?

```
src
 ↓
produces T
 ↓
<? extends T>

dest
 ↓
consumes T
 ↓
<? super T>
```

Example:

```java
List<Integer> source =
        List.of(10, 20, 30);

List<Number> destination =
        new ArrayList<>(List.of(0, 0, 0));

Collections.copy(destination, source);
```

Here:

```
source      → Producer → <? extends T>
destination → Consumer → <? super T>
```

This is an excellent example to mention in interviews when explaining PECS.

---

### 15. Type Erasure

**Type erasure** means Java's generic type information is primarily a **compile-time feature** and is erased from ordinary runtime type information when the compiler generates bytecode.

Example:

```java
List<String> names =
        new ArrayList<>();
```

At compile time, Java knows:

```
List<String>
```

But at runtime, the object's class is essentially:

```
ArrayList
```

rather than a separate:

```
ArrayList<String>
```

and:

```java
List<Integer> numbers =
        new ArrayList<>();
```

also uses the same runtime class:

```
ArrayList
```

Conceptually:

```
Source Code
     ↓
List<String>
List<Integer>
     ↓
Compiler
     ↓
Generic type information erased
     ↓
Bytecode
```

### Why does Java use type erasure?

Main reasons include:

- Backward compatibility with older Java code
- Generics do not require separate runtime classes for every type parameterization
- Existing JVM bytecode model could continue to work

---

### 16. Type Erasure Example

Consider:

```java
class Box<T> {

    private T value;

    void set(T value) {
        this.value = value;
    }

    T get() {
        return value;
    }
}
```

Conceptually, after erasure:

```java
class Box {

    private Object value;

    void set(Object value) {
        this.value = value;
    }

    Object get() {
        return value;
    }
}
```

The compiler inserts the necessary casts at the appropriate usage points.

For a bounded type:

```java
class Box<T extends Number> {
    T value;
}
```

the effective erased type is the bound:

```
T → Number
```

Important interview point:

> Type erasure means generic type parameters generally are not available as ordinary runtime type information.
> 

---

### 17. Restrictions Caused by Type Erasure

Because generic type information is erased, some operations are not allowed.

You cannot do:

```java
if (obj instanceof List<String>) {
    // ❌
}
```

because the JVM cannot distinguish:

```
List<String>
```

from:

```
List<Integer>
```

at runtime.

But this is allowed:

```java
if (obj instanceof List<?>) {
    // ✅
}
```

because `<?>` does not require knowing the specific type argument.

Similarly, you cannot directly do:

```java
T obj = new T();  // ❌
```

because the runtime type of `T` is not known.

You also cannot create:

```java
T[] arr = new T[10];  // ❌
```

directly for the same fundamental reason.

---

### 18. Raw Types

A **raw type** is using a generic class or interface **without specifying its type parameter**.

Example:

```java
List list =
        new ArrayList();
```

instead of:

```java
List<String> list =
        new ArrayList<>();
```

Raw types existed before generics were introduced in Java 5.

Example:

```java
List list =
        new ArrayList();

list.add("Java");
list.add(100);

String s =
        (String) list.get(1);
// Runtime ClassCastException
```

The compiler cannot provide the same type safety because the generic type was omitted.

With generics:

```java
List<String> list =
        new ArrayList<>();

list.add("Java");

// list.add(100);  // ❌ compile-time error
```

This catches the problem much earlier.

---

### 19. Raw Type vs Generic Type

| Raw Type | Generic Type |
| --- | --- |
| `List list` | `List<String> list` |
| No type parameter | Type parameter specified |
| Weak type safety | Strong compile-time type safety |
| Requires more casting | Usually avoids explicit casting |
| Can produce runtime `ClassCastException` | Many type errors caught at compile time |
| Legacy code compatibility | Preferred modern approach |

Avoid raw types in new code unless you specifically need to interact with legacy APIs.

---

### 20. Wildcards vs Type Parameters

This is another useful interview distinction.

A **type parameter** introduces a type variable:

```java
static <T> void print(T value) {
}
```

A **wildcard** represents an unknown type:

```java
static void print(List<?> list) {
}
```

Compare:

```java
<T> void method(List<T> list)
```

with:

```java
void method(List<?> list)
```

The first introduces a named type `T` that can be referred to elsewhere in the method signature.

The second says:

> "I don't care what the element type is."
> 

For example:

```java
static <T> T first(List<T> list) {
    return list.get(0);
}
```

Here `T` is important because it appears in both the input and return type.

Whereas:

```java
static void print(List<?> list) {
    System.out.println(list);
}
```

doesn't need to know the specific element type.

---

### Final Generics Cheat Sheet

```
<?>
 ↓
Unknown type
 ↓
Can read as Object
 ↓
Cannot safely add specific values
```

```
<? extends T>
 ↓
T or subclass
 ↓
Producer
 ↓
Mainly read
```

```
<? super T>
 ↓
T or superclass
 ↓
Consumer
 ↓
Mainly write
```

```
PECS
 ↓
Producer Extends
Consumer Super
```

```
Type Erasure
 ↓
Generic type information is primarily
compile-time information
 ↓
Runtime does not generally retain
specific type arguments
```

```
Raw Type
 ↓
List list
 ↓
Generic type omitted
 ↓
Avoid in new code
```

For interviews, the **highest-priority concepts here are PECS, `? extends T`, `? super T`, type erasure, and raw types**.
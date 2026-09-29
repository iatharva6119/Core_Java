# Java 8+ Features

# 1. Lambda Expressions

A lambda expression is a concise way to represent an **anonymous function**. It is mainly used to provide an implementation of a **functional interface**.

Before Java 8:

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello");
    }
};
```

With lambda:

```java
Runnable r = () -> {
    System.out.println("Hello");
};
```

For a single statement:

```java
Runnable r = () -> System.out.println("Hello");
```

Lambda expressions were introduced in **Java 8**.

---

# 2. Lambda Syntax

Basic syntax:

```
(parameters) -> expression
```

or

```
(parameters) -> {
    statements;
}
```

Examples:

```java
() -> System.out.println("Hello")
```

```java
x -> x * 2
```

```java
(a, b) -> a + b
```

```java
(a, b) -> {
    int result = a + b;
    return result;
}
```

Rules:

- No parameter:

```java
() -> ...
```

- One parameter — parentheses can be omitted:

```java
x -> x * 2
```

- Multiple parameters:

```java
(x, y) -> x + y
```

- Explicit types are allowed:

```java
(int x, int y) -> x + y
```

but don't mix implicit and explicit parameter types:

```java
// Invalid
(int x, y) -> x + y
```

---

# 3. Functional Programming

Lambda expressions allow Java to support a more **functional programming style**.

Instead of focusing only on **how** to perform an operation, you can pass behavior as data.

For example:

```java
List<Integer> numbers = Arrays.asList(10, 20, 30, 40);

numbers.forEach(n -> System.out.println(n));
```

Here:

```java
n -> System.out.println(n)
```

represents the behavior being passed to `forEach()`.

Lambdas are commonly used with:

- Functional interfaces
- Collections
- Streams
- Threads
- Event handling
- Callbacks

---

# 4. Lambda with Collections

Lambdas are heavily used with the Collection Framework.

## `forEach()`

```java
List<String> names = Arrays.asList("Atharva", "Rahul", "Amit");

names.forEach(name -> System.out.println(name));
```

You can also use method reference:

```java
names.forEach(System.out::println);
```

## Sorting

Before Java 8:

```java
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});
```

With lambda:

```java
names.sort((a, b) -> a.compareTo(b));
```

Or:

```java
names.sort(String::compareTo);
```

## Filtering

Using `removeIf()`:

```java
List<Integer> numbers =
    new ArrayList<>(Arrays.asList(10, 15, 20, 25));

numbers.removeIf(n -> n % 2 != 0);
```

After execution:

```
[10, 20]
```

---

# 5. Lambda with Threads

Lambda makes creating threads much shorter.

Traditional:

```java
Thread t = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Task running");
    }
});

t.start();
```

Using lambda:

```java
Thread t = new Thread(() -> {
    System.out.println("Task running");
});

t.start();
```

Or:

```java
new Thread(() -> System.out.println("Task running")).start();
```

Why does this work?

Because `Runnable` is a **functional interface**:

```java
@FunctionalInterface
public interface Runnable {
    void run();
}
```

It has exactly **one abstract method**, so a lambda can provide its implementation.

### Interview point

A lambda itself is **not an object or a functional interface**. It is an expression whose target type must be a functional interface.

For example:

```java
Runnable r = () -> System.out.println("Hello");
```

Here the lambda is assigned to a `Runnable` functional-interface reference.

The key relationship is:

```
Lambda
   ↓ implements behavior of
Functional Interface
   ↓
Runnable / Comparator / Predicate / Function / Consumer
```

---

# 6. `Predicate<T>`

`Predicate<T>` represents a function that **takes one argument and returns `boolean`**.

Package:

```
java.util.function.Predicate
```

Main method:

```java
boolean test(T t)
```

Example:

```java
Predicate<Integer> isEven = n -> n % 2 == 0;

System.out.println(isEven.test(10)); // true
System.out.println(isEven.test(7));  // false
```

Common use: filtering.

```java
List<Integer> numbers =
    Arrays.asList(10, 15, 20, 25);

numbers.removeIf(n -> n % 2 != 0);
```

Important methods:

```
and()
or()
negate()
```

---

# 7. `Function<T, R>`

`Function<T, R>` takes one argument of type `T` and **returns a result of type `R`**.

Main method:

```java
R apply(T t)
```

Example:

```java
Function<String, Integer> length = s -> s.length();

System.out.println(length.apply("Java")); // 4
```

Another example:

```java
Function<Integer, Integer> square = n -> n * n;

System.out.println(square.apply(5)); // 25
```

Think:

```
Input → Processing → Output

T → Function → R
```

---

# 8. `Consumer<T>`

`Consumer<T>` takes one argument but **does not return a value**.

Main method:

```java
void accept(T t)
```

Example:

```java
Consumer<String> print = s -> System.out.println(s);

print.accept("Hello");
```

Very commonly used with `forEach()`:

```java
names.forEach(name -> System.out.println(name));
```

Think:

```
Input → Action → Nothing returned
```

---

# 9. `Supplier<T>`

`Supplier<T>` takes **no argument** and returns a value.

Main method:

```java
T get()
```

Example:

```java
Supplier<Double> random =
    () -> Math.random();

System.out.println(random.get());
```

Another example:

```java
Supplier<String> message =
    () -> "Hello Java";

System.out.println(message.get());
```

Think:

```
No input → Produces output
```

---

# 10. `UnaryOperator<T>`

`UnaryOperator<T>` is a specialized `Function<T, T>`.

It takes one value and returns a value of the **same type**.

```java
UnaryOperator<Integer> square = n -> n * n;

System.out.println(square.apply(5)); // 25
```

Relationship:

```
UnaryOperator<T>
        ↓
Function<T, T>
```

Example:

```java
UnaryOperator<String> upper =
    s -> s.toUpperCase();
```

---

# 11. `BinaryOperator<T>`

`BinaryOperator<T>` is a specialized `BiFunction<T, T, T>`.

It takes **two values of the same type** and returns the same type.

```java
BinaryOperator<Integer> add =
    (a, b) -> a + b;

System.out.println(add.apply(10, 20)); // 30
```

Relationship:

```
BinaryOperator<T>
        ↓
BiFunction<T, T, T>
```

Another example:

```java
BinaryOperator<Integer> max =
    (a, b) -> Math.max(a, b);
```

---

# 12. Custom Functional Interface

You can create your own functional interface.

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}
```

Use lambda:

```java
Calculator add = (a, b) -> a + b;

Calculator multiply = (a, b) -> a * b;

System.out.println(add.calculate(10, 20));      // 30
System.out.println(multiply.calculate(10, 20)); // 200
```

The important point is that a functional interface must have **exactly one abstract method**.

It can still have:

- Multiple `default` methods
- Multiple `static` methods
- Methods inherited from `Object`

Example:

```java
@FunctionalInterface
interface Greeting {

    void greet(String name);

    default void message() {
        System.out.println("Welcome");
    }

    static void info() {
        System.out.println("Greeting interface");
    }
}
```

---

# 13. `@FunctionalInterface`

`@FunctionalInterface` tells the compiler that an interface is intended to be a functional interface.

Example:

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}
```

If you add another abstract method:

```java
@FunctionalInterface
interface Calculator {

    int add(int a, int b);

    int multiply(int a, int b); // Compilation error
}
```

The annotation is **not mandatory** for an interface to be functional.

This is valid:

```java
interface Calculator {
    int add(int a, int b);
}
```

But using `@FunctionalInterface` is recommended because the compiler verifies the rule.

### Must-remember table

| Interface | Input | Output | Main method |
| --- | --- | --- | --- |
| `Predicate<T>` | 1 | `boolean` | `test()` |
| `Function<T,R>` | 1 | `R` | `apply()` |
| `Consumer<T>` | 1 | Nothing | `accept()` |
| `Supplier<T>` | 0 | `T` | `get()` |
| `UnaryOperator<T>` | 1 | Same `T` | `apply()` |
| `BinaryOperator<T>` | 2 | Same `T` | `apply()` |

The easiest way to memorize them:

```
Predicate → Check something
Function  → Transform something
Consumer  → Consume/use something
Supplier  → Supply something
Unary     → 1 input → same type
Binary    → 2 inputs → same type
```

---

# 14. Stream API

The **Stream API** was introduced in Java 8 to process collections/data in a declarative and functional style.

A Stream is **not a data structure** and does not store data. It provides a pipeline for processing data.

Typical pipeline:

```
Source → Intermediate Operations → Terminal Operation
```

Example:

```java
List<Integer> numbers =
    Arrays.asList(10, 15, 20, 25);

numbers.stream()
       .filter(n -> n > 15)
       .map(n -> n * 2)
       .forEach(System.out::println);
```

Output:

```
40
50
```

---

# 15. Creating Streams

Several ways exist.

From a Collection:

```java
List<Integer> numbers =
    Arrays.asList(1, 2, 3);

Stream<Integer> stream = numbers.stream();
```

From values:

```java
Stream<Integer> stream =
    Stream.of(1, 2, 3, 4);
```

From an array:

```java
int[] arr = {1, 2, 3, 4};

IntStream stream = Arrays.stream(arr);
```

Empty stream:

```java
Stream<String> stream =
    Stream.empty();
```

---

# 16. `stream()`

`stream()` creates a sequential stream from a Collection.

```java
List<String> names =
    Arrays.asList("Atharva", "Rahul", "Amit");

names.stream()
     .forEach(System.out::println);
```

Important:

```
Collection → stream() → Stream
```

The original collection is **not modified** merely by creating a stream.

---

# 17. `filter()`

`filter()` selects elements based on a condition.

It takes a `Predicate`.

```java
List<Integer> numbers =
    Arrays.asList(10, 15, 20, 25, 30);

numbers.stream()
       .filter(n -> n > 20)
       .forEach(System.out::println);
```

Output:

```
25
30
```

`filter()` is an **intermediate operation**.

---

# 18. `map()`

`map()` transforms every element.

It takes a `Function`.

```java
List<Integer> numbers =
    Arrays.asList(1, 2, 3, 4);

numbers.stream()
       .map(n -> n * n)
       .forEach(System.out::println);
```

Output:

```
1
4
9
16
```

Think:

```
map = one element → one transformed element
```

---

# 19. `flatMap()`

`flatMap()` is used when each element produces multiple elements, and you want to **flatten them into one stream**.

Example:

```java
List<List<Integer>> numbers =
    Arrays.asList(
        Arrays.asList(1, 2),
        Arrays.asList(3, 4),
        Arrays.asList(5, 6)
    );

numbers.stream()
       .flatMap(List::stream)
       .forEach(System.out::println);
```

Output:

```
1
2
3
4
5
6
```

Difference:

```
map     → Stream<Stream<T>> / nested structure
flatMap → Stream<T>         / flattened structure
```

This is one of the most important Stream interview questions.

---

# 20. `sorted()`

Sorts stream elements.

Natural ordering:

```java
List<Integer> numbers =
    Arrays.asList(30, 10, 20);

numbers.stream()
       .sorted()
       .forEach(System.out::println);
```

Output:

```
10
20
30
```

Custom sorting:

```java
numbers.stream()
       .sorted((a, b) -> b - a)
       .forEach(System.out::println);
```

Output:

```
30
20
10
```

`sorted()` is an intermediate operation.

---

# 21. `distinct()`

Removes duplicate elements.

```java
List<Integer> numbers =
    Arrays.asList(10, 20, 10, 30, 20);

numbers.stream()
       .distinct()
       .forEach(System.out::println);
```

Output:

```
10
20
30
```

For objects, uniqueness depends on their `equals()` and `hashCode()` implementations.

---

# 22. `limit()`

Restricts the stream to at most `n` elements.

```java
Stream.of(10, 20, 30, 40, 50)
      .limit(3)
      .forEach(System.out::println);
```

Output:

```
10
20
30
```

Common use:

```
Top N results
Pagination
Taking first few elements
```

---

# 23. `skip()`

Skips the first `n` elements.

```java
Stream.of(10, 20, 30, 40, 50)
      .skip(2)
      .forEach(System.out::println);
```

Output:

```
30
40
50
```

Useful for pagination:

```
skip(offset)
limit(pageSize)
```

---

# 24. `peek()`

`peek()` performs an action on elements as they pass through the stream.

```java
numbers.stream()
       .peek(n -> System.out.println("Before: " + n))
       .filter(n -> n > 10)
       .forEach(System.out::println);
```

Important: `peek()` is **lazy** and generally intended for debugging/observing a pipeline, not for essential business logic.

Without a terminal operation:

```java
numbers.stream()
       .peek(System.out::println);
```

Nothing is printed.

---

# 25. `forEach()`

`forEach()` performs an action on every element.

```java
List<String> names =
    Arrays.asList("A", "B", "C");

names.stream()
     .forEach(System.out::println);
```

`forEach()` is a **terminal operation**.

---

# 26. `collect()`

`collect()` gathers stream results into a collection or another result structure.

Example:

```java
List<Integer> result =
    numbers.stream()
           .filter(n -> n > 10)
           .collect(Collectors.toList());
```

Modern Java can also use:

```java
List<Integer> result =
    numbers.stream()
           .filter(n -> n > 10)
           .toList();
```

Grouping:

```java
Map<Integer, List<String>> result =
    names.stream()
         .collect(
             Collectors.groupingBy(String::length)
         );
```

`collect()` is a terminal operation.

---

# 27. `reduce()`

`reduce()` combines stream elements into a **single result**.

Example — sum:

```java
int sum =
    Stream.of(1, 2, 3, 4)
          .reduce(0, (a, b) -> a + b);

System.out.println(sum); // 10
```

Conceptually:

```
0 + 1 + 2 + 3 + 4 = 10
```

Another example:

```java
int max =
    Stream.of(10, 30, 20)
          .reduce(Integer.MIN_VALUE, Integer::max);
```

`reduce()` is useful for:

```
Sum
Product
Maximum/minimum
Combining values
```

---

# 28. `count()`

Returns the number of elements.

```java
long count =
    Stream.of(10, 20, 30, 40)
          .count();

System.out.println(count); // 4
```

Notice the return type:

```
long
```

not `int`.

---

# 29. `min()`

Returns the minimum element as an `Optional`.

```java
Optional<Integer> min =
    Stream.of(30, 10, 20)
          .min(Integer::compareTo);

System.out.println(min.get()); // 10
```

Why `Optional`?

Because the stream might be empty.

---

# 30. `max()`

Returns the maximum element as an `Optional`.

```java
Optional<Integer> max =
    Stream.of(30, 10, 20)
          .max(Integer::compareTo);

System.out.println(max.get()); // 30
```

Again:

```
max() → Optional<T>
```

---

# 31. `anyMatch()`

Returns `true` if **at least one** element matches the condition.

```java
boolean result =
    numbers.stream()
           .anyMatch(n -> n > 20);
```

Think:

```
ANY element satisfies condition?
```

---

# 32. `allMatch()`

Returns `true` if **every** element matches.

```java
boolean result =
    numbers.stream()
           .allMatch(n -> n > 0);
```

Think:

```
ALL elements satisfy condition?
```

---

# 33. `noneMatch()`

Returns `true` if **no** element matches.

```java
boolean result =
    numbers.stream()
           .noneMatch(n -> n < 0);
```

Think:

```
NO element satisfies condition?
```

These three return `boolean` and are terminal operations.

---

# 34. `findFirst()`

Returns the first element as an `Optional`.

```java
Optional<Integer> result =
    numbers.stream()
           .findFirst();
```

Example:

```java
System.out.println(result.orElse(-1));
```

For an ordered stream, this gives the first element according to encounter order.

---

# 35. `findAny()`

Returns any element from the stream as an `Optional`.

```java
Optional<Integer> result =
    numbers.stream()
           .findAny();
```

For a sequential stream, it will often appear to return the first element, but **you should not rely on that**.

The distinction becomes important with parallel streams:

```java
numbers.parallelStream()
       .findAny();
```

`findAny()` can return whichever element is conveniently available.

---

# Stream Operations — Must Remember

## Intermediate operations

These return another `Stream` and are **lazy**:

```
filter()
map()
flatMap()
sorted()
distinct()
limit()
skip()
peek()
```

## Terminal operations

These produce a final result or side effect:

```
forEach()
collect()
reduce()
count()
min()
max()
anyMatch()
allMatch()
noneMatch()
findFirst()
findAny()
```

The most important interview concept is:

```
Stream pipeline

Source
  ↓
stream()
  ↓
filter()
  ↓
map()
  ↓
sorted()
  ↓
collect()
```

Intermediate operations don't execute immediately. **Execution starts when a terminal operation is encountered.**

Also remember: **a Stream is generally single-use**. Once a terminal operation consumes it, you cannot reuse that same Stream.

```java
Stream<Integer> s = numbers.stream();

s.count();

// s.forEach(System.out::println);
// IllegalStateException
```

---

# 36. Intermediate Operations

Intermediate operations transform or filter a Stream and return **another Stream**.

Examples:

```
filter()
map()
flatMap()
sorted()
distinct()
limit()
skip()
peek()
```

Example:

```java
List<Integer> result =
    numbers.stream()
           .filter(n -> n > 10)
           .map(n -> n * 2)
           .collect(Collectors.toList());
```

Here `filter()` and `map()` are intermediate operations.

**Key point:** Intermediate operations are **lazy**.

---

# 37. Terminal Operations

A terminal operation ends the Stream pipeline and produces a result or side effect.

Examples:

```
forEach()
collect()
reduce()
count()
min()
max()
anyMatch()
allMatch()
noneMatch()
findFirst()
findAny()
```

Example:

```java
long count =
    numbers.stream()
           .filter(n -> n > 10)
           .count();
```

`count()` triggers execution of the pipeline.

---

# 38. Lazy Evaluation

Stream operations are not executed immediately.

```java
numbers.stream()
       .filter(n -> {
           System.out.println("Filtering " + n);
           return n > 10;
       });
```

Nothing happens because there is no terminal operation.

Add:

```java
.count();
```

Now the pipeline executes.

Conceptually:

```
Create pipeline
      ↓
Intermediate operations
      ↓
Nothing executes yet
      ↓
Terminal operation
      ↓
Pipeline executes
```

Lazy evaluation can also avoid unnecessary work.

```java
Optional<Integer> result =
    numbers.stream()
           .filter(n -> n > 10)
           .findFirst();
```

Once the first matching element is found, the Stream doesn't need to process all remaining elements.

---

# 39. Stream Pipeline

A Stream pipeline consists of:

```
Source → Intermediate operations → Terminal operation
```

Example:

```java
List<Integer> result =
    numbers.stream()                     // Source
           .filter(n -> n > 10)          // Intermediate
           .map(n -> n * 2)              // Intermediate
           .collect(Collectors.toList()); // Terminal
```

Think:

```
List
 ↓
stream()
 ↓
filter()
 ↓
map()
 ↓
collect()
 ↓
List
```

---

# 40. Sequential Streams

A sequential Stream processes elements **one at a time in a single thread** by default.

```java
numbers.stream()
       .forEach(System.out::println);
```

Conceptually:

```
Thread
  ↓
1 → 2 → 3 → 4 → 5
```

Use sequential streams when:

- The workload is small.
- Operations are simple.
- Ordering matters.
- Parallelization would add unnecessary overhead.

Most Streams you create with `stream()` are sequential.

---

# 41. Parallel Streams

A parallel Stream divides processing across multiple threads, typically using the **ForkJoinPool common pool**.

```java
numbers.parallelStream()
       .forEach(System.out::println);
```

Conceptually:

```
             Stream
                |
        ----------------
        |      |       |
      Thread Thread  Thread
        |      |       |
      data   data    data
```

You can also convert a stream:

```java
numbers.stream()
       .parallel()
       .forEach(System.out::println);
```

Important: Parallel does **not automatically mean faster**.

For small datasets or cheap operations, parallelism can actually be slower due to thread-management and coordination overhead.

Also, avoid shared mutable state:

```java
List<Integer> result =
    new ArrayList<>();

numbers.parallelStream()
       .forEach(n -> result.add(n)); // Unsafe
```

Prefer collectors or other thread-safe approaches.

---

# 42. `Collectors`

`Collectors` is a utility class in `java.util.stream` that provides implementations of `Collector`.

It is commonly used with:

```java
stream.collect(...)
```

Example:

```java
List<Integer> result =
    numbers.stream()
           .filter(n -> n > 10)
           .collect(Collectors.toList());
```

Common collectors:

```
toList()
toSet()
toMap()
joining()
groupingBy()
partitioningBy()
counting()
summarizingInt()
averagingInt()
```

---

# 43. `groupingBy()`

`groupingBy()` groups elements based on a classification function.

Example:

```java
List<String> names =
    Arrays.asList("Amit", "Anil", "Rahul", "Raj");
```

Group by name length:

```java
Map<Integer, List<String>> result =
    names.stream()
         .collect(
             Collectors.groupingBy(String::length)
         );
```

Result conceptually:

```
3 → [Amit, Anil]
5 → [Rahul]
3 → ...
```

More realistic example:

```java
Map<String, List<Employee>> employeesByDept =
    employees.stream()
             .collect(
                 Collectors.groupingBy(
                     Employee::getDepartment
                 )
             );
```

You can also perform downstream operations.

For example, count employees per department:

```java
Map<String, Long> countByDept =
    employees.stream()
             .collect(
                 Collectors.groupingBy(
                     Employee::getDepartment,
                     Collectors.counting()
                 )
             );
```

Think:

```
groupingBy() → GROUP BY
```

Similar conceptually to SQL:

```sql
SELECT department, COUNT(*)
FROM employee
GROUP BY department;
```

---

# 44. `partitioningBy()`

`partitioningBy()` divides elements into exactly **two groups** based on a `Predicate`.

```java
Map<Boolean, List<Integer>> result =
    numbers.stream()
           .collect(
               Collectors.partitioningBy(
                   n -> n % 2 == 0
               )
           );
```

Result:

```
true  → [2, 4, 6, 8]
false → [1, 3, 5, 7]
```

The keys are always:

```
true
false
```

Difference:

```
groupingBy()     → multiple groups
partitioningBy() → exactly two groups
```

---

# 45. `joining()`

`joining()` combines Stream elements into a single String.

```java
List<String> names =
    Arrays.asList("Java", "Spring", "Docker");

String result =
    names.stream()
         .collect(Collectors.joining(", "));
```

Result:

```
Java, Spring, Docker
```

With prefix and suffix:

```java
String result =
    names.stream()
         .collect(
             Collectors.joining(", ", "[", "]")
         );
```

Result:

```
[Java, Spring, Docker]
```

Important:

`joining()` is generally used with `Stream<String>`.

---

# 46. `toMap()`

`toMap()` converts Stream elements into a `Map`.

Example:

```java
List<String> names =
    Arrays.asList("Amit", "Rahul", "Raj");

Map<String, Integer> result =
    names.stream()
         .collect(
             Collectors.toMap(
                 name -> name,
                 String::length
             )
         );
```

Result:

```
Amit   → 4
Rahul  → 5
Raj    → 3
```

The two functions are:

```
keyMapper   → creates key
valueMapper → creates value
```

Conceptually:

```java
Collectors.toMap(
    keyMapper,
    valueMapper
)
```

## Important `toMap()` interview trap

Duplicate keys cause an `IllegalStateException`.

```java
List<String> names =
    Arrays.asList("Amit", "Anil");
```

If you use first character as the key:

```java
names.stream()
     .collect(
         Collectors.toMap(
             name -> name.charAt(0),
             name -> name
         )
     );
```

Both names produce key `'A'`, causing a duplicate-key exception.

Provide a merge function:

```java
Map<Character, String> result =
    names.stream()
         .collect(
             Collectors.toMap(
                 name -> name.charAt(0),
                 name -> name,
                 (oldValue, newValue) -> oldValue
             )
         );
```

Now duplicate keys are handled.

---

# Most Important Stream Concepts

Keep this mental model:

```
                    STREAM API
                        |
              ----------------------
              |                    |
        Intermediate           Terminal
              |                    |
      Lazy / returns Stream     Executes pipeline
              |                    |
  filter, map, flatMap          collect, reduce
  sorted, distinct              count, forEach
  limit, skip                   min, max
  peek                          findFirst
                                anyMatch...
```

And for collectors:

```
groupingBy()     → Group into multiple categories
partitioningBy() → Split into true/false
joining()        → Combine Strings
toMap()          → Create Map
```

### Important interview distinction

**`groupingBy()` vs `partitioningBy()`**

```
groupingBy(Employee::getDepartment)
        ↓
HR → [...]
IT → [...]
Sales → [...]

partitioningBy(Employee::isActive)
        ↓
true  → [...]
false → [...]
```

---

# 47. `Optional`

`Optional<T>` is a container that may contain a value or may be empty.

It was introduced in Java 8 mainly to make the absence of a value explicit and reduce careless `null` handling.

Without `Optional`:

```java
String name = getName();

if (name != null) {
    System.out.println(name.length());
}
```

With `Optional`:

```java
Optional<String> name = getName();

name.ifPresent(
    n -> System.out.println(n.length())
);
```

Important: `Optional` does **not eliminate `NullPointerException`**. It is mainly a tool for representing potentially absent values.

---

# 48. `Optional.of()`

`of()` creates an `Optional` containing a **non-null value**.

```java
Optional<String> name =
    Optional.of("Atharva");
```

But passing `null` causes `NullPointerException`:

```java
Optional<String> name =
    Optional.of(null); // NullPointerException
```

Use `of()` when you know the value cannot be null.

---

# 49. `Optional.ofNullable()`

`ofNullable()` accepts both non-null and null values.

```java
String name = null;

Optional<String> optional =
    Optional.ofNullable(name);
```

Result:

```
Optional.empty
```

If the value is non-null:

```java
Optional<String> optional =
    Optional.ofNullable("Atharva");
```

Result:

```
Optional[Atharva]
```

This is commonly used when wrapping values that may be `null`.

---

# 50. `Optional.empty()`

Creates an empty `Optional`.

```java
Optional<String> optional =
    Optional.empty();
```

You can check it:

```java
System.out.println(
    optional.isPresent()
); // false
```

---

# 51. `isPresent()`

Returns `true` if a value exists.

```java
Optional<String> name =
    Optional.of("Atharva");

if (name.isPresent()) {
    System.out.println(name.get());
}
```

Output:

```
Atharva
```

However, this pattern:

```java
if (optional.isPresent()) {
    optional.get();
}
```

is often less desirable than directly using `ifPresent()`, `orElse()`, etc.

---

# 52. `ifPresent()`

Executes an action only when a value exists.

```java
Optional<String> name =
    Optional.of("Atharva");

name.ifPresent(
    n -> System.out.println(n)
);
```

If empty, nothing happens.

It takes a `Consumer<T>`:

```
Optional<T>
    ↓
ifPresent(Consumer<T>)
```

---

# 53. `orElse()`

Returns the contained value if present; otherwise returns a default value.

```java
Optional<String> name =
    Optional.empty();

String result =
    name.orElse("Unknown");

System.out.println(result);
```

Output:

```
Unknown
```

Important interview point:

**The argument to `orElse()` is evaluated even when the Optional contains a value.**

```java
String result =
    optional.orElse(getDefaultValue());
```

`getDefaultValue()` is evaluated regardless of whether `optional` has a value.

---

# 54. `orElseGet()`

`orElseGet()` uses a `Supplier` to generate the default value **only when the Optional is empty**.

```java
String result =
    optional.orElseGet(
        () -> getDefaultValue()
    );
```

Therefore:

```
orElse()    → eager
orElseGet() → lazy
```

Example:

```java
Optional<String> name =
    Optional.of("Atharva");

String result =
    name.orElseGet(() -> {
        System.out.println("Generating default");
        return "Unknown";
    });
```

The default supplier is not executed because the value already exists.

---

# 55. `orElseThrow()`

Throws an exception when the Optional is empty.

Modern Java:

```java
String name =
    optional.orElseThrow();
```

If empty, it throws:

```
NoSuchElementException
```

You can provide a custom exception:

```java
String name =
    optional.orElseThrow(
        () -> new RuntimeException("Name not found")
    );
```

This is very common in Spring applications:

```java
User user =
    userRepository.findById(id)
                  .orElseThrow(
                      () -> new UserNotFoundException(
                          "User not found"
                      )
                  );
```

---

# 56. `map()`

`map()` transforms the value inside an `Optional`.

```java
Optional<String> name =
    Optional.of("Atharva");

Optional<Integer> length =
    name.map(String::length);
```

Result:

```
Optional[7]
```

If the original Optional is empty, `map()` does not execute the function and returns an empty Optional.

```
Optional<T>
    ↓ map(Function)
Optional<R>
```

Example:

```java
Optional<String> name =
    Optional.of("atharva");

Optional<String> upper =
    name.map(String::toUpperCase);
```

Result:

```
Optional[ATHARVA]
```

---

# 57. `flatMap()`

`flatMap()` is used when the mapping function **already returns an Optional**.

Suppose:

```java
Optional<String> name =
    Optional.of("Atharva");
```

A method returns:

```java
Optional<String> getNickname(String name)
```

Using `map()` would create:

```
Optional<Optional<String>>
```

That's usually not what you want.

Use:

```java
Optional<String> nickname =
    name.flatMap(this::getNickname);
```

Conceptually:

```
map:
Optional<T> → Optional<Optional<R>>

flatMap:
Optional<T> → Optional<R>
```

This is the same fundamental idea as `Stream.flatMap()`: **flatten nested containers**.

---

# Most Important `Optional` Comparison

| Method | Purpose |
| --- | --- |
| `of()` | Non-null value only |
| `ofNullable()` | Value may be null |
| `empty()` | Creates empty Optional |
| `isPresent()` | Checks whether value exists |
| `ifPresent()` | Executes action if value exists |
| `orElse()` | Default value; eager |
| `orElseGet()` | Default supplier; lazy |
| `orElseThrow()` | Throws if empty |
| `map()` | Transform contained value |
| `flatMap()` | Transform when mapper returns Optional |

### Most important interview question

**`orElse()` vs `orElseGet()`**

```java
orElse(defaultValue)
```

The default expression is evaluated immediately.

```java
orElseGet(() -> defaultValue)
```

The supplier is evaluated only if the Optional is empty.

So for an expensive default computation:

```java
optional.orElseGet(
    () -> expensiveOperation()
);
```

is generally preferable.

### One important best practice

Avoid using `Optional` as a general-purpose replacement for every variable that might be null.

Good:

```java
Optional<User> findUserById(Long id)
```

Less appropriate:

```java
class User {
    Optional<String> name; // often unnecessary for ordinary fields
}
```

`Optional` is most useful for **return values where absence is a legitimate outcome**.

---

# 58. Static Method Reference

A method reference is a shorter form of a lambda when an existing method already provides the required behavior.

Syntax:

```
ClassName::staticMethod
```

Example:

```java
Function<Integer, Integer> square =
    Math::abs;

System.out.println(
    square.apply(-10)
); // 10
```

Equivalent lambda:

```java
Function<Integer, Integer> square =
    x -> Math.abs(x);
```

Another common example:

```java
List<String> names =
    Arrays.asList("Amit", "Rahul", "Raj");

names.forEach(System.out::println);
```

For `System.out::println`, technically this is an **instance method reference**, because `println()` belongs to the `PrintStream` object `System.out`.

---

# 59. Instance Method Reference

Syntax:

```
object::instanceMethod
```

Example:

```java
String text = "hello";

Supplier<String> upper =
    text::toUpperCase;

System.out.println(
    upper.get()
); // HELLO
```

Equivalent lambda:

```java
Supplier<String> upper =
    () -> text.toUpperCase();
```

Another common form is referencing an instance method of an arbitrary object of a particular type:

```java
Function<String, Integer> length =
    String::length;
```

Equivalent:

```java
Function<String, Integer> length =
    s -> s.length();
```

So there are two useful forms:

```
object::method
Type::instanceMethod
```

---

# 60. Constructor Reference

Constructor references provide a concise way to create objects.

Syntax:

```
ClassName::new
```

Example:

```java
Supplier<ArrayList<String>> listCreator =
    ArrayList::new;

ArrayList<String> list =
    listCreator.get();
```

Equivalent lambda:

```java
Supplier<ArrayList<String>> listCreator =
    () -> new ArrayList<>();
```

With parameters:

```java
Function<String, StringBuilder> builderCreator =
    StringBuilder::new;
```

Equivalent:

```java
Function<String, StringBuilder> builderCreator =
    s -> new StringBuilder(s);
```

---

# Interface Improvements

# 61. Default Methods

Java 8 introduced `default` methods in interfaces.

A default method has a method body.

```java
interface Vehicle {

    default void start() {
        System.out.println("Vehicle starting");
    }
}
```

A class implementing the interface automatically gets that implementation:

```java
class Car implements Vehicle {
}
```

```java
Car car = new Car();

car.start();
```

Output:

```
Vehicle starting
```

Why were default methods introduced?

Primarily to allow interfaces to evolve without breaking existing implementations.

For example, Java could add a method to an existing interface with a default implementation, so existing implementing classes would not necessarily need to implement the new method.

A class can override the default method:

```java
class Car implements Vehicle {

    @Override
    public void start() {
        System.out.println("Car starting");
    }
}
```

---

# 62. Static Methods in Interfaces

Java 8 also allows interfaces to contain static methods.

```java
interface MathUtil {

    static int square(int n) {
        return n * n;
    }
}
```

Call it using the **interface name**:

```java
int result =
    MathUtil.square(5);
```

You cannot call it through an implementing object:

```java
Car car = new Car();

// car.square(); // Invalid
```

Important distinction:

```
default method → inherited by implementing class
static method  → belongs to interface itself
```

---

# 63. Functional Interfaces

A functional interface contains **exactly one abstract method**.

Example:

```java
@FunctionalInterface
interface Calculator {

    int calculate(int a, int b);
}
```

It can be implemented using a lambda:

```java
Calculator add =
    (a, b) -> a + b;

System.out.println(
    add.calculate(10, 20)
);
```

Java's common built-in functional interfaces include:

```
Predicate<T>      → T → boolean
Function<T,R>     → T → R
Consumer<T>       → T → void
Supplier<T>       → () → T
UnaryOperator<T>  → T → T
BinaryOperator<T> → (T,T) → T
```

A functional interface can also contain `default` and `static` methods because those are **not abstract methods**.

Example:

```java
@FunctionalInterface
interface Calculator {

    int calculate(int a, int b);

    default void info() {
        System.out.println("Calculator");
    }

    static void help() {
        System.out.println("Use calculate()");
    }
}
```

This is still a valid functional interface because it has only **one abstract method**: `calculate()`.

### Important interview point

`@FunctionalInterface` is not what makes an interface functional. The interface is functional because it has exactly one abstract method. The annotation tells the compiler to enforce that rule.

---

# Method Reference Summary

```
Static method:
ClassName::staticMethod

Instance method:
object::instanceMethod

Instance method of arbitrary object:
ClassName::instanceMethod

Constructor:
ClassName::new
```

The easiest way to think about method references:

```java
// Lambda
x -> System.out.println(x)

// Method reference
System.out::println
```

If the lambda simply calls an existing method without additional logic, a method reference is often the cleaner form.
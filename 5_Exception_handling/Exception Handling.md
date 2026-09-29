# Exception Handling

# 1. Exception Hierarchy

Java exception handling starts with the `Throwable` hierarchy.

```
                     Object
                        |
                    Throwable
                   /         \
                Error       Exception
                 |             |
          OutOfMemoryError   RuntimeException
          StackOverflowError      |
                ...           Unchecked
                                  |
                           Other Exceptions
                                  |
                           Checked Exceptions
```

More accurately:

```
Throwable
├── Error
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── ...
│
└── Exception
    ├── RuntimeException
    │   ├── NullPointerException
    │   ├── ArithmeticException
    │   ├── ArrayIndexOutOfBoundsException
    │   └── ...
    │
    └── Checked Exceptions
        ├── IOException
        ├── SQLException
        ├── FileNotFoundException
        └── ...
```

The key distinction for interviews is:

```
Throwable
├── Error        → serious JVM/system problems
└── Exception
    ├── Checked  → compiler forces handling
    └── RuntimeException → unchecked
```

---

# 2. Throwable

`Throwable` is the **root class for all errors and exceptions** that can be thrown and caught in Java.

```
Throwable
├── Error
└── Exception
```

Important methods:

```java
try {
    int x = 10 / 0;
} catch (Throwable t) {
    System.out.println(t.getMessage());
    t.printStackTrace();
}
```

Common methods:

- `getMessage()` → returns error message
- `printStackTrace()` → prints stack trace
- `toString()` → class name + message
- `getCause()` → returns underlying cause

Usually, you should **not catch `Throwable` directly**, because that also catches serious `Error`s.

---

# 3. Error

`Error` represents serious problems that applications generally **should not try to recover from**.

Examples:

```
OutOfMemoryError
StackOverflowError
NoClassDefFoundError
```

Example:

```java
public static void recursive() {
    recursive();
}
```

This can eventually produce:

```
StackOverflowError
```

Important:

```
Error ≠ Exception
```

Errors are generally caused by JVM/system-level problems rather than normal application logic.

---

# 4. Exception

`Exception` represents conditions that an application **may be able to handle**.

```
Exception
├── RuntimeException
└── Other checked exceptions
```

Examples:

```
IOException
SQLException
NullPointerException
ArithmeticException
```

Handling:

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

---

# 5. RuntimeException

`RuntimeException` is the parent class of most common **unchecked exceptions**.

Examples:

```
RuntimeException
├── NullPointerException
├── ArithmeticException
├── ArrayIndexOutOfBoundsException
├── ClassCastException
├── NumberFormatException
└── IllegalArgumentException
```

Example:

```java
String s = null;
System.out.println(s.length());
```

Result:

```
NullPointerException
```

The compiler does **not** force you to handle it.

```java
public void test() {
    String s = null;
    s.length(); // compiles
}
```

---

# 6. Checked Exceptions

Checked exceptions are exceptions that the **compiler forces you to handle or declare**.

They are subclasses of `Exception` but **not** subclasses of `RuntimeException`.

Examples:

```
IOException
SQLException
FileNotFoundException
ClassNotFoundException
InterruptedException
```

Example:

```java
import java.io.*;

public void readFile() throws IOException {
    FileReader file = new FileReader("data.txt");
}
```

You must either:

### Handle it

```java
try {
    FileReader file = new FileReader("data.txt");
} catch (IOException e) {
    System.out.println("File error");
}
```

### Or declare it

```java
public void readFile() throws IOException {
    FileReader file = new FileReader("data.txt");
}
```

**Interview definition:**

> A checked exception is checked by the compiler and must be either caught or declared using `throws`.
> 

---

# 7. Unchecked Exceptions

Unchecked exceptions are exceptions that the compiler **does not force you to handle**.

They include:

1. `RuntimeException` and its subclasses
2. `Error` and its subclasses are also unchecked, though technically they are not exceptions

Common unchecked exceptions:

```
NullPointerException
ArithmeticException
ArrayIndexOutOfBoundsException
NumberFormatException
ClassCastException
IllegalArgumentException
```

Example:

```java
int x = 10 / 0;
```

This compiles successfully but throws:

```
ArithmeticException
```

---

## Checked vs Unchecked — Must Know

| Checked | Unchecked |
| --- | --- |
| Checked by compiler | Not checked by compiler |
| Must catch or declare | Catching is optional |
| Subclasses of `Exception` excluding `RuntimeException` | `RuntimeException` and subclasses |
| Usually external/recoverable conditions | Usually programming/logic errors |
| `IOException` | `NullPointerException` |
| `SQLException` | `ArithmeticException` |
| `FileNotFoundException` | `ArrayIndexOutOfBoundsException` |

### Interview trap

**Q: Is `Error` a checked exception?**

No.

More precisely:

```
Throwable
├── Error        → unchecked
└── Exception
    ├── RuntimeException → unchecked
    └── Others           → checked
```

So remember:

> **Checked = Exception − RuntimeException and its subclasses.**
> 

> **Unchecked = RuntimeException hierarchy + Error hierarchy.**
> 

---

# 8. `try`

`try` contains code that may throw an exception.

```java
try {
    int result = 10 / 0;
}
```

A `try` block must be followed by at least one `catch` or a `finally`.

```java
try {
    // risky code
} catch (Exception e) {
    // handling
}
```

or:

```java
try {
    // risky code
} finally {
    // cleanup
}
```

---

# 9. `catch`

`catch` handles an exception thrown from the associated `try` block.

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

Important: The `catch` parameter contains the exception object.

```java
e.getMessage();
e.printStackTrace();
```

---

# 10. `finally`

`finally` is used for cleanup code and normally executes whether an exception occurs or not.

```java
try {
    int x = 10 / 2;
} catch (Exception e) {
    System.out.println("Error");
} finally {
    System.out.println("Cleanup");
}
```

Output:

```
Cleanup
```

Typical use:

```
Close files
Close database connections
Release resources
```

Important exception: `finally` may not execute if the JVM terminates abruptly, for example through `System.exit()`.

---

# 11. `throw`

`throw` is used to **explicitly throw an exception**.

```java
if (age < 18) {
    throw new IllegalArgumentException("Age must be 18 or above");
}
```

Syntax:

```java
throw new ExceptionType("message");
```

`throw` works with an actual exception object.

---

# 12. `throws`

`throws` is used in a method declaration to indicate that the method **may throw** one or more exceptions.

```java
public void readFile() throws IOException {
    FileReader file = new FileReader("data.txt");
}
```

The caller can then handle it:

```java
try {
    readFile();
} catch (IOException e) {
    System.out.println("File error");
}
```

### `throw` vs `throws`

| `throw` | `throws` |
| --- | --- |
| Actually throws an exception | Declares possible exceptions |
| Used inside method/body | Used in method signature |
| Throws one exception object at a time | Can declare multiple exceptions |
| `throw new IOException()` | `throws IOException` |

---

# 13. Multiple Catch

You can have multiple `catch` blocks for different exceptions.

```java
try {
    int[] arr = {1, 2, 3};
    System.out.println(arr[5]);
} catch (ArithmeticException e) {
    System.out.println("Arithmetic error");
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Array error");
}
```

Only the **first matching catch block** executes.

Important: Put specific exceptions before general exceptions.

Correct:

```java
catch (ArithmeticException e) {
} catch (Exception e) {
}
```

Incorrect:

```java
catch (Exception e) {
} catch (ArithmeticException e) {
    // unreachable
}
```

Because `Exception` already catches `ArithmeticException`.

---

# 14. Nested Try

A `try` block can exist inside another `try`, `catch`, or `finally`.

```java
try {
    System.out.println("Outer");

    try {
        int x = 10 / 0;
    } catch (ArithmeticException e) {
        System.out.println("Inner catch");
    }

} catch (Exception e) {
    System.out.println("Outer catch");
}
```

Output:

```
Outer
Inner catch
```

Nested `try` is useful when different sections of code require different exception handling.

---

# 15. Custom Exceptions

You can create your own exception class by extending `Exception` or `RuntimeException`.

## Checked custom exception

```java
class InsufficientBalanceException extends Exception {

    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

Use it:

```java
void withdraw(double amount) throws InsufficientBalanceException {

    if (amount > 10000) {
        throw new InsufficientBalanceException(
            "Insufficient balance"
        );
    }
}
```

If you extend `Exception` → checked exception.

If you extend `RuntimeException` → unchecked exception.

```java
class InvalidAgeException extends RuntimeException {

    public InvalidAgeException(String message) {
        super(message);
    }
}
```

---

# 16. Exception Propagation

Exception propagation means an exception moves **up the call stack** until it is handled.

```java
void method3() {
    int x = 10 / 0;
}

void method2() {
    method3();
}

void method1() {
    method2();
}
```

If `method3()` doesn't handle the exception:

```
method3()
   ↓
method2()
   ↓
method1()
   ↓
JVM
```

If `method1()` catches it:

```java
void method1() {

    try {
        method2();
    } catch (ArithmeticException e) {
        System.out.println("Handled");
    }
}
```

Then propagation stops at `method1()`.

**Important:** Unchecked exceptions automatically propagate up the call stack. Checked exceptions generally must be caught or declared.

---

# 17. Stack Trace

A stack trace shows the sequence of method calls that led to an exception.

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    e.printStackTrace();
}
```

Typical output:

```
java.lang.ArithmeticException: / by zero
    at Calculator.divide(Calculator.java:10)
    at Main.main(Main.java:5)
```

It tells you:

- Exception type
- Exception message
- Class
- Method
- Line number
- Call sequence

Very useful for debugging.

---

# 18. `try-with-resources`

`try-with-resources` automatically closes resources that implement `AutoCloseable`.

Instead of:

```java
FileReader reader = null;

try {
    reader = new FileReader("data.txt");
} finally {
    if (reader != null) {
        reader.close();
    }
}
```

Use:

```java
try (FileReader reader = new FileReader("data.txt")) {

    // use reader

} catch (IOException e) {
    e.printStackTrace();
}
```

The resource is automatically closed.

Multiple resources:

```java
try (
    FileReader reader = new FileReader("input.txt");
    BufferedReader br = new BufferedReader(reader)
) {
    System.out.println(br.readLine());
}
```

Resources are closed automatically, generally in **reverse order of creation**.

**Interview:** `try-with-resources` works with classes implementing `AutoCloseable` (and therefore `Closeable`).

---

# 19. Suppressed Exceptions

Suppressed exceptions occur mainly with `try-with-resources`.

Suppose both the main operation and resource closing throw exceptions:

```java
try (MyResource resource = new MyResource()) {
    throw new Exception("Main exception");
}
```

If `close()` also throws an exception, the exception from the main `try` body remains the **primary exception**, while the close exception becomes **suppressed**.

You can access it:

```java
catch (Exception e) {

    for (Throwable suppressed : e.getSuppressed()) {
        System.out.println(suppressed);
    }
}
```

Important:

```
Main exception → Primary
close() exception → Suppressed
```

This is an important `try-with-resources` interview concept.

---

# 20. Multi-Catch

Java allows multiple exception types in a single `catch` block.

```java
try {
    // risky code
} catch (IOException | SQLException e) {
    System.out.println("Operation failed");
}
```

This avoids duplicate handling code.

Important restriction:

You cannot use related exceptions in the same multi-catch.

Incorrect:

```java
catch (Exception | IOException e) {
}
```

Because `IOException` is already a subclass of `Exception`.

Also, the multi-catch exception variable is implicitly `final`, so you cannot assign another exception to `e`.

---

# 21. Exception Handling Best Practices

For interviews and real projects, remember these:

1. **Catch specific exceptions**, not unnecessarily broad `Exception`.

```java
catch (IOException e) {
}
```

is preferable to:

```java
catch (Exception e) {
}
```

1. **Don't swallow exceptions.**

Avoid:

```java
catch (Exception e) {
}
```

1. **Don't use exceptions for normal control flow.**

Bad:

```java
try {
    Integer.parseInt(input);
} catch (Exception e) {
    // using exception as normal logic
}
```

1. **Provide meaningful exception messages.**

```java
throw new IllegalArgumentException(
    "Age cannot be negative"
);
```

1. **Preserve the original cause when wrapping exceptions.**

```java
catch (SQLException e) {
    throw new RuntimeException(
        "Database operation failed",
        e
    );
}
```

1. **Use custom exceptions when they represent meaningful domain failures.**

```
InsufficientBalanceException
InvalidOrderException
UserNotFoundException
```

1. **Use `try-with-resources` for resource management.**
2. **Don't catch `Error` unless you have a very specific reason.**
3. **Log exceptions appropriately instead of simply printing stack traces in production.**
4. **Don't expose sensitive information through exception messages or stack traces.**

### Most important interview distinctions

```
try       → risky code
catch     → handle exception
finally   → cleanup
throw     → explicitly throw exception
throws    → declare possible exception
```

And:

```
Multiple catch → separate handlers
Multi-catch    → one handler for multiple unrelated exception types
Nested try     → try inside another try/catch/finally
Propagation    → exception moves up the call stack
Stack trace    → shows where/how the exception occurred
```

---

# 22. Error vs Exception

| Error | Exception |
| --- | --- |
| Serious JVM/system-level problem | Application-level problem |
| Usually not recoverable | Often recoverable |
| Generally should not be handled | Can be handled |
| `OutOfMemoryError` | `IOException` |
| `StackOverflowError` | `SQLException` |
| `NoClassDefFoundError` | `NullPointerException` |

Hierarchy:

```
Throwable
├── Error
└── Exception
    ├── RuntimeException
    └── Checked Exceptions
```

Example:

```java
// Error
StackOverflowError

// Exception
ArithmeticException
```

**Interview point:** `Error` and `Exception` are both subclasses of `Throwable`, but they represent fundamentally different failure categories.

---

# 23. Checked vs Unchecked Exception

| Checked | Unchecked |
| --- | --- |
| Checked by compiler | Not checked by compiler |
| Must be caught or declared | Catch/declare is optional |
| `Exception` subclasses excluding `RuntimeException` | `RuntimeException` subclasses |
| Usually external/recoverable conditions | Usually programming/logic errors |
| `IOException` | `NullPointerException` |
| `SQLException` | `ArithmeticException` |
| `FileNotFoundException` | `NumberFormatException` |

Example checked:

```java
void read() throws IOException {
    FileReader f = new FileReader("a.txt");
}
```

Example unchecked:

```java
void calculate() {
    int x = 10 / 0; // compiles
}
```

Remember:

```
Checked   = Exception - RuntimeException
Unchecked = RuntimeException + Error
```

---

# 24. `throw` vs `throws`

| `throw` | `throws` |
| --- | --- |
| Actually throws an exception | Declares possible exceptions |
| Used inside method body | Used in method signature |
| Followed by exception object | Followed by exception class names |
| One exception at a time | Can declare multiple |
| `throw new Exception()` | `throws Exception` |

Example:

```java
void checkAge(int age) {

    if (age < 18) {
        throw new IllegalArgumentException("Invalid age");
    }
}
```

Here, `throw` actually creates/throws the exception.

```java
void readFile() throws IOException {
    // file operation
}
```

Here, `throws` tells the caller that the method may produce an `IOException`.

**Easy interview trick:**

> `throw` = **do it**
> 

> `throws` = **declare it**
> 

---

# 25. `final` vs `finally` vs `finalize`

These three are completely different.

| `final` | `finally` | `finalize()` |
| --- | --- | --- |
| Keyword | Block | Method |
| Used with variable, method, class | Used with exception handling | Historically associated with GC cleanup |
| Prevents modification/overriding/inheritance | Executes cleanup code after try/catch | Called by GC historically before object reclamation |
| Compile-time concept | Exception-handling concept | GC/lifecycle concept |
| Still used | Still used | **Deprecated and should not be used** |

### `final`

```java
final int x = 10;

// x = 20;  // Error
```

Three common uses:

```
final variable → cannot be reassigned
final method   → cannot be overridden
final class    → cannot be inherited
```

Example:

```java
final class A {
}
```

---

### `finally`

Used with exception handling.

```java
try {
    System.out.println("Try");
} catch (Exception e) {
    System.out.println("Catch");
} finally {
    System.out.println("Always cleanup");
}
```

It is commonly used for cleanup, although **try-with-resources is preferred for resource management**.

---

### `finalize()`

`finalize()` was a method from `Object` that was historically associated with garbage collection.

```java
@Override
protected void finalize() throws Throwable {
    // old cleanup mechanism
}
```

However, **`finalize()` has been deprecated since Java 9 and removed from the recommended programming model**. Modern Java code should use:

```
try-with-resources
AutoCloseable
Cleaner (where appropriate)
```

instead.

### Most important interview answer

If asked:

**"What is the difference between final, finally and finalize?"**

Say:

> `final` is a keyword used to restrict variables, methods, and classes. `finally` is a block used for cleanup in exception handling. `finalize()` was a garbage-collection-related method, but it is deprecated and should not be used in modern Java.
>
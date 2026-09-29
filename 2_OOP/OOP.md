# OOP

### 1. Class

A **class is a blueprint/template for creating objects**. It defines the object's properties and behaviors.

```java
class Student {
    String name; // property
    int age;

    void study() { // behavior
        System.out.println("Studying");
    }
}
```

Here, `Student` is a class.

---

### 2. Object

An **object is an instance of a class**. It represents an actual entity and has its own state and behavior.

```java
Student s = new Student();

s.name = "Atharva";
s.age = 22;
s.study();
```

Here:

- `Student` → Class
- `s` → Reference variable
- `new Student()` → Object creation
- `name`, `age` → State
- `study()` → Behavior

**Interview difference:**

```
Class  → Blueprint
Object → Instance of the class
```

**Example:**

`Car` is a class, while `BMW`, `Toyota`, etc. can be objects created from that class.

---

### 3. State and Behavior

An object mainly has **state and behavior**.

- **State** → Data/properties of an object.
- **Behavior** → Actions performed by an object through methods.

```java
class Student {
    String name; // State
    int age;     // State

    void study() { // Behavior
        System.out.println("Studying");
    }
}
```

Here:

- `name`, `age` → State
- `study()` → Behavior

---

### 4. Instance Variables

Instance variables are **variables declared inside a class but outside methods/constructors**.

Each object gets its **own copy**.

```java
class Student {
    String name;
    int age;
}

Student s1 = new Student();
Student s2 = new Student();

s1.age = 20;
s2.age = 22;
```

Here `s1` and `s2` have separate `age` values.

**Important:** Instance variables have **default values** if not explicitly initialized.

---

### 5. Instance Methods

Instance methods are methods that **belong to an object** and can directly access instance variables.

```java
class Student {
    String name;

    void display() {
        System.out.println(name);
    }
}

Student s = new Student();

s.name = "Atharva";
s.display();
```

Instance methods are normally called using an **object**:

```java
s.display();
```

**Key difference:**

```
Instance variable → Object's data
Instance method   → Object's behavior
```

---

### 6. Class / Static Variables

A **static variable belongs to the class**, not to individual objects.

- Only **one shared copy** exists.
- Shared by all objects.
- Access using the class name.

```java
class Student {
    static String college = "I2IT";
}

System.out.println(Student.college);
```

Example:

```java
Student s1 = new Student();
Student s2 = new Student();

s1.college = "ABC";

System.out.println(s2.college); // ABC
```

Both objects share the same `college`.

---

### 7. Static Methods

A static method **belongs to the class**, so it can be called without creating an object.

```java
class Calculator {
    static int add(int a, int b) {
        return a + b;
    }
}

int result = Calculator.add(10, 20);
```

**Important:**

- Can directly access **static members**.
- Cannot directly access **instance variables/methods**.

```java
class Student {
    int age = 20;

    static void show() {
        // System.out.println(age); // Error
    }
}
```

---

### 8. Constructors

A constructor is used to **initialize an object**.

- Same name as class.
- No return type.
- Automatically called when an object is created.

```java
class Student {
    String name;

    Student(String name) {
        this.name = name;
    }
}

Student s = new Student("Atharva");
```

Here `Student(String name)` is the constructor.

**Key difference:**

```
Static variable     → Shared by all objects
Static method       → Belongs to class
Instance variable   → Separate for each object
Constructor         → Initializes each object
```

---

## 9. Four Pillars of OOP

The four fundamental pillars of OOP are **Encapsulation, Abstraction, Inheritance, and Polymorphism**.

### 9.1 Encapsulation

**Wrapping data and methods together in a class and restricting direct access to the data.**

Usually achieved using `private` variables and `public` getters/setters.

```java
class Student {
    private int age;

    public void setAge(int age) {
        this.age = age;
    }

    public int getAge() {
        return age;
    }
}
```

Here, `age` cannot be accessed directly from outside the class.

**Interview line:** Encapsulation provides **data hiding and controlled access**.

---

### 9.2 Abstraction

**Hiding implementation details and exposing only the necessary functionality.**

Achieved using **abstract classes and interfaces**.

```java
abstract class Animal {
    abstract void sound();
}

class Dog extends Animal {
    void sound() {
        System.out.println("Bark");
    }
}
```

The user knows `sound()` exists, but the implementation is hidden behind the abstraction.

**Interview line:** Abstraction focuses on **what an object does**, not **how it does it**.

---

### 9.3 Inheritance

A child class **acquires properties and methods of a parent class**.

Achieved using `extends`.

```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {
}

Dog d = new Dog();

d.eat();
```

`Dog` inherits `eat()` from `Animal`.

**Benefit:** Code reusability.

---

### 9.4 Polymorphism

Polymorphism means **one interface/name can have multiple forms**.

Two types:

**Compile-time polymorphism → Method Overloading**

```java
void add(int a, int b) {
    System.out.println(a + b);
}

void add(int a, int b, int c) {
    System.out.println(a + b + c);
}
```

**Runtime polymorphism → Method Overriding**

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

Animal a = new Dog();

a.sound(); // Bark
```

**Important interview distinction:**

```
Encapsulation  → How data is protected
Abstraction    → What is exposed / implementation hidden
Inheritance    → Reuse parent functionality
Polymorphism   → Same method/interface, different behavior
```

---

## 10. Encapsulation

Encapsulation means **bundling data and methods together in a class and controlling access to the data**.

### Getters and Setters

Used to **read and modify private variables**.

```java
class Student {
    private int age;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

```java
Student s = new Student();

s.setAge(22);

System.out.println(s.getAge());
```

### Data Hiding

Making data `private` prevents direct access from outside the class.

```java
class Student {
    private int age;
}
```

```java
// s.age = 22;  // Error
```

Access is provided through methods such as getters/setters.

### Access Modifiers

Access modifiers control **visibility/accessibility**.

- `private` → Same class only
- `default` → Same package
- `protected` → Same package + subclasses
- `public` → Everywhere

### Interview Point

**Encapsulation = Data hiding + controlled access.**

Encapsulation is commonly implemented using **private fields + public getters/setters**.

---

## 11. Inheritance

Inheritance allows a child class to **acquire properties and methods of a parent class**.

```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {
}

Dog d = new Dog();

d.eat();
```

Here, `Dog` inherits from `Animal`.

### Single Inheritance

One child inherits from one parent.

```
Animal
   ↓
  Dog
```

```java
class Animal {
}

class Dog extends Animal {
}
```

### Multilevel Inheritance

Inheritance happens in multiple levels.

```
Animal
   ↓
  Dog
   ↓
 Puppy
```

```java
class Animal {
}

class Dog extends Animal {
}

class Puppy extends Dog {
}
```

### Hierarchical Inheritance

Multiple child classes inherit from the same parent.

```
       Animal
       /    \
     Dog    Cat
```

```java
class Animal {
}

class Dog extends Animal {
}

class Cat extends Animal {
}
```

### Why Java doesn't support multiple class inheritance?

Java doesn't allow:

```java
class C extends A, B  // Not allowed
```

Main reason: **ambiguity**, commonly called the **Diamond Problem**.

If both `A` and `B` have the same method, Java wouldn't know which implementation `C` should inherit.

Java solves this by allowing **multiple interface inheritance** with rules for resolving default methods.

### `extends`

`extends` is used for **class-to-class inheritance**.

```java
class Dog extends Animal {
}
```

### `super`

`super` refers to the **immediate parent class**.

Used to access parent members or call the parent constructor.

```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {

    void show() {
        super.eat();
    }
}
```

### Constructor Chaining

When a child object is created, the **parent constructor executes first**, followed by the child constructor.

```java
class Animal {
    Animal() {
        System.out.println("Animal");
    }
}

class Dog extends Animal {
    Dog() {
        System.out.println("Dog");
    }
}

Dog d = new Dog();
```

Output:

```
Animal
Dog
```

`super()` is automatically inserted as the first statement of a child constructor if you don't explicitly write it.

### IS-A Relationship

Inheritance represents an **IS-A relationship**.

```
Dog IS-A Animal
Car IS-A Vehicle
```

```java
class Dog extends Animal {
}
```

**Interview point:** `extends` represents an IS-A relationship between classes.

---

## 12. Polymorphism

Polymorphism means **one method/interface can have different forms or behaviors**.

There are two main types:

### Compile-time Polymorphism

Resolved by the **compiler**.

### Method Overloading

Multiple methods with the **same name but different parameter lists**.

```java
class Calculator {

    int add(int a, int b) {
        return a + b;
    }

    int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

```java
Calculator c = new Calculator();

c.add(10, 20);
c.add(10, 20, 30);
```

**Important:** Changing only the return type does **not** create overloading.

---

### Runtime Polymorphism

Resolved at **runtime** using method overriding.

### Method Overriding

A child class provides its own implementation of a parent method.

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

### Dynamic Method Dispatch

When a **parent reference refers to a child object**, the overridden method is selected at runtime.

```java
Animal a = new Dog();

a.sound();
```

Output:

```
Bark
```

The reference type is `Animal`, but the actual object is `Dog`, so `Dog`'s method executes.

---

### Upcasting

Converting a **child reference to a parent reference**.

It is automatic.

```java
Dog d = new Dog();

Animal a = d;
```

Or:

```java
Animal a = new Dog();
```

Commonly used for runtime polymorphism.

---

### Downcasting

Converting a **parent reference back to a child reference**.

It requires explicit casting.

```java
Animal a = new Dog();

Dog d = (Dog) a;

d.sound();
```

**Important:** Downcasting is safe only when the actual object is an instance of that child class.

```java
Animal a = new Animal();

Dog d = (Dog) a; // ClassCastException
```

### Quick Interview Summary

```
Overloading        → Compile-time
Overriding         → Runtime
Dynamic dispatch   → Overridden method decided at runtime
Upcasting          → Child → Parent
Downcasting        → Parent → Child
```

---

## 13. Abstraction

Abstraction means **hiding implementation details and exposing only the required functionality**.

### Abstract Class

A class declared with `abstract`. It can contain both **abstract and concrete methods**.

```java
abstract class Animal {

    abstract void sound();

    void eat() {
        System.out.println("Eating");
    }
}
```

It **cannot be instantiated directly**.

```java
// Animal a = new Animal(); // Error
```

### Abstract Method

A method declared without a body.

```java
abstract void sound();
```

The child class must implement it unless the child is also abstract.

```java
class Dog extends Animal {

    void sound() {
        System.out.println("Bark");
    }
}
```

---

### Interface

An interface defines a **contract** that implementing classes must follow.

```java
interface Animal {
    void sound();
}

class Dog implements Animal {

    public void sound() {
        System.out.println("Bark");
    }
}
```

A class uses `implements` to implement an interface.

---

### Interface Default Method

Since Java 8, an interface can have a method with a **body using** **`default`**.

```java
interface Animal {

    default void eat() {
        System.out.println("Eating");
    }
}
```

Implementing classes automatically get this method but can override it.

---

### Interface Static Method

An interface can have a `static` method.

```java
interface MathUtil {

    static void show() {
        System.out.println("Hello");
    }
}

MathUtil.show();
```

**Important:** Interface static methods are called using the **interface name**, not through an implementing object.

---

### Functional Interface

An interface containing **exactly one abstract method**.

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}
```

It can be used with **lambda expressions**.

```java
Calculator c = (a, b) -> a + b;
```

Examples: `Runnable`, `Comparator`, `Predicate`, `Function`.

---

### Multiple Inheritance Using Interfaces

Java does not support multiple inheritance with classes:

```java
// class C extends A, B  // Not allowed
```

But a class can implement **multiple interfaces**:

```java
interface A {
    void showA();
}

interface B {
    void showB();
}

class C implements A, B {

    public void showA() {
    }

    public void showB() {
    }
}
```

So Java supports multiple inheritance **through interfaces**.

**Tricky:** What happens if two interfaces have the same default method?

The implementing class must **override the method** to resolve the conflict.

```
Class → extends → one class
Class → implements → multiple interfaces
```

---

## 14. Important OOP Comparisons

### Abstract Class vs Interface

| Abstract Class | Interface |
| --- | --- |
| `abstract class` | `interface` |
| Can have constructors | Cannot have constructors |
| Can have instance variables | Fields are `public static final` by default |
| Can have abstract + concrete methods | Can have abstract, `default`, and `static` methods |
| Class extends one abstract class | Class can implement multiple interfaces |
| `extends` | `implements` |

---

### Overloading vs Overriding

| Overloading | Overriding |
| --- | --- |
| Same method name | Same method signature |
| Different parameters | Same parameters |
| Compile-time polymorphism | Runtime polymorphism |
| Usually within same class | Requires inheritance |
| Return type alone cannot overload | Return type must be compatible |

```java
// Overloading
add(int a, int b);
add(int a, int b, int c);

// Overriding
class Dog extends Animal {
    void sound() {
    }
}
```

---

### Encapsulation vs Abstraction

| Encapsulation | Abstraction |
| --- | --- |
| Protects/hides data | Hides implementation details |
| Controls access | Shows only essential functionality |
| Mainly using `private` + getters/setters | Using abstract classes/interfaces |
| Focuses on **how data is accessed** | Focuses on **what is exposed** |

**Easy:**

```
Encapsulation → Data hiding
Abstraction   → Implementation hiding
```

---

### Inheritance vs Composition

**Inheritance:** Represents an **IS-A** relationship.

```java
class Dog extends Animal {
}
```

Dog **IS-A** Animal.

**Composition:** Represents a **HAS-A** relationship.

```java
class Car {
    Engine engine = new Engine();
}
```

Car **HAS-A** Engine.

**Interview point:** Prefer composition when you want flexibility and loose coupling rather than unnecessary inheritance.

---

### IS-A vs HAS-A

**IS-A → Inheritance**

```
Dog IS-A Animal
```

**HAS-A → Composition/Association**

```
Car HAS-A Engine
```

```java
class Dog extends Animal {
} // IS-A

class Car {
    Engine engine;
} // HAS-A
```

---

### Static vs Instance

| Static | Instance |
| --- | --- |
| Belongs to class | Belongs to object |
| One shared copy | Each object has its own copy |
| Can access without object | Usually requires object |
| `ClassName.method()` | `object.method()` |

```java
class Student {
    static String college = "I2IT";
    String name;
}
```

`college` → static/shared

`name` → instance/separate for each object

---

### Final Class vs Abstract Class

| Final Class | Abstract Class |
| --- | --- |
| Cannot be inherited | Designed to be inherited |
| Cannot have subclasses | Can have subclasses |
| Can be instantiated | Cannot be instantiated |
| Used to prevent inheritance | Used to provide abstraction |

```java
final class A {
}

// class B extends A {} // Error
```

```java
abstract class A {
    abstract void show();
}

// A obj = new A(); // Error
```

**Key interview point:**

```
final class     → "Don't inherit me"
abstract class  → "You must inherit/implement me to create a concrete object"
```
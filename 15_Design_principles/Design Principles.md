# Design Principles

For fresher interviews, don't just memorize definitions. For each principle/pattern, understand **what problem it solves, how it works, and where you would use it**.

# Design Principles

## 1. SOLID Principles

**SOLID** is a set of five object-oriented design principles used to create software that is easier to maintain, extend, test, and modify.

```
S → Single Responsibility Principle
O → Open/Closed Principle
L → Liskov Substitution Principle
I → Interface Segregation Principle
D → Dependency Inversion Principle
```

The main goal is to reduce:

- Tight coupling
- Fragile code
- Unnecessary dependencies
- Difficult testing
- Difficult maintenance

---

## 2. Single Responsibility Principle — SRP

> A class should have one reason to change.
> 

It does **not** necessarily mean a class can have only one method.

Bad design:

```java
class UserService {

    void registerUser() {
        // registration
    }

    void sendEmail() {
        // email
    }

    void generateReport() {
        // report
    }
}
```

This class has multiple responsibilities.

A better design:

```java
class UserService {

    void registerUser() {
    }
}

class EmailService {

    void sendEmail() {
    }
}

class ReportService {

    void generateReport() {
    }
}
```

Now each class has a focused responsibility.

Interview answer:

> SRP states that a class should have one reason to change, meaning its responsibilities should be cohesive rather than unrelated.
> 

---

## 3. Open/Closed Principle — OCP

> Software entities should be open for extension but closed for modification.
> 

Suppose we have:

```java
class PaymentService {

    void pay(String type) {

        if (type.equals("CARD")) {
            // card payment
        } else if (type.equals("UPI")) {
            // UPI payment
        }
    }
}
```

Adding another payment type requires modifying the existing class.

Better:

```java
interface Payment {

    void pay();
}
```

```java
class CardPayment implements Payment {

    public void pay() {
        System.out.println("Card payment");
    }
}
```

```java
class UPIPayment implements Payment {

    public void pay() {
        System.out.println("UPI payment");
    }
}
```

Now we can add:

```java
class CashPayment implements Payment {

    public void pay() {
        System.out.println("Cash payment");
    }
}
```

without modifying the existing payment implementations.

Interview answer:

> OCP means existing behavior should generally be extendable through new code rather than repeatedly modifying stable existing code.
> 

---

## 4. Liskov Substitution Principle — LSP

> Objects of a subclass should be usable wherever objects of the superclass are expected without breaking the correctness of the program.
> 

Example:

```java
class Bird {

    void fly() {
    }
}
```

If we create:

```java
class Penguin extends Bird {

    @Override
    void fly() {
        throw new UnsupportedOperationException();
    }
}
```

we have a design problem.

A `Penguin` is a bird, but it cannot satisfy the behavioral contract implied by `Bird.fly()`.

Better:

```java
interface Bird {
}
```

```java
interface FlyingBird {

    void fly();
}
```

```java
class Eagle implements Bird, FlyingBird {

    public void fly() {
    }
}
```

```java
class Penguin implements Bird {
}
```

The key idea isn't simply "inheritance must work syntactically."

It is:

> A subtype must preserve the behavioral expectations of its abstraction.
> 

---

## 5. Interface Segregation Principle — ISP

> Clients should not be forced to depend on methods they do not use.
> 

Bad:

```java
interface Machine {

    void print();

    void scan();

    void fax();
}
```

A basic printer might only support printing:

```java
class BasicPrinter implements Machine {

    public void print() {
    }

    public void scan() {
        // Not supported
    }

    public void fax() {
        // Not supported
    }
}
```

Better:

```java
interface Printer {

    void print();
}
```

```java
interface Scanner {

    void scan();
}
```

```java
interface Fax {

    void fax();
}
```

Now classes implement only what they actually support.

---

## 6. Dependency Inversion Principle — DIP

> High-level modules should not depend directly on low-level concrete implementations. Both should depend on abstractions.
> 

Bad:

```java
class MySQLDatabase {

    void save() {
    }
}
```

```java
class UserService {

    private MySQLDatabase database = new MySQLDatabase();

    void saveUser() {
        database.save();
    }
}
```

`UserService` is tightly coupled to MySQL.

Better:

```java
interface Database {

    void save();
}
```

```java
class MySQLDatabase implements Database {

    public void save() {
    }
}
```

```java
class UserService {

    private final Database database;

    UserService(Database database) {
        this.database = database;
    }

    void saveUser() {
        database.save();
    }
}
```

Now:

```
UserService
     ↓
 Database interface
     ↑
 ┌───┴──────────┐
MySQL        MongoDB
```

This also leads directly to **Dependency Injection**.

---

# SOLID Summary

## 7. SOLID Cheat Sheet

```
S → Single Responsibility
    One reason to change

O → Open/Closed
    Open for extension,
    closed for modification

L → Liskov Substitution
    Subtypes must honor
    the parent contract

I → Interface Segregation
    Don't force clients to
    depend on unused methods

D → Dependency Inversion
    Depend on abstractions,
    not concrete implementations
```

A useful memory trick:

```
S → Responsibility
O → Extension
L → Substitution
I → Interfaces
D → Dependencies
```

---

# Other Design Principles

## 8. DRY — Don't Repeat Yourself

> Every piece of knowledge should have a single authoritative representation in a system.
> 

Bad:

```java
double calculateTaxForOrder() {
    return price * 0.18;
}
```

and elsewhere:

```java
double calculateTaxForProduct() {
    return price * 0.18;
}
```

The same business rule is duplicated.

Better:

```java
class TaxCalculator {

    double calculateTax(double price) {
        return price * 0.18;
    }
}
```

Then:

```java
taxCalculator.calculateTax(price);
```

Benefits:

- Less duplication
- Easier maintenance
- Fewer inconsistencies
- Centralized business logic

Important:

> DRY is about avoiding duplicated knowledge/logic, not blindly eliminating every repeated line of code.
> 

---

## 9. KISS — Keep It Simple, Stupid

> Prefer the simplest solution that correctly solves the problem.
> 

Suppose you need to check whether a number is even.

Don't build unnecessary abstractions.

Simple:

```java
boolean isEven(int n) {
    return n % 2 == 0;
}
```

KISS discourages:

- Unnecessary abstractions
- Over-engineering
- Excessive complexity
- Complicated logic when a simple solution works

Interview answer:

> KISS means design and implementation should remain as simple as reasonably possible while satisfying the requirements.
> 

---

## 10. YAGNI — You Aren't Gonna Need It

> Don't implement functionality until it is actually required.
> 

Suppose your application currently needs:

```
Login
Register
Logout
```

Don't immediately build:

```
OAuth
Biometric login
SSO
Multi-region authentication
20 authentication providers
```

unless there is a requirement for them.

YAGNI helps avoid:

- Unused code
- Unnecessary complexity
- Extra maintenance
- Premature architecture

Difference:

```
KISS
→ Keep existing solution simple

YAGNI
→ Don't build unnecessary functionality
```

---

## 11. Composition Over Inheritance

> Prefer building objects by combining smaller objects rather than creating deep inheritance hierarchies when inheritance isn't required.
> 

Inheritance:

```java
class Car extends Vehicle {
}
```

Composition:

```java
class Engine {

    void start() {
    }
}
```

```java
class Car {

    private Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }
}
```

Now:

```
Car
 ↓ HAS-A
Engine
```

instead of:

```
Car
 ↓ IS-A
Vehicle
```

Composition provides:

- Lower coupling
- Better flexibility
- Easier testing
- Runtime behavior substitution
- Less fragile inheritance hierarchies

A good rule:

> Use inheritance for a genuine, stable **IS-A** relationship; use composition when you primarily need to reuse or combine behavior.
> 

---

## 12. Immutability

An immutable object is an object whose **state cannot be changed after construction**.

Example:

```java
final class Student {

    private final int id;
    private final String name;

    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

There are:

- No setters
- `final` fields
- State initialized through constructor
- No methods that modify the state

Usage:

```java
Student s = new Student(101, "Atharva");
```

You cannot change the object's fields after construction.

### Why immutability?

Benefits:

- Thread safety
- Easier reasoning
- Safe sharing
- Good for keys in hash-based collections
- Prevents accidental state changes

Examples from Java:

```java
String
Integer
LocalDate
LocalDateTime
```

are immutable.

Important:

> `final` reference does not automatically make the referenced object immutable.
> 

For example:

```java
final List<String> list = new ArrayList<>();
```

You cannot assign a different list to `list`, but you can still modify the existing list:

```java
list.add("Java");
```

---

## 13. Dependency Injection — DI

Dependency Injection means:

> An object's dependencies are provided to it from outside rather than the object creating them itself.
> 

Without DI:

```java
class UserService {

    private EmailService emailService = new EmailService();
}
```

`UserService` creates its dependency.

With DI:

```java
class UserService {

    private final EmailService emailService;

    UserService(EmailService emailService) {
        this.emailService = emailService;
    }
}
```

The dependency is supplied externally:

```java
EmailService emailService = new EmailService();

UserService service = new UserService(emailService);
```

### Types of DI

```
1. Constructor Injection
2. Setter Injection
3. Field Injection
```

Constructor injection is generally preferred because dependencies are explicit and can be made `final`.

Spring heavily uses Dependency Injection.

Example:

```java
@Service
class UserService {

    private final UserRepository repository;

    UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

Conceptually:

```
Spring Container
      ↓
creates Repository
      ↓
creates UserService
      ↓
injects Repository
```

---

## 14. Loose Coupling

Loose coupling means components have **minimal dependency on each other's concrete implementations**.

Tightly coupled:

```java
class NotificationService {

    private EmailNotification notification = new EmailNotification();
}
```

If you want SMS, you must modify the class.

Loosely coupled:

```java
interface Notification {

    void send();
}
```

```java
class EmailNotification implements Notification {

    public void send() {
    }
}
```

```java
class SMSNotification implements Notification {

    public void send() {
    }
}
```

```java
class NotificationService {

    private final Notification notification;

    NotificationService(Notification notification) {
        this.notification = notification;
    }
}
```

Now:

```
NotificationService
        ↓
   Notification
      ↑     ↑
    Email   SMS
```

Benefits:

- Easier testing
- Easier replacement
- Easier maintenance
- Better extensibility

---

## 15. High Cohesion

Cohesion describes how closely related the responsibilities inside a module/class are.

**High cohesion** means a class has a focused, logically related responsibility.

Good:

```java
class UserRepository {

    void saveUser() {
    }

    User findUser() {
        return null;
    }

    void deleteUser() {
    }
}
```

These methods are all related to user persistence.

Bad:

```java
class UserManager {

    void saveUser() {
    }

    void sendEmail() {
    }

    void calculateSalary() {
    }

    void generatePDF() {
    }
}
```

Unrelated responsibilities are mixed together.

Ideal design:

```
UserRepository
→ database operations

EmailService
→ email operations

SalaryService
→ salary calculations

PdfService
→ PDF generation
```

High cohesion and loose coupling are complementary:

```
High Cohesion
→ Keep related responsibilities together

Loose Coupling
→ Keep unrelated components independent
```

---

# Basic Design Patterns

## 16. What is a Design Pattern?

A design pattern is a **reusable general solution to a recurring software design problem**.

It is not a copy-paste code template.

Think:

```
Problem
   ↓
Known recurring structure
   ↓
Design Pattern
   ↓
Reusable design solution
```

The patterns you're focusing on are:

```
Creational:
→ Singleton
→ Factory
→ Builder

Behavioral:
→ Strategy
→ Observer

Structural:
→ Adapter
```

---

# Singleton

## 17. Singleton Pattern

Singleton ensures that a class has **only one instance** and provides a controlled way to access it.

Example:

```java
class Singleton {

    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {

        if (instance == null) {
            instance = new Singleton();
        }

        return instance;
    }
}
```

Usage:

```java
Singleton s1 = Singleton.getInstance();
Singleton s2 = Singleton.getInstance();

System.out.println(s1 == s2);
```

Output:

```
true
```

Both references point to the same object.

### Important problem

The above implementation is **not thread-safe**.

A common thread-safe approach is initialization-on-demand holder:

```java
class Singleton {

    private Singleton() {
    }

    private static class Holder {

        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

The JVM's class initialization guarantees safe initialization of the holder.

### Singleton use cases

Potential examples:

- Configuration manager
- Application-wide stateless service
- Certain caches
- Logging infrastructure

But don't make everything Singleton.

Important interview point:

> Singleton is about controlling instance creation, not simply declaring everything `static`.
> 

---

# Factory

## 18. Factory Pattern

Factory centralizes object creation so the client does not need to directly instantiate the concrete implementation.

Suppose:

```java
interface Payment {

    void pay();
}
```

Implementations:

```java
class CardPayment implements Payment {

    public void pay() {
        System.out.println("Card");
    }
}
```

```java
class UPIPayment implements Payment {

    public void pay() {
        System.out.println("UPI");
    }
}
```

Factory:

```java
class PaymentFactory {

    static Payment create(String type) {

        if (type.equals("CARD")) {
            return new CardPayment();
        }

        if (type.equals("UPI")) {
            return new UPIPayment();
        }

        throw new IllegalArgumentException(
            "Unknown payment type"
        );
    }
}
```

Client:

```java
Payment payment = PaymentFactory.create("UPI");

payment.pay();
```

Instead of:

```java
new UPIPayment();
```

the client asks the factory for a suitable object.

### Why Factory?

It separates:

```
Object creation
      from
Object usage
```

Useful when object creation is:

- Complex
- Conditional
- Based on configuration/input
- Likely to change

---

# Builder

## 19. Builder Pattern

Builder is useful for creating objects with **many optional parameters** or complex construction logic.

Without Builder:

```java
User user = new User(
    101,
    "Atharva",
    "Pune",
    22,
    "Java",
    true
);
```

This can become difficult to read and maintain.

Builder:

```java
User user = new User.Builder()
        .id(101)
        .name("Atharva")
        .city("Pune")
        .age(22)
        .skill("Java")
        .active(true)
        .build();
```

Conceptually:

```
Builder
   ↓
set required/optional values
   ↓
build()
   ↓
User object
```

### Why Builder?

Useful when:

- Many constructor parameters exist
- Many parameters are optional
- Readability matters
- Object construction has multiple steps

Common examples include:

```
Lombok @Builder
StringBuilder
HTTP request builders
Java APIs with builder-style construction
```

Don't confuse:

```
Builder Pattern
```

with:

```
StringBuilder
```

`StringBuilder` is a mutable string utility; it is not, by itself, the classic Builder design pattern.

---

# Strategy

## 20. Strategy Pattern

Strategy allows you to define **multiple interchangeable algorithms/behaviors** and select one at runtime.

Suppose we have different payment strategies:

```java
interface PaymentStrategy {

    void pay(double amount);
}
```

```java
class CardPayment implements PaymentStrategy {

    public void pay(double amount) {
        System.out.println("Paying by card");
    }
}
```

```java
class UPIPayment implements PaymentStrategy {

    public void pay(double amount) {
        System.out.println("Paying through UPI");
    }
}
```

Context:

```java
class PaymentService {

    private final PaymentStrategy strategy;

    PaymentService(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    void pay(double amount) {
        strategy.pay(amount);
    }
}
```

Usage:

```java
PaymentService service =
    new PaymentService(new UPIPayment());

service.pay(1000);
```

Change strategy:

```java
PaymentService service =
    new PaymentService(new CardPayment());
```

The `PaymentService` doesn't need to know the internal implementation of the algorithm.

### Strategy solves

A common problem:

```java
if (type.equals("A")) {
    // algorithm A
} else if (type.equals("B")) {
    // algorithm B
} else if (type.equals("C")) {
    // algorithm C
}
```

Instead, separate algorithms into strategy implementations.

---

# Observer

## 21. Observer Pattern

Observer defines a **one-to-many relationship** where multiple observers are notified when the subject's state changes.

Example:

```
             Subject
                |
       ┌────────┼────────┐
       ↓        ↓        ↓
   Observer1 Observer2 Observer3
```

Example:

```java
interface Observer {

    void update(String message);
}
```

Subject:

```java
class NewsAgency {

    private final List<Observer> observers = new ArrayList<>();

    void addObserver(Observer observer) {
        observers.add(observer);
    }

    void notifyObservers(String news) {

        for (Observer observer : observers) {
            observer.update(news);
        }
    }
}
```

Observer:

```java
class MobileApp implements Observer {

    public void update(String news) {
        System.out.println(
            "Notification: " + news
        );
    }
}
```

Usage:

```java
NewsAgency agency = new NewsAgency();

agency.addObserver(
    new MobileApp()
);

agency.notifyObservers(
    "New Java release"
);
```

The subject doesn't need to know the concrete observer implementation.

### Real-world examples

- Event listeners
- GUI events
- Notification systems
- Publish/subscribe concepts
- Reactive programming

Spring application events are also conceptually related to the Observer/Event pattern.

---

# Adapter

## 22. Adapter Pattern

Adapter allows **incompatible interfaces to work together**.

Suppose your application expects:

```java
interface PaymentProcessor {

    void pay(double amount);
}
```

But an existing third-party library provides:

```java
class LegacyPayment {

    void makePayment(double amount) {
        System.out.println(
            "Legacy payment"
        );
    }
}
```

The interfaces don't match.

Create an adapter:

```java
class PaymentAdapter implements PaymentProcessor {

    private final LegacyPayment legacyPayment;

    PaymentAdapter(LegacyPayment legacyPayment) {
        this.legacyPayment = legacyPayment;
    }

    public void pay(double amount) {
        legacyPayment.makePayment(amount);
    }
}
```

Now:

```java
PaymentProcessor processor =
    new PaymentAdapter(
        new LegacyPayment()
    );

processor.pay(1000);
```

Conceptually:

```
Application
    ↓
Expected Interface
PaymentProcessor
    ↓
   Adapter
    ↓
LegacyPayment
    ↓
Third-party system
```

### Adapter solves

> "I have an existing class/API, but its interface doesn't match what my application expects."
> 

Common real-world examples:

- Integrating legacy code
- Third-party APIs
- Different payment providers
- Different logging libraries
- External SDK wrappers

---

# 23. Basic Design Patterns — Classification

This is important for interviews.

```
CREATIONAL
│
├── Singleton
├── Factory
└── Builder
```

These focus on **object creation**.

```
BEHAVIORAL
│
├── Strategy
└── Observer
```

These focus on **object behavior and communication**.

```
STRUCTURAL
│
└── Adapter
```

This focuses on **how existing components/interfaces are connected**.

---

# 24. Pattern Comparison

| Pattern | Main Problem | Key Idea |
| --- | --- | --- |
| Singleton | Need controlled single instance | One instance |
| Factory | Object creation varies | Centralize creation |
| Builder | Complex object construction | Step-by-step construction |
| Strategy | Multiple interchangeable algorithms | Select behavior at runtime |
| Observer | One object needs to notify many | One-to-many notification |
| Adapter | Interfaces don't match | Convert one interface to another |

---

# 25. Most Important Interview Distinctions

### Factory vs Builder

```
Factory
→ Decides WHICH object to create

Builder
→ Controls HOW a complex object is constructed
```

Example:

```
Factory
→ CardPayment or UPIPayment?

Builder
→ User with id + name + city + age + ...
```

### Strategy vs Factory

```
Factory
→ creates/selects an object

Strategy
→ represents interchangeable behavior/algorithm
```

They can also be used together.

### Adapter vs Inheritance

```
Adapter
→ makes incompatible interfaces compatible

Inheritance
→ creates an IS-A relationship
```

Adapter is often implemented using **composition**, although object adapters can also be discussed alongside inheritance-based approaches.

### Singleton vs Static

```
Singleton
→ one actual object instance

static
→ class-level members; does not itself represent an object instance
```

---

# 26. Complete Design Principles Cheat Sheet

```
SOLID
│
├── S → Single Responsibility
├── O → Open/Closed
├── L → Liskov Substitution
├── I → Interface Segregation
└── D → Dependency Inversion

Other Principles
│
├── DRY
│    → Don't Repeat Yourself
│
├── KISS
│    → Keep solution simple
│
├── YAGNI
│    → Don't build unnecessary features
│
├── Composition over Inheritance
│    → Prefer HAS-A when appropriate
│
├── Immutability
│    → State doesn't change after construction
│
├── Dependency Injection
│    → Dependencies supplied externally
│
├── Loose Coupling
│    → Minimize concrete dependencies
│
└── High Cohesion
     → Keep related responsibilities together
```

And the patterns:

```
Creational
├── Singleton
├── Factory
└── Builder

Behavioral
├── Strategy
└── Observer

Structural
└── Adapter
```

For your placement interviews, the **highest-priority topics** from this section are: **all five SOLID principles, Dependency Injection, loose coupling vs high cohesion, composition vs inheritance, Factory vs Builder, Strategy, Singleton, and Adapter**. You should be able to explain each with a small real-world example rather than only giving the textbook definition.
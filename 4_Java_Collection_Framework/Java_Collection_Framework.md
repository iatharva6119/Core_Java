# Java Collection Framework

## 1. Collection Hierarchy

The Java Collection Framework provides interfaces and classes for storing and manipulating groups of objects.

Basic hierarchy:

```
Iterable
   ↓
Collection
   ├── List
   ├── Set
   └── Queue

Map   ← Separate from Collection
```

### `Iterable`

Root interface for objects that can be **iterated using a for-each loop**.

```java
for (String name : names) {
    System.out.println(name);
}
```

### `Collection`

Parent interface of `List`, `Set`, and `Queue`.

Provides common operations such as:

```
add()
remove()
contains()
size()
clear()
```

### `List`

- Ordered
- Allows duplicates
- Index-based access

Examples:

```
ArrayList
LinkedList
Vector
```

### `Set`

- Does not allow duplicate elements
- Generally used when uniqueness is required

Examples:

```
HashSet
LinkedHashSet
TreeSet
```

### `Queue`

Used for processing elements, commonly in **FIFO** order.

Examples:

```
PriorityQueue
LinkedList
ArrayDeque
```

### `Map`

`Map` is **not a child of** **`Collection`**.

It stores data as **key-value pairs**.

```java
Map<Integer, String> map = new HashMap<>();

map.put(1, "Atharva");
map.put(2, "Rahul");
```

```
1 → Atharva
2 → Rahul
```

Important implementations:

```
HashMap
LinkedHashMap
TreeMap
Hashtable
```

### Interview Point

**Q: Why is Map not part of Collection?**

Because `Collection` represents a group of individual elements, while `Map` represents **key-value mappings**.

---

## 2. List

`List` is an ordered collection that **allows duplicate elements** and provides index-based access.

```java
List<String> names = new ArrayList<>();

names.add("Atharva");
names.add("Rahul");
names.add("Atharva");

System.out.println(names);
// [Atharva, Rahul, Atharva]
```

### ArrayList

`ArrayList` is a **resizable array** implementation of `List`.

```java
ArrayList<Integer> list = new ArrayList<>();

list.add(10);
list.add(20);
list.add(30);

System.out.println(list.get(1));
// 20
```

Important:

- Fast random access: `O(1)`
- Allows duplicates
- Maintains insertion order
- Not synchronized

### LinkedList

`LinkedList` uses a **doubly linked list** internally.

```java
LinkedList<Integer> list = new LinkedList<>();

list.add(10);
list.add(20);
list.addFirst(5);
list.addLast(30);
```

Important:

- Insertion/deletion is efficient when position/node is already known.
- Random access is slow: `O(n)`
- Can also implement `Deque` and `Queue`.

### Vector

`Vector` is a **legacy resizable array**.

```java
Vector<Integer> v = new Vector<>();

v.add(10);
v.add(20);
```

Important:

- Synchronized → thread-safe
- Generally slower than `ArrayList`
- Rarely preferred in modern Java

### Stack

`Stack` is a **legacy LIFO (Last In, First Out)** collection.

```java
Stack<Integer> stack = new Stack<>();

stack.push(10);
stack.push(20);

System.out.println(stack.pop());
// 20
```

Modern Java generally prefers `ArrayDeque` for stack operations.

---

### ArrayList Internal Working

`ArrayList` internally uses a **dynamic array**.

When you add elements:

```
ArrayList
[10][20][30][  ][  ]
```

When the internal array becomes full, ArrayList creates a **larger array**, copies the existing elements, and continues adding.

Important complexities:

- `get()` → `O(1)`
- `add()` at end → **O(1) amortized**
- Insert/delete in middle → `O(n)`
- Search → `O(n)`

**Interview:** ArrayList is fast for reading/accessing elements but slower for frequent insertion/deletion in the middle.

---

### LinkedList Internal Working

LinkedList consists of **nodes**.

Each node stores:

```
[Previous | Data | Next]
```

Example:

```
null ← [10] ⇄ [20] ⇄ [30] → null
```

Each node points to the previous and next node.

Important complexities:

- Access by index → `O(n)`
- Add/remove at beginning → `O(1)`
- Add/remove at end → `O(1)`
- Search → `O(n)`

### ArrayList vs LinkedList

| ArrayList | LinkedList |
| --- | --- |
| Dynamic array | Doubly linked list |
| `get()` → `O(1)` | `get()` → `O(n)` |
| Middle insertion/deletion → `O(n)` | Node insertion/deletion → `O(1)` once node is located |
| Less memory overhead | More memory due to node references |
| Usually preferred for general List usage | Useful when frequent structural changes at known positions are needed |

**Tricky:** Is LinkedList always faster for insertion/deletion?

**No.** Finding the required position can itself take `O(n)`. So LinkedList is not automatically faster than ArrayList.

---

## 3. Set

`Set` is a collection that **does not allow duplicate elements**.

```java
Set<Integer> set = new HashSet<>();

set.add(10);
set.add(20);
set.add(10);

System.out.println(set);
// [10, 20]
```

### HashSet

Uses a **hash table** internally.

- No duplicates
- No guaranteed order
- Allows one `null`
- Average `add()`, `remove()`, `contains()` → `O(1)`

```java
HashSet<Integer> set = new HashSet<>();

set.add(30);
set.add(10);
set.add(20);
```

### LinkedHashSet

`LinkedHashSet` maintains **insertion order**.

```java
LinkedHashSet<Integer> set = new LinkedHashSet<>();

set.add(30);
set.add(10);
set.add(20);

System.out.println(set);
// [30, 10, 20]
```

- No duplicates
- Maintains insertion order
- Average basic operations → `O(1)`
- Allows one `null`

### TreeSet

`TreeSet` stores elements in **sorted order**.

```java
TreeSet<Integer> set = new TreeSet<>();

set.add(30);
set.add(10);
set.add(20);

System.out.println(set);
// [10, 20, 30]
```

- No duplicates
- Sorted/natural order
- Basic operations → `O(log n)`
- Does not allow `null` with natural ordering

### HashSet vs LinkedHashSet vs TreeSet

| HashSet | LinkedHashSet | TreeSet |
| --- | --- | --- |
| No duplicates | No duplicates | No duplicates |
| No guaranteed order | Insertion order | Sorted |
| `O(1)` avg. | `O(1)` avg. | `O(log n)` |
| One `null` | One `null` | Generally no `null` |

**Tricky:** Does `HashSet` maintain insertion order?

**No.** If insertion order is required, use `LinkedHashSet`.

---

## 4. Queue

`Queue` is used to store elements for **processing in a particular order**. Typically follows **FIFO (First In, First Out)**.

```java
Queue<Integer> q = new LinkedList<>();

q.add(10);
q.add(20);

System.out.println(q.poll());
// 10
```

Common methods:

```
add() / offer()     → Add
remove() / poll()   → Remove
element() / peek()  → View front
```

---

### PriorityQueue

`PriorityQueue` processes elements based on **priority**, not insertion order.

By default, the **smallest element has the highest priority**.

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.add(30);
pq.add(10);
pq.add(20);

System.out.println(pq.poll());
// 10
```

- Does not allow `null`
- Duplicates allowed
- `peek()` → highest-priority element
- `poll()` → removes highest-priority element

---

### Deque

`Deque` means **Double Ended Queue**.

Elements can be added or removed from **both ends**.

```java
Deque<Integer> dq = new ArrayDeque<>();

dq.addFirst(10);
dq.addLast(20);

System.out.println(dq.removeFirst());
// 10

System.out.println(dq.removeLast());
// 20
```

Can work as both:

- Queue → FIFO
- Stack → LIFO

---

### ArrayDeque

`ArrayDeque` is a **resizable-array implementation of Deque**.

```java
ArrayDeque<Integer> dq = new ArrayDeque<>();

dq.addFirst(10);
dq.addLast(20);
```

Important:

- Fast operations at both ends
- Does not allow `null`
- Not thread-safe
- Generally preferred over legacy `Stack` for stack operations

### Quick Comparison

| Queue | PriorityQueue | Deque | ArrayDeque |
| --- | --- | --- | --- |
| FIFO | Priority-based | Both ends | Deque implementation |
| FIFO | Priority | Depends on operation | Depends on operation |
| Depends on implementation | No `null` | Depends on implementation | No `null` |
| `LinkedList` | `PriorityQueue` | Interface | `ArrayDeque` |

**Interview point:** `PriorityQueue` does **not** mean the entire queue is sorted. Only the element returned by `peek()`/`poll()` is guaranteed to be the highest-priority element.

---

## 5. Map

`Map` stores data as **key-value pairs**.

- Keys are unique.
- Values can be duplicated.

```java
Map<Integer, String> map = new HashMap<>();

map.put(1, "Java");
map.put(2, "Spring");

System.out.println(map.get(1));
// Java
```

### HashMap

Most commonly used Map implementation.

- No guaranteed order
- Allows **one null key**
- Allows multiple null values
- Average `put()`, `get()`, `remove()` → `O(1)`
- Not thread-safe

```java
HashMap<Integer, String> map = new HashMap<>();

map.put(1, "Java");
map.put(2, "Spring");
map.put(null, "SQL");
```

---

### LinkedHashMap

Maintains **insertion order** by default.

```java
LinkedHashMap<Integer, String> map = new LinkedHashMap<>();

map.put(3, "C");
map.put(1, "Java");
map.put(2, "Python");

System.out.println(map);
// {3=C, 1=Java, 2=Python}
```

- Allows one null key
- Average basic operations → `O(1)`
- Useful when insertion order must be maintained.

---

### TreeMap

Stores keys in **sorted order**.

```java
TreeMap<Integer, String> map = new TreeMap<>();

map.put(30, "C");
map.put(10, "Java");
map.put(20, "Python");

System.out.println(map);
// {10=Java, 20=Python, 30=C}
```

- Sorted by keys
- Basic operations → `O(log n)`
- Does not allow `null` keys with natural ordering.

---

### Hashtable

Legacy, **synchronized Map**.

```java
Hashtable<Integer, String> table = new Hashtable<>();

table.put(1, "Java");
```

- Thread-safe
- Does not allow `null` key or value
- Generally replaced by `ConcurrentHashMap` for modern concurrent applications.

---

### ConcurrentHashMap

A **thread-safe Map designed for concurrent access**.

```java
ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();

map.put(1, "Java");
map.put(2, "Spring");
```

- Thread-safe
- High concurrency
- Does not allow `null` keys or values
- Generally preferred over `Hashtable` for concurrent applications.

---

### WeakHashMap

Uses **weak references for its keys**.

If a key is no longer strongly referenced elsewhere, it can be **garbage collected**, and its mapping may then disappear.

```java
WeakHashMap<Object, String> map = new WeakHashMap<>();

Object key = new Object();

map.put(key, "Java");

key = null;
// key can now be garbage collected
```

Useful for certain **cache-like structures** where entries should not prevent keys from being garbage collected.

---

### EnumMap

A Map specifically designed for **enum keys**.

```java
enum Day {
    MONDAY,
    TUESDAY,
    WEDNESDAY
}

EnumMap<Day, String> map = new EnumMap<>(Day.class);

map.put(Day.MONDAY, "Working");
map.put(Day.TUESDAY, "Working");
```

- Keys must be enum constants.
- Fast and memory-efficient for enum keys.
- Maintains the **natural order of enum constants**.
- Does not allow `null` keys.

### Quick Comparison

| Map | Order | Thread-safe | Null Key |
| --- | --- | --- | --- |
| `HashMap` | No guarantee | No | 1 |
| `LinkedHashMap` | Insertion | No | 1 |
| `TreeMap` | Sorted | No | No* |
| `Hashtable` | No guarantee | Yes | No |
| `ConcurrentHashMap` | No guarantee | Yes | No |
| `WeakHashMap` | No guarantee | No | Yes |
| `EnumMap` | Enum order | No | No |
- With natural ordering, `TreeMap` does not allow a null key.

**Tricky:** Is `ConcurrentHashMap` simply a synchronized `HashMap`?

**No.** It is specifically designed for concurrent access and generally provides much better concurrency than synchronizing an entire map.

---

## 6. How `HashMap` Works Internally

`HashMap` stores data as **key-value pairs** using hashing.

```java
HashMap<Integer, String> map = new HashMap<>();

map.put(101, "Java");
map.put(102, "Spring");
```

Internally:

```
Key
 ↓
hashCode()
 ↓
Hash Function
 ↓
Bucket Index
 ↓
Entry stored in Bucket
```

---

### Hashing

Hashing is the process of converting a key into a **hash value** using `hashCode()`.

```java
key.hashCode()
```

HashMap uses this hash to determine where the entry should be stored.

---

### Hash Function

HashMap transforms the hash value into a **bucket index**.

Conceptually:

```
index = hash & (capacity - 1)
```

This works efficiently when the capacity is a power of 2.

---

### Hash Bucket

A bucket is a position in HashMap's internal table where entries are stored.

```
Bucket 0 → Entry
Bucket 1 → null
Bucket 2 → Entry → Entry
Bucket 3 → null
```

Multiple entries can exist in the same bucket because of collisions.

---

### Hash Collision

A collision occurs when **different keys map to the same bucket**.

```
Key A ──→ Bucket 3
Key B ──→ Bucket 3
```

HashMap handles collisions using a **linked structure**, and in modern Java, a heavily populated bucket can be converted into a **Red-Black Tree**.

---

### `equals()` + `hashCode()`

HashMap uses both methods to identify the correct key.

When inserting/searching:

1. `hashCode()` determines the bucket.
2. `equals()` checks whether the keys are actually equal.

Important contract:

> If two objects are equal according to `equals()`, they must have the same `hashCode()`.
> 

```java
class Student {
    int id;

    @Override
    public int hashCode() {
        return id;
    }

    @Override
    public boolean equals(Object obj) {
        Student s = (Student) obj;
        return this.id == s.id;
    }
}
```

**Tricky:** Same hash code does NOT mean objects are equal. Different objects can have the same hash code.

---

### HashMap Resizing

When the number of entries crosses the **threshold**, HashMap resizes its internal table.

Typically:

```
Old capacity = 16
New capacity = 32
```

Entries are redistributed into the new table.

Resizing is relatively expensive, which is why choosing an appropriate initial capacity can help when the expected size is known.

---

### Load Factor

Load factor determines **when HashMap should resize**.

Default load factor:

```
0.75
```

For capacity `16`:

```
Threshold = 16 × 0.75 = 12
```

When the map exceeds the threshold, it resizes.

---

### Initial Capacity

Initial capacity is the initial size of the internal hash table.

```java
HashMap<Integer, String> map = new HashMap<>(32);
```

Here, `32` is the requested initial capacity.

**Important:** The table is typically allocated lazily, when entries are first added.

---

### Treeification

If a bucket becomes heavily populated due to collisions, HashMap can convert the bucket's linked structure into a **Red-Black Tree**.

In modern Java, treeification occurs when the bucket reaches **8 nodes**, provided the table is sufficiently large (at least **64**). Otherwise, HashMap prefers resizing.

This improves lookup from approximately:

```
Linked structure → O(n)
Red-Black Tree   → O(log n)
```

---

### HashMap Complexity

Average case:

```
put()    → O(1)
get()    → O(1)
remove() → O(1)
```

With heavy collisions:

```
Before treeification → O(n)
After treeification  → O(log n)
```

### Complete Internal Flow

```
put(key, value)
      ↓
key.hashCode()
      ↓
Hash calculation
      ↓
Bucket index
      ↓
Check existing entries
      ↓
equals()
      ↓
Store / update entry
      ↓
Check threshold
      ↓
Resize if required
```

**Most important interview line:**

> HashMap uses `hashCode()` to locate the bucket and `equals()` to identify the exact key within that bucket.
> 

---

## 7. Iteration

Iteration means **traversing elements of a collection one by one**.

### Iterator

`Iterator` is used to traverse a collection in the **forward direction**.

```java
List<Integer> list = new ArrayList<>(List.of(10, 20, 30));

Iterator<Integer> it = list.iterator();

while (it.hasNext()) {
    System.out.println(it.next());
}
```

Important methods:

- `hasNext()` → checks if next element exists
- `next()` → returns next element
- `remove()` → removes current element

---

### ListIterator

`ListIterator` is specifically for **List** and can move in **both directions**.

```java
List<Integer> list = new ArrayList<>(List.of(10, 20, 30));

ListIterator<Integer> it = list.listIterator();

while (it.hasNext()) {
    System.out.println(it.next());
}

while (it.hasPrevious()) {
    System.out.println(it.previous());
}
```

It also supports `add()`, `set()`, and `remove()`.

**Difference:**

```
Iterator     → Forward only → Collection
ListIterator → Forward + backward → List only
```

---

### Enhanced `for` Loop

Also called **for-each loop**. Used for simple traversal.

```java
for (int n : list) {
    System.out.println(n);
}
```

Simple and readable, but does not directly provide the iterator's control methods.

---

### `forEach()`

Java 8 introduced `forEach()` with lambda expressions.

```java
list.forEach(n -> System.out.println(n));
```

Can also use method reference:

```java
list.forEach(System.out::println);
```

### Interview Point

**Q: Which should you use to safely remove elements while iterating?**

Use `Iterator.remove()`.

```java
Iterator<Integer> it = list.iterator();

while (it.hasNext()) {
    if (it.next() == 20) {
        it.remove();
    }
}
```

Directly modifying many collections while using a normal for-each loop can cause `ConcurrentModificationException`.

---

## 8. Sorting

### Comparable

`Comparable` is used to define the **natural/default ordering** of a class.

It uses:

```
compareTo()
```

Example:

```java
class Student implements Comparable<Student> {
    int age;

    Student(int age) {
        this.age = age;
    }

    public int compareTo(Student s) {
        return this.age - s.age;
    }
}
```

```java
Collections.sort(students);
```

---

### Comparator

`Comparator` is used when you want to define **custom/external sorting logic**.

It uses:

```
compare()
```

Example:

```java
Comparator<Student> byAge =
    (s1, s2) -> s1.age - s2.age;

students.sort(byAge);
```

You can create multiple Comparators for the same class.

---

### `Collections.sort()`

Used to sort a `List`.

```java
List<Integer> list = new ArrayList<>(List.of(30, 10, 20));

Collections.sort(list);

System.out.println(list);
// [10, 20, 30]
```

Can use a Comparator:

```java
Collections.sort(list, Comparator.reverseOrder());
```

---

### `Arrays.sort()`

Used to sort arrays.

```java
int[] arr = {30, 10, 20};

Arrays.sort(arr);

System.out.println(Arrays.toString(arr));
// [10, 20, 30]
```

---

### Custom Sorting

Usually done using `Comparator`.

```java
class Student {
    String name;
    int age;
}
```

Sort by age:

```java
students.sort((s1, s2) -> s1.age - s2.age);
```

Sort by name:

```java
students.sort((s1, s2) -> s1.name.compareTo(s2.name));
```

Descending age:

```java
students.sort((s1, s2) -> s2.age - s1.age);
```

### Important Comparison

| Comparable | Comparator |
| --- | --- |
| `compareTo()` | `compare()` |
| Defines natural ordering | Defines custom ordering |
| Implemented by the class | Separate object/interface |
| Usually one natural ordering | Can have multiple sorting strategies |

**Interview point:** Use `Comparable` when the class has a clear natural ordering; use `Comparator` when you need different/custom sorting criteria.

---

## 9. Collections Utility

### `Collections` Class

`Collections` is a utility class in `java.util` that provides **static methods for working with collections**, especially Lists.

```java
List<Integer> list = new ArrayList<>(List.of(10, 20, 30));

Collections.reverse(list);
```

### `Arrays` Class

`Arrays` is a utility class in `java.util` that provides **static methods for working with arrays**.

```java
int[] arr = {30, 10, 20};

Arrays.sort(arr);
```

---

### `Collections.reverse()`

Reverses the order of elements in a List.

```java
List<Integer> list = new ArrayList<>(List.of(1, 2, 3));

Collections.reverse(list);

System.out.println(list);
// [3, 2, 1]
```

---

### `Collections.frequency()`

Returns the number of times an element occurs.

```java
List<Integer> list = List.of(10, 20, 10, 30, 10);

System.out.println(Collections.frequency(list, 10));
// 3
```

---

### `Collections.max()`

Returns the **maximum element**.

```java
List<Integer> list = List.of(10, 30, 20);

System.out.println(Collections.max(list));
// 30
```

---

### `Collections.min()`

Returns the **minimum element**.

```java
List<Integer> list = List.of(10, 30, 20);

System.out.println(Collections.min(list));
// 10
```

---

### `Collections.unmodifiableList()`

Creates a **read-only view** of a List.

```java
List<Integer> list =
    new ArrayList<>(List.of(10, 20));

List<Integer> readOnly =
    Collections.unmodifiableList(list);

readOnly.add(30);
// UnsupportedOperationException
```

**Important:** It does not create a completely independent copy. Changes to the original list can still be visible through the unmodifiable view.

### Quick Difference

```
Collections → Collections/List utilities
Arrays      → Array utilities
```

**Interview point:** `Collections.unmodifiableList()` prevents modification **through that returned view**, but the original List can still be modified.

---

## 10. Generics

Generics allow us to write **type-safe and reusable code** by specifying the type of data a class or method can work with.

```java
List<String> names = new ArrayList<>();

names.add("Atharva");

// names.add(10); // Compile-time error
```

### Generic Class

A class that works with a type parameter.

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

Box<Integer> b = new Box<>();

b.set(10);
```

`T` is a **type parameter**.

### Generic Method

A method that has its own type parameter.

```java
static <T> void print(T value) {
    System.out.println(value);
}

print(10);
print("Java");
```

### Type Parameters

Common naming conventions:

```
T → Type
E → Element
K → Key
V → Value
N → Number
```

They are just conventions; you can use other names.

---

### Wildcards

`?` represents an **unknown type**.

```java
List<?> list;
```

It means the list can contain objects of some unknown type.

### `<?>`

Unbounded wildcard.

```java
void print(List<?> list) {
    for (Object x : list) {
        System.out.println(x);
    }
}
```

You can read elements as `Object`, but generally cannot add a specific value.

---

### `<? extends T>`

Means **T or any subclass of T**.

```java
List<? extends Number> list;
```

Can accept:

```
List<Integer>
List<Double>
List<Float>
```

Mainly useful when **reading** data.

```java
Number n = list.get(0);
```

You generally cannot add elements because the exact subtype is unknown.

---

### `<? super T>`

Means **T or any superclass of T**.

```java
List<? super Integer> list;
```

Can accept:

```
List<Integer>
List<Number>
List<Object>
```

Useful when **adding/writing** `T`.

```java
list.add(10);
```

### Easy Rule

```
? extends T → Read
? super T   → Write
```

This is commonly remembered as **PECS**:

> Producer Extends, Consumer Super.
> 

---

### Bounded Types

Restrict which types can be used.

**Upper bound:**

```java
<T extends Number>
```

`T` must be `Number` or a subclass.

**Multiple bounds:**

```java
<T extends Number & Comparable<T>>
```

A type can have **one class bound + multiple interface bounds**.

---

### Type Safety

Generics catch incorrect types at **compile time**.

Without generics:

```java
List list = new ArrayList();

list.add("Java");

String s = (String) list.get(0);
```

With generics:

```java
List<String> list = new ArrayList<>();

list.add("Java");

// list.add(10); // Compile-time error
```

This reduces the need for explicit casting and prevents many runtime `ClassCastException`s.

---

### Type Erasure

Java generics are mainly a **compile-time feature**.

The compiler removes most generic type information during compilation through **type erasure**.

```java
List<String> list = new ArrayList<>();
```

At runtime, it is essentially treated as a `List` without the generic type parameter.

**Important consequences:**

```java
// new T();              // Not allowed
// List<String>.class;   // Not allowed
```

You also cannot normally check:

```java
// if (obj instanceof List<String>) // Not allowed
```

### Important Interview Points

```
<T>              → Type parameter
<?>              → Unknown type
<? extends T>    → T or subclass → Producer/Read
<? super T>      → T or superclass → Consumer/Write
Generics         → Compile-time type safety
Type erasure     → Generic type information mostly removed at runtime
```

**Tricky:** Can `List<Integer>` be assigned to `List<Number>`?

**No.**

```java
// List<Number> list = new ArrayList<Integer>(); // Error
```

Generics are **invariant** in Java. `List<Integer>` is not a subtype of `List<Number>`.

---

## 11. Must-Know Comparisons

### ArrayList vs LinkedList

| ArrayList | LinkedList |
| --- | --- |
| Dynamic array | Doubly linked list |
| `get()` → `O(1)` | `get()` → `O(n)` |
| Middle insert/delete → `O(n)` | Node insert/delete → `O(1)` after locating node |
| Less memory | More memory |
| Better for frequent access | Better for frequent structural changes |

**Interview:** ArrayList is the usual default choice for `List`.

---

### ArrayList vs Vector

| ArrayList | Vector |
| --- | --- |
| Not synchronized | Synchronized |
| Faster generally | Slower generally |
| Modern | Legacy |
| Not thread-safe | Thread-safe |

---

### HashSet vs LinkedHashSet vs TreeSet

| HashSet | LinkedHashSet | TreeSet |
| --- | --- | --- |
| No order guarantee | Insertion order | Sorted order |
| `O(1)` average | `O(1)` average | `O(log n)` |
| 1 null allowed | 1 null allowed | Null generally not allowed |

---

### HashMap vs Hashtable

| HashMap | Hashtable |
| --- | --- |
| Not synchronized | Synchronized |
| 1 null key + null values | No null key/value |
| Faster generally | Slower generally |
| Modern | Legacy |

---

### HashMap vs ConcurrentHashMap

| HashMap | ConcurrentHashMap |
| --- | --- |
| Not thread-safe | Thread-safe |
| Better for single-threaded use | Designed for concurrent access |
| Allows null key/value | Does not allow null key/value |

**Interview:** Use `ConcurrentHashMap` when multiple threads access/update the map concurrently.

---

### HashMap vs LinkedHashMap

| HashMap | LinkedHashMap |
| --- | --- |
| No guaranteed order | Maintains insertion order by default |
| Less overhead | Slightly more overhead |
| Generally preferred when order doesn't matter | Use when order matters |

---

### HashMap vs TreeMap

| HashMap | TreeMap |
| --- | --- |
| No guaranteed order | Sorted by keys |
| `O(1)` average | `O(log n)` |
| Allows null key | Null key generally not allowed |
| Hash table | Red-Black Tree |

---

### Comparable vs Comparator

| Comparable | Comparator |
| --- | --- |
| `compareTo()` | `compare()` |
| Natural ordering | Custom ordering |
| Implemented by class | Separate comparison logic |
| Usually one natural order | Multiple sorting strategies possible |

```java
class Student implements Comparable<Student> {

    public int compareTo(Student s) {
        return this.age - s.age;
    }
}
```

```java
Comparator<Student> c =
    (s1, s2) -> s1.name.compareTo(s2.name);
```

---

### Iterator vs ListIterator

| Iterator | ListIterator |
| --- | --- |
| Forward only | Forward + backward |
| Works with Collection | Only List |
| `remove()` | `add()`, `set()`, `remove()` |

---

### Queue vs Deque

| Queue | Deque |
| --- | --- |
| Generally FIFO | Both ends |
| Insert/remove mainly at ends according to queue semantics | Insert/remove from both front and rear |
| `offer()`, `poll()` | `addFirst()`, `addLast()`, etc. |

```
Queue → FIFO
Deque → Double-ended
```

---

### ArrayDeque vs Stack

| ArrayDeque | Stack |
| --- | --- |
| Modern | Legacy |
| Not synchronized | Synchronized |
| Faster generally | Slower generally |
| Implements Deque | Extends Vector |
| Preferred for stack operations | Usually avoid in new code |

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(10);
stack.push(20);

System.out.println(stack.pop());
// 20
```

**Most important interview recommendation:**

```
General List        → ArrayList
Need insertion order → LinkedHashSet / LinkedHashMap
Need sorted data    → TreeSet / TreeMap
General Map         → HashMap
Concurrent Map      → ConcurrentHashMap
Stack               → ArrayDeque
Queue               → ArrayDeque / appropriate Queue implementation
```
# String

## 1. String

`String` is a class in `java.lang` used to represent a sequence of characters.

```java
String name = "Atharva";
```

Important: `String` is an **object**, not a primitive data type.

---

## 2. String Immutability

Strings are **immutable**, meaning once a String object is created, its content cannot be changed.

```java
String s = "Java";

s.concat(" Programming");

System.out.println(s); // Java
```

`concat()` creates a new String; it does not modify `s`.

---

## 3. Why String is Immutable

Main reasons:

- **Security** — Strings are used for passwords, file paths, URLs, etc.
- **String Pool** — Immutable strings can safely be shared.
- **Thread safety** — Immutable objects are inherently thread-safe.
- **Hashing** — `String` can safely be used as a `HashMap` key because its content cannot change.

---

## 4. String Pool

String Pool is a special memory area where JVM stores **shared String literals**.

```java
String a = "Java";
String b = "Java";
```

Both refer to the same pooled String object.

```
a ──┐
    ├──> "Java"
b ──┘
```

This saves memory.

---

## 5. String Literals

A String created using double quotes is called a **String literal**.

```java
String s = "Hello";
```

The literal is stored in the **String Pool**.

---

## 6. `new String()`

Using `new` explicitly creates a **new String object in the heap**.

```java
String s1 = "Java";
String s2 = new String("Java");
```

Here:

- `"Java"` → String Pool
- `new String("Java")` → new heap object

---

## 7. String Object Creation

Two common ways:

```java
String s1 = "Java";             // String pool
String s2 = new String("Java"); // New heap object
```

**Tricky:**

```java
String a = "Java";
String b = "Java";
String c = new String("Java");

System.out.println(a == b);       // true
System.out.println(a == c);       // false
System.out.println(a.equals(c));  // true
```

---

## 8. `==` vs `.equals()`

`==` compares **references** for objects.

`.equals()` compares **content** for String.

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);      // false
System.out.println(a.equals(b)); // true
```

**Interview rule:** Use `.equals()` when comparing String contents.

---

## 9. `equalsIgnoreCase()`

Compares String content while **ignoring case**.

```java
"Java".equalsIgnoreCase("JAVA"); // true
```

---

## 10. `compareTo()`

Compares two Strings **lexicographically**.

Returns:

- `0` → equal
- Negative → first String comes before second
- Positive → first String comes after second

```java
"Apple".compareTo("Banana"); // negative
"Banana".compareTo("Apple"); // positive
"Java".compareTo("Java");    // 0
```

---

## 11. `compareToIgnoreCase()`

Same as `compareTo()`, but **ignores case**.

```java
"java".compareToIgnoreCase("JAVA"); // 0
```

---

## 12. `concat()`

Combines two Strings.

```java
String s = "Hello";

String result = s.concat(" World");

System.out.println(result); // Hello World
```

Returns a **new String** because String is immutable.

---

## 13. `substring()`

Extracts part of a String.

```java
String s = "JavaProgramming";

System.out.println(s.substring(4));   // Programming
System.out.println(s.substring(0, 4)); // Java
```

Start index is **inclusive**, end index is **exclusive**.

---

## 14. `charAt()`

Returns the character at a given index.

```java
String s = "Java";

System.out.println(s.charAt(0)); // J
```

---

## 15. `indexOf()`

Returns the **first occurrence** of a character/String.

```java
String s = "Java Programming";

System.out.println(s.indexOf('a'));    // 1
System.out.println(s.indexOf("gram")); // 8
```

Returns `-1` if not found.

---

## 16. `lastIndexOf()`

Returns the **last occurrence**.

```java
String s = "Java";

System.out.println(s.lastIndexOf('a')); // 3
```

---

## 17. `contains()`

Checks whether a String contains a particular sequence.

```java
"Java Programming".contains("Program"); // true
```

Returns `boolean`.

---

## 18. `startsWith()`

Checks whether a String starts with a particular sequence.

```java
"Java Programming".startsWith("Java"); // true
```

---

## 19. `endsWith()`

Checks whether a String ends with a particular sequence.

```java
"Java Programming".endsWith("ing"); // true
```

---

## 20. `replace()`

Replaces characters or literal character sequences.

```java
String s = "Java Java";

System.out.println(s.replace("Java", "Python"));
// Python Python
```

---

## 21. `replaceAll()`

Replaces matches using a **regular expression (regex)**.

```java
String s = "Java123";

System.out.println(s.replaceAll("\\d", ""));
// Java
```

**Important difference:**

```
replace()     → Literal replacement
replaceAll()  → Regex-based replacement
```

---

## 22. `split()`

Splits a String based on a delimiter/regex and returns a **String array**.

```java
String s = "Java,Python,C++";

String[] arr = s.split(",");

System.out.println(arr[0]); // Java
```

---

## 23. `trim()`

Removes leading and trailing characters with code points **U+0020 or below** (traditional ASCII-style whitespace handling).

```java
String s = "  Java  ";

System.out.println(s.trim()); // Java
```

---

## 24. `strip()`

Introduced in **Java 11**.

Removes leading and trailing **Unicode whitespace**.

```java
String s = "  Java  ";

System.out.println(s.strip()); // Java
```

**Important:**

```
trim()  → older, limited whitespace handling
strip() → Unicode-aware
```

---

## 25. `isEmpty()`

Checks whether length is exactly `0`.

```java
"".isEmpty();   // true
" ".isEmpty();  // false
```

---

## 26. `isBlank()`

Introduced in **Java 11**.

Checks whether String is empty or contains only whitespace.

```java
"".isBlank();      // true
"   ".isBlank();   // true
"Java".isBlank();  // false
```

**Important:**

```
isEmpty() → length == 0
isBlank() → empty or only whitespace
```

---

## 27. `join()`

Joins multiple Strings using a delimiter.

```java
String result = String.join("-", "Java", "Spring", "Boot");

System.out.println(result);
// Java-Spring-Boot
```

---

## 28. `String.valueOf()`

Converts a value into its String representation.

```java
int n = 100;

String s = String.valueOf(n);

System.out.println(s); // "100"
```

It is commonly used to convert primitive values to String.

---

## 29. String Conversion

Common conversions:

### Primitive → String

```java
int n = 10;

String s1 = String.valueOf(n);
String s2 = Integer.toString(n);
```

### String → Primitive

```java
String s = "10";

int n = Integer.parseInt(s);
```

Other examples:

```java
double d = Double.parseDouble("10.5");

boolean b = Boolean.parseBoolean("true");
```

### Most important interview points

```
String → Immutable
"Java" → String Pool
new String("Java") → New object
== → Reference comparison
equals() → Content comparison
replace() → Literal replacement
replaceAll() → Regex replacement
trim() → Traditional whitespace removal
strip() → Unicode-aware whitespace removal
isEmpty() → Empty only
isBlank() → Empty or whitespace only
```

---

## 30. StringBuilder

`StringBuilder` is a **mutable sequence of characters** used when frequent String modifications are required.

```java
StringBuilder sb = new StringBuilder("Java");

sb.append(" Programming");

System.out.println(sb);
// Java Programming
```

---

## 31. Mutability

Unlike `String`, `StringBuilder` is **mutable**, so its existing object can be modified.

```java
StringBuilder sb = new StringBuilder("Java");

sb.append("!");

System.out.println(sb);
// Java!
```

No new StringBuilder object is required for each modification.

---

## 32. `append()`

Adds content at the **end**.

```java
StringBuilder sb = new StringBuilder("Java");

sb.append(" Programming");

System.out.println(sb);
// Java Programming
```

---

## 33. `insert()`

Inserts content at a specified index.

```java
StringBuilder sb = new StringBuilder("Jva");

sb.insert(1, "a");

System.out.println(sb);
// Java
```

---

## 34. `delete()`

Removes characters from `start` to `end - 1`.

```java
StringBuilder sb = new StringBuilder("Java");

sb.delete(1, 3);

System.out.println(sb);
// Ja
```

---

## 35. `reverse()`

Reverses the characters.

```java
StringBuilder sb = new StringBuilder("Java");

sb.reverse();

System.out.println(sb);
// avaJ
```

---

## 36. Capacity

Capacity is the amount of character storage currently allocated.

Default capacity:

```java
StringBuilder sb = new StringBuilder();

System.out.println(sb.capacity());
// 16
```

If capacity is exceeded, it automatically increases.

You can also specify initial capacity:

```java
StringBuilder sb = new StringBuilder(50);

System.out.println(sb.capacity());
// 50
```

**Important interview point:**

```
String        → Immutable
StringBuilder → Mutable
```

`StringBuilder` is generally preferred over repeated String concatenation when performing many modifications.

---

## 37. StringBuffer

`StringBuffer` is a **mutable sequence of characters**, similar to `StringBuilder`.

```java
StringBuffer sb = new StringBuffer("Java");

sb.append(" Programming");

System.out.println(sb);
// Java Programming
```

---

## 38. Thread Safety

`StringBuffer` is **thread-safe** because its methods are synchronized.

This means multiple threads can safely work with the same `StringBuffer`.

```java
StringBuffer sb = new StringBuffer("Java");

sb.append(" Programming");
```

The synchronization makes it safer for concurrent access, but can add performance overhead.

---

## 39. StringBuilder vs StringBuffer

| StringBuilder | StringBuffer |
| --- | --- |
| Mutable | Mutable |
| Not synchronized | Synchronized |
| Not thread-safe | Thread-safe |
| Generally faster | Generally slower |
| Java 5+ | Older class |

**Interview answer:**

> Use `StringBuilder` when thread safety is not required because it generally provides better performance. Use `StringBuffer` when multiple threads need synchronized access to the same mutable character sequence.
> 

**Easy to remember:**

```
String        → Immutable
StringBuilder → Mutable + Faster
StringBuffer  → Mutable + Thread-safe
```

---

## 40. Important String Comparisons

### String vs StringBuilder

| String | StringBuilder |
| --- | --- |
| Immutable | Mutable |
| Modification creates new object | Modifies existing object |
| Slower for many modifications | Faster for many modifications |
| Thread-safe due to immutability | Not thread-safe |

```java
String s = "Java";

s = s + " Programming";
// New String created

StringBuilder sb = new StringBuilder("Java");

sb.append(" Programming");
// Same object modified
```

---

### StringBuilder vs StringBuffer

| StringBuilder | StringBuffer |
| --- | --- |
| Mutable | Mutable |
| Not synchronized | Synchronized |
| Not thread-safe | Thread-safe |
| Generally faster | Generally slower |
| Introduced in Java 5 | Older class |

```
StringBuilder → Use when thread safety is not required
StringBuffer  → Use when synchronized access is required
```

---

### `==` vs `.equals()`

For objects:

- `==` → compares **references**
- `.equals()` → compares **content/logical equality** when properly implemented

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);      // false
System.out.println(a.equals(b)); // true
```

**Tricky:** With String literals:

```java
String a = "Java";
String b = "Java";

System.out.println(a == b);
// true
```

Because both refer to the same String Pool object.

---

### String Literal vs String Object

**String literal:**

```java
String s1 = "Java";
```

- Stored in String Pool.
- JVM can reuse the same object.

**String object using `new`:**

```java
String s2 = new String("Java");
```

- Creates a new String object in the heap.
- `"Java"` itself may already exist in the String Pool.

Example:

```java
String a = "Java";
String b = new String("Java");

System.out.println(a == b);      // false
System.out.println(a.equals(b)); // true
```

**Key interview summary:**

```
String literal → String Pool
new String()   → New heap object
==             → Reference comparison
equals()       → Content comparison
```

---

## 41. Reverse String

```java
String s = "Java";

String rev = "";

for (int i = s.length() - 1; i >= 0; i--) {
    rev += s.charAt(i);
}

System.out.println(rev);
// avaJ
```

For interviews, `StringBuilder.reverse()` is more efficient:

```java
String rev = new StringBuilder(s).reverse().toString();
```

---

## 42. Palindrome

A String is a palindrome if it reads the same forward and backward.

```java
String s = "madam";

String rev = new StringBuilder(s).reverse().toString();

System.out.println(s.equals(rev));
// true
```

---

## 43. Anagram

Two Strings are anagrams if they contain the **same characters with the same frequencies**.

Example: `listen` and `silent`.

```java
char[] a = "listen".toCharArray();
char[] b = "silent".toCharArray();

Arrays.sort(a);
Arrays.sort(b);

System.out.println(Arrays.equals(a, b));
// true
```

---

## 44. Character Frequency

Count how many times each character occurs.

```java
String s = "banana";

int[] freq = new int[256];

for (char c : s.toCharArray()) {
    freq[c]++;
}

System.out.println(freq['a']);
// 3
```

---

## 45. First Non-Repeating Character

Find the first character whose frequency is `1`.

```java
String s = "swiss";

for (char c : s.toCharArray()) {
    if (s.indexOf(c) == s.lastIndexOf(c)) {
        System.out.println(c);
        // w
        break;
    }
}
```

---

## 46. Duplicate Characters

Find characters appearing more than once.

```java
String s = "programming";

Set<Character> seen = new HashSet<>();

for (char c : s.toCharArray()) {
    if (!seen.add(c)) {
        System.out.println(c);
    }
}
```

---

## 47. Remove Duplicates

```java
String s = "programming";

StringBuilder result = new StringBuilder();

Set<Character> seen = new HashSet<>();

for (char c : s.toCharArray()) {
    if (seen.add(c)) {
        result.append(c);
    }
}

System.out.println(result);
// progamin
```

---

## 48. Reverse Words

```java
String s = "Java is powerful";

String[] words = s.split(" ");

StringBuilder result = new StringBuilder();

for (int i = words.length - 1; i >= 0; i--) {
    result.append(words[i]).append(" ");
}

System.out.println(result.toString().trim());
// powerful is Java
```

---

## 49. Longest Substring Without Repeating Characters

Use the **sliding window** technique.

```java
String s = "abcabcbb";

Set<Character> set = new HashSet<>();

int left = 0;
int max = 0;

for (int right = 0; right < s.length(); right++) {

    while (set.contains(s.charAt(right))) {
        set.remove(s.charAt(left++));
    }

    set.add(s.charAt(right));

    max = Math.max(max, right - left + 1);
}

System.out.println(max);
// 3
```

For `"abcabcbb"`, the longest substring is `"abc"`.

---

## 50. String Compression

Example:

```
aaabbc → a3b2c1
```

```java
String s = "aaabbc";

StringBuilder result = new StringBuilder();

int count = 1;

for (int i = 1; i <= s.length(); i++) {

    if (i < s.length() && s.charAt(i) == s.charAt(i - 1)) {
        count++;
    } else {
        result.append(s.charAt(i - 1)).append(count);
        count = 1;
    }
}

System.out.println(result);
// a3b2c1
```

### Important interview patterns

```
Reverse String              → Two pointers / StringBuilder
Palindrome                  → Two pointers / reverse
Anagram                     → Sorting / frequency
Character Frequency         → HashMap / frequency array
First Non-Repeating         → Frequency + traversal
Duplicate Characters        → HashSet / HashMap
Remove Duplicates           → HashSet
Reverse Words               → split() + traversal
Longest Substring           → Sliding Window
String Compression          → Two pointers / counting
```
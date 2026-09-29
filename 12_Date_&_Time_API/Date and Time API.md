# Date and Time API

## Date and Time API

### 1. `LocalDate`

`LocalDate` represents a **date without time and without timezone**.

Package:

```java
java.time.LocalDate
```

Example:

```java
LocalDate date = LocalDate.now();
System.out.println(date);
```

Example output:

```
2026-09-04
```

You can create a specific date:

```java
LocalDate date = LocalDate.of(2026, 9, 4);
```

Useful methods:

```
getYear()
getMonth()
getDayOfMonth()
plusDays()
plusMonths()
plusYears()
minusDays()
isBefore()
isAfter()
```

Example:

```java
LocalDate today = LocalDate.now();
LocalDate nextWeek = today.plusDays(7);
```

Important:

> `LocalDate` is immutable.
> 

---

### 2. `LocalTime`

`LocalTime` represents **time without a date and without timezone**.

```java
LocalTime time = LocalTime.now();
System.out.println(time);
```

Example:

```
14:35:20.123
```

Specific time:

```java
LocalTime time = LocalTime.of(14, 30, 15);
```

Useful methods:

```
getHour()
getMinute()
getSecond()
plusHours()
plusMinutes()
minusHours()
isBefore()
isAfter()
```

Example:

```java
LocalTime time = LocalTime.of(10, 30);
LocalTime later = time.plusHours(2);
```

Important:

> `LocalTime` does not contain timezone information.
> 

---

### 3. `LocalDateTime`

`LocalDateTime` represents **date + time**, but **without timezone information**.

```java
LocalDateTime now = LocalDateTime.now();
```

Example:

```
2026-09-04T14:30:20
```

Create manually:

```java
LocalDateTime dateTime = LocalDateTime.of(2026, 9, 4, 14, 30);
```

You can also combine:

```java
LocalDate date = LocalDate.of(2026, 9, 4);
LocalTime time = LocalTime.of(14, 30);
LocalDateTime dateTime = LocalDateTime.of(date, time);
```

Important:

> `LocalDateTime` should not be used when you need to represent a specific global instant because it has no timezone/offset.
> 

For example, `"2026-09-04 14:30"` is not enough to determine one unique moment globally.

---

### 4. `ZonedDateTime`

`ZonedDateTime` represents:

**Date + Time + Time Zone**

Example:

```java
ZonedDateTime now = ZonedDateTime.now();
System.out.println(now);
```

Example:

```
2026-09-04T14:30+05:30[Asia/Kolkata]
```

Create with a specific zone:

```java
ZonedDateTime time =
        ZonedDateTime.now(ZoneId.of("Asia/Kolkata"));
```

Another timezone:

```java
ZonedDateTime newYork =
        ZonedDateTime.now(ZoneId.of("America/New_York"));
```

Useful when dealing with:

- International applications
- Meetings across timezones
- Flight schedules
- User-specific timezone display
- DST-aware date/time calculations

Important:

> `ZonedDateTime` contains timezone information and handles timezone rules such as daylight-saving transitions.
> 

---

### 5. `Instant`

`Instant` represents a **point on the global UTC timeline**.

Think of it as a timestamp independent of a user's local timezone.

Example:

```java
Instant now = Instant.now();
System.out.println(now);
```

Example:

```
2026-09-04T09:00:00Z
```

`Z` means UTC.

Common uses:

- Database timestamps
- Logging
- Event timestamps
- Distributed systems
- API timestamps
- Measuring an absolute point in time

Example:

```java
Instant start = Instant.now();

// operation

Instant end = Instant.now();
```

You can calculate elapsed time using `Duration`.

Important distinction:

```
LocalDateTime
→ date + time, no timezone

ZonedDateTime
→ date + time + timezone

Instant
→ exact point on UTC timeline
```

---

### 6. `Duration`

`Duration` represents an amount of **time-based duration**, such as seconds, minutes, hours, or nanoseconds.

Example:

```java
Duration duration = Duration.ofHours(2);
```

Other examples:

```java
Duration.ofSeconds(30);
Duration.ofMinutes(15);
Duration.ofDays(2);
```

Calculate duration between two times:

```java
LocalTime start = LocalTime.of(10, 0);
LocalTime end = LocalTime.of(12, 30);

Duration duration = Duration.between(start, end);

System.out.println(duration);
```

Output:

```
PT2H30M
```

`Duration` is primarily for **time-based amounts**.

Think:

```
30 seconds
2 hours
15 minutes
```

---

### 7. `Period`

`Period` represents an amount of **date-based time**, mainly:

```
Years
Months
Days
```

Example:

```java
Period period = Period.ofYears(2);
```

Or:

```java
Period period = Period.ofMonths(6);
```

Between two dates:

```java
LocalDate start = LocalDate.of(2024, 9, 4);
LocalDate end = LocalDate.of(2026, 9, 4);

Period period = Period.between(start, end);

System.out.println(period);
```

Output:

```
P2Y
```

Important:

```
Duration
→ time-based
→ hours, minutes, seconds, nanos

Period
→ date-based
→ years, months, days
```

This distinction matters because months and years don't have a fixed number of seconds/days.

---

### 8. `DateTimeFormatter`

`DateTimeFormatter` is used to **format and parse date/time values**.

Package:

```java
java.time.format.DateTimeFormatter
```

Example:

```java
LocalDate date = LocalDate.now();

DateTimeFormatter formatter =
        DateTimeFormatter.ofPattern("dd-MM-yyyy");

String result = date.format(formatter);

System.out.println(result);
```

Output:

```
04-09-2026
```

Common patterns:

```
dd-MM-yyyy
yyyy-MM-dd
dd/MM/yyyy
HH:mm:ss
dd-MM-yyyy HH:mm:ss
```

Parsing:

```java
DateTimeFormatter formatter =
        DateTimeFormatter.ofPattern("dd-MM-yyyy");

LocalDate date =
        LocalDate.parse("04-09-2026", formatter);
```

So:

```
Formatting:
Date/Time → String

Parsing:
String → Date/Time
```

Important:

> `DateTimeFormatter` is immutable and thread-safe, unlike many older date-formatting approaches.
> 

---

### 9. Why Java 8 Date/Time API is Preferred Over `Date`/`Calendar`

The modern API was introduced in **Java 8** under:

```
java.time
```

The older APIs include:

```
java.util.Date
java.util.Calendar
java.text.SimpleDateFormat
```

The modern API addresses several problems in the older API.

#### 1. Immutability

Modern classes are immutable:

```
LocalDate
LocalTime
LocalDateTime
Instant
ZonedDateTime
```

Example:

```java
LocalDate date = LocalDate.of(2026, 9, 4);

LocalDate next = date.plusDays(1);
```

`date` itself is not modified.

Instead, a new `LocalDate` is returned.

---

#### 2. Better API Design

The modern API separates concepts clearly:

```
LocalDate
→ Date

LocalTime
→ Time

LocalDateTime
→ Date + Time

ZonedDateTime
→ Date + Time + Zone

Instant
→ Global timestamp
```

The old `Date` class mixed several concepts and had a less intuitive API.

---

#### 3. Better Timezone Support

Modern API:

```java
ZonedDateTime.now(
    ZoneId.of("Asia/Kolkata")
);
```

You can explicitly work with timezone information.

---

#### 4. Thread Safety

Modern date/time classes are immutable and thread-safe.

For example:

```java
DateTimeFormatter
```

can safely be shared.

Older:

```java
SimpleDateFormat
```

is mutable and **not thread-safe**.

---

#### 5. Fluent API

Modern API provides readable operations:

```java
date.plusDays(7).minusMonths(1);
```

This is much easier to understand than many older `Calendar` operations.

---

### 10. Modern API vs Old API

| Modern API | Old API |
| --- | --- |
| `LocalDate` | `Date` / `Calendar` |
| `LocalTime` | `Date` / `Calendar` |
| `LocalDateTime` | `Calendar` |
| `ZonedDateTime` | `Calendar` + timezone handling |
| `Instant` | `Date` / timestamp |
| `Duration` | Manual calculations / `Date` differences |
| `Period` | Manual calendar calculations |
| `DateTimeFormatter` | `SimpleDateFormat` |
| Immutable | Many old classes are mutable |
| Thread-safe | `SimpleDateFormat` is not thread-safe |
| Clear API | Older API is more cumbersome |

---

### 11. Important Conversion Between Modern and Old APIs

You may encounter legacy code, so know how to convert.

Modern:

```java
Instant instant = Instant.now();
```

Convert to old `Date`:

```java
Date date = Date.from(instant);
```

Convert `Date` back:

```java
Instant instant = date.toInstant();
```

This is useful when working with older libraries/APIs that still require `java.util.Date`.

---

### 12. Complete Mental Model

Remember the API like this:

```
                  java.time
                     |
      ┌──────────────┼───────────────┐
      ↓              ↓               ↓
   Date/Time       Timeline        Amount
      |               |               |
      |               |        ┌──────┴──────┐
      |               |        ↓             ↓
      |            Instant   Duration       Period
      |
 ┌────┼────────┬─────────────┐
 ↓    ↓        ↓             ↓
Date Time DateTime       ZonedDateTime
```

And formatting:

```
Date/Time
    ↓
DateTimeFormatter
    ↓
String
```

Parsing:

```
String
    ↓
DateTimeFormatter
    ↓
Date/Time
```

The most important interview distinctions are:

```
LocalDate
→ date only

LocalTime
→ time only

LocalDateTime
→ date + time, no zone

ZonedDateTime
→ date + time + zone

Instant
→ exact point on UTC timeline

Duration
→ hours/minutes/seconds-based amount

Period
→ years/months/days-based amount

DateTimeFormatter
→ format + parse
```

And the main reason to prefer Java 8+ `java.time` is:

> **It provides a clear, immutable, thread-safe, timezone-aware, and much better-designed API compared with the legacy `Date`/`Calendar` classes.**
>
# JDBC

## 1. What is JDBC?

**JDBC (Java Database Connectivity)** is a Java API used to connect Java applications with relational databases and perform database operations.

It allows Java applications to:

- Establish database connections
- Execute SQL queries
- Insert/update/delete data
- Retrieve data
- Manage transactions
- Call stored procedures
- Process query results

The main JDBC package is:

```java
import java.sql.*;
```

Typical flow:

```
Java Application
       ↓
     JDBC API
       ↓
   JDBC Driver
       ↓
    Database
```

Example:

```java
Connection con = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/studentdb",
    "root",
    "password"
);

PreparedStatement ps = con.prepareStatement(
    "SELECT * FROM students WHERE id = ?"
);

ps.setInt(1, 101);

ResultSet rs = ps.executeQuery();

while (rs.next()) {
    System.out.println(rs.getString("name"));
}
```

---

# JDBC Architecture

## 2. JDBC Architecture

JDBC architecture consists of the Java application, JDBC API, JDBC driver, and database.

```
                Java Application
                       |
                       ↓
                    JDBC API
                  java.sql / javax.sql
                       |
                       ↓
                   DriverManager
                       |
                       ↓
                  JDBC Driver
                       |
                       ↓
                     Database
```

### Main components

```
Java Application
      ↓
JDBC API
      ↓
DriverManager / DataSource
      ↓
JDBC Driver
      ↓
Database
```

### JDBC API

Provides interfaces/classes such as:

```
Connection
Statement
PreparedStatement
CallableStatement
ResultSet
DriverManager
SQLException
```

The JDBC API provides a common programming interface regardless of the database vendor.

For example, your Java code can use:

```
Connection
PreparedStatement
ResultSet
```

whether the database is MySQL, PostgreSQL, Oracle, etc., assuming an appropriate JDBC driver is available.

---

## 3. JDBC Driver

A **JDBC Driver** is software that allows Java applications to communicate with a specific database.

For example:

```
Java Application
      ↓
JDBC API
      ↓
MySQL JDBC Driver
      ↓
MySQL Database
```

The driver translates JDBC calls into the database-specific communication protocol.

### Common JDBC drivers

```
MySQL       → MySQL Connector/J
PostgreSQL  → PostgreSQL JDBC Driver
Oracle      → Oracle JDBC Driver
```

### Driver Types

Historically, JDBC drivers were classified into four types:

| Type | Name | Important? |
| --- | --- | --- |
| Type 1 | JDBC-ODBC Bridge | Obsolete |
| Type 2 | Native-API Driver | Rare |
| Type 3 | Network Protocol Driver | Rare |
| Type 4 | Thin/Pure Java Driver | Most commonly used |

Modern applications generally use **Type 4 drivers**.

### Type 4 Driver

A Type 4 driver is written entirely in Java and directly communicates with the database.

```
Java Application
      ↓
JDBC API
      ↓
Type 4 JDBC Driver
      ↓
Database
```

For example, MySQL Connector/J is a Type 4 driver.

---

## 4. Connection

`Connection` represents an active connection between a Java application and a database.

It belongs to:

```
java.sql.Connection
```

Example:

```java
Connection con = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/studentdb",
    "root",
    "password"
);
```

The JDBC URL:

```
jdbc:mysql://localhost:3306/studentdb
```

means approximately:

```
jdbc
 ↓
mysql
 ↓
localhost
 ↓
3306
 ↓
studentdb
```

### Important Connection methods

```
createStatement()
prepareStatement()
prepareCall()
commit()
rollback()
setAutoCommit()
close()
```

Example:

```java
con.setAutoCommit(false);

PreparedStatement ps = con.prepareStatement(
    "UPDATE accounts SET balance = balance - ? WHERE id = ?"
);
```

A `Connection` is also responsible for transaction management.

---

# JDBC Statements

## 5. Statement

`Statement` is used to execute static SQL statements.

Example:

```java
Statement stmt = con.createStatement();

ResultSet rs = stmt.executeQuery(
    "SELECT * FROM students"
);
```

Another example:

```java
int rows = stmt.executeUpdate(
    "UPDATE students SET age = 23 WHERE id = 101"
);
```

The problem appears when user input is directly concatenated into SQL.

Bad:

```java
String name = "Atharva";

Statement stmt = con.createStatement();

String sql =
    "SELECT * FROM students WHERE name = '" + name + "'";

ResultSet rs = stmt.executeQuery(sql);
```

This can lead to **SQL injection**.

---

## 6. PreparedStatement

`PreparedStatement` is used for **parameterized SQL queries**.

Example:

```java
PreparedStatement ps = con.prepareStatement(
    "SELECT * FROM students WHERE id = ?"
);

ps.setInt(1, 101);

ResultSet rs = ps.executeQuery();
```

The `?` is a parameter placeholder.

```
SQL:
SELECT * FROM students WHERE id = ?

             ↓

ps.setInt(1, 101)

             ↓

Database executes parameterized query
```

### Setting parameters

```java
ps.setInt(1, 101);
ps.setString(2, "Atharva");
ps.setDouble(3, 85.5);
ps.setDate(4, date);
```

Parameter indexes start from **1**, not 0.

Important:

```java
ps.setString(1, name);
```

not:

```java
ps.setString(0, name);  // ❌
```

### Why PreparedStatement?

Main advantages:

1. Prevents SQL injection when used correctly.
2. Separates SQL structure from parameter values.
3. Easier to work with dynamic input.
4. Can improve performance when the same prepared statement is executed repeatedly, depending on driver/database behavior.
5. Provides type-safe parameter binding through methods like `setInt`, `setString`, etc.

---

# CallableStatement

## 7. CallableStatement

`CallableStatement` is used to execute **stored procedures and stored functions**.

It extends:

```
Statement
   ↓
PreparedStatement
   ↓
CallableStatement
```

Example stored procedure:

```sql
CREATE PROCEDURE getStudent(IN studentId INT)
BEGIN
    SELECT * FROM students WHERE id = studentId;
END
```

Java:

```java
CallableStatement cs = con.prepareCall(
    "{call getStudent(?)}"
);

cs.setInt(1, 101);

ResultSet rs = cs.executeQuery();
```

### OUT parameter

Suppose a stored procedure returns a value through an OUT parameter:

```java
CallableStatement cs = con.prepareCall(
    "{call getStudentCount(?)}"
);

cs.registerOutParameter(1, Types.INTEGER);

cs.execute();

int count = cs.getInt(1);
```

Important interview answer:

> `CallableStatement` is used to execute stored procedures/functions, while `PreparedStatement` is primarily used for parameterized SQL statements.
> 

---

# ResultSet

## 8. ResultSet

`ResultSet` represents the data returned by a SQL query.

Example:

```java
PreparedStatement ps = con.prepareStatement(
    "SELECT id, name, age FROM students"
);

ResultSet rs = ps.executeQuery();

while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    int age = rs.getInt("age");

    System.out.println(id + " " + name + " " + age);
}
```

`ResultSet` maintains a cursor over the returned rows.

Initially:

```
       Cursor
         ↓
       before
       first row
```

After:

```java
rs.next();
```

the cursor moves to the first row.

```
       Cursor
         ↓
     ┌─────────┐
     │ Row 1   │
     ├─────────┤
     │ Row 2   │
     ├─────────┤
     │ Row 3   │
     └─────────┘
```

Another `next()` moves to Row 2.

### Getting values

By column name:

```java
rs.getString("name");
rs.getInt("age");
```

By column index:

```java
rs.getString(2);
rs.getInt(3);
```

Column indexes start from **1**.

---

# Execute Methods

## 9. `executeQuery()`

Used primarily for SQL statements that **return a ResultSet**, typically `SELECT`.

```java
ResultSet rs = ps.executeQuery();
```

Example:

```java
PreparedStatement ps = con.prepareStatement(
    "SELECT * FROM students"
);

ResultSet rs = ps.executeQuery();
```

Return type:

```
ResultSet
```

---

## 10. `executeUpdate()`

Used for SQL statements that modify data:

```
INSERT
UPDATE
DELETE
```

Example:

```java
PreparedStatement ps = con.prepareStatement(
    "UPDATE students SET age = ? WHERE id = ?"
);

ps.setInt(1, 23);
ps.setInt(2, 101);

int rows = ps.executeUpdate();

System.out.println(rows);
```

The return value is the number of affected rows for DML statements.

For example:

```
UPDATE → 1
```

means one row was affected.

---

## 11. `execute()`

`execute()` is used when you don't know in advance whether the statement will return a `ResultSet` or an update count, or when using statements that can produce multiple results.

```java
boolean result = ps.execute();
```

If:

```
true
```

the first result is a `ResultSet`.

If:

```
false
```

the first result is an update count or there is no result.

For normal application code:

```
SELECT
→ executeQuery()

INSERT/UPDATE/DELETE
→ executeUpdate()

Unknown/mixed
→ execute()
```

---

# Transactions

## 12. Transaction

A **transaction** is a logical unit of database work that should be completed as a whole.

Example: transferring money.

```
Account A
   ↓
 - ₹100
   ↓
Account B
   ↓
 + ₹100
```

Both operations should succeed together.

If the first succeeds but the second fails, the database should be restored to the previous consistent state.

This is the idea behind **ACID transactions**:

```
A → Atomicity
C → Consistency
I → Isolation
D → Durability
```

---

## 13. Auto-Commit

By default, JDBC connections normally operate with:

```
autoCommit=true
```

That means each SQL statement is committed automatically.

Example:

```java
con.setAutoCommit(true);
```

Conceptually:

```
SQL 1
 ↓
COMMIT

SQL 2
 ↓
COMMIT
```

For a multi-step transaction, disable auto-commit:

```java
con.setAutoCommit(false);
```

Then explicitly commit or rollback.

---

# Commit

## 14. `commit()`

`commit()` permanently saves the changes made during the current transaction.

Example:

```java
con.setAutoCommit(false);

PreparedStatement ps = con.prepareStatement(
    "UPDATE students SET age = ? WHERE id = ?"
);

ps.setInt(1, 23);
ps.setInt(2, 101);

ps.executeUpdate();

con.commit();
```

Flow:

```
BEGIN
 ↓
SQL operation
 ↓
SQL operation
 ↓
COMMIT
 ↓
Changes become permanent
```

---

# Rollback

## 15. `rollback()`

`rollback()` undoes changes made since the transaction began or since the relevant savepoint.

Example:

```java
try {
    con.setAutoCommit(false);

    // Operation 1
    ps1.executeUpdate();

    // Operation 2
    ps2.executeUpdate();

    con.commit();

} catch (SQLException e) {
    con.rollback();
}
```

Flow:

```
BEGIN
 ↓
Operation 1
 ↓
Operation 2
 ↓
Error
 ↓
ROLLBACK
 ↓
Undo transaction changes
```

### Complete transaction example

```java
try {
    con.setAutoCommit(false);

    PreparedStatement debit = con.prepareStatement(
        "UPDATE account " +
        "SET balance = balance - ? " +
        "WHERE id = ?"
    );

    PreparedStatement credit = con.prepareStatement(
        "UPDATE account " +
        "SET balance = balance + ? " +
        "WHERE id = ?"
    );

    debit.setDouble(1, 1000);
    debit.setInt(2, 1);

    credit.setDouble(1, 1000);
    credit.setInt(2, 2);

    debit.executeUpdate();
    credit.executeUpdate();

    con.commit();

} catch (SQLException e) {
    con.rollback();

} finally {
    con.setAutoCommit(true);
}
```

The important concept is:

```
Both succeed → COMMIT
Any failure  → ROLLBACK
```

---

# Batch Processing

## 16. Batch Processing

Batch processing allows you to send multiple SQL operations together instead of executing each operation individually.

Without batch:

```java
ps.executeUpdate();
ps.executeUpdate();
ps.executeUpdate();
```

With batch:

```java
ps.addBatch();
ps.addBatch();
ps.addBatch();

ps.executeBatch();
```

Example:

```java
PreparedStatement ps = con.prepareStatement(
    "INSERT INTO students(id, name) VALUES (?, ?)"
);

ps.setInt(1, 101);
ps.setString(2, "Atharva");
ps.addBatch();

ps.setInt(1, 102);
ps.setString(2, "Rahul");
ps.addBatch();

ps.setInt(1, 103);
ps.setString(2, "Amit");
ps.addBatch();

int[] results = ps.executeBatch();
```

### Benefits

Batch processing can:

- Reduce network round trips
- Improve throughput
- Reduce per-statement execution overhead
- Be useful for bulk inserts/updates/deletes

Often combined with transactions:

```java
con.setAutoCommit(false);

ps.addBatch();
ps.addBatch();
ps.addBatch();

ps.executeBatch();

con.commit();
```

---

# Connection Pooling

## 17. Connection Pooling

Creating a database connection can be relatively expensive because it may involve:

```
Network connection
Authentication
Database session creation
Resource allocation
```

Creating a new connection for every request is inefficient.

Connection pooling solves this problem.

Instead of:

```
Request
  ↓
Create connection
  ↓
Execute query
  ↓
Close connection
```

we use:

```
             Connection Pool
          ┌────┬────┬────┬────┐
          │ C1 │ C2 │ C3 │ C4 │
          └────┴────┴────┴────┘
             ↑        ↑
          Requests use
          available connections
```

A connection pool maintains a set of reusable database connections.

Application:

```
Request
   ↓
Borrow connection
   ↓
Execute SQL
   ↓
Return connection
   ↓
Pool
```

Calling:

```java
connection.close();
```

when using a pooling `DataSource` typically **returns the connection to the pool rather than physically closing the underlying database connection**.

### Common connection pool

A popular Java connection pool is **HikariCP**.

It is commonly used with Spring Boot applications.

### `DataSource`

Modern JDBC applications commonly obtain connections through:

```
DataSource
```

rather than directly using `DriverManager` everywhere.

Conceptually:

```
Application
    ↓
DataSource
    ↓
Connection Pool
    ↓
Database
```

---

# SQL Injection

## 18. SQL Injection

SQL injection occurs when untrusted user input is incorrectly incorporated into SQL statements in a way that changes the intended SQL structure.

Bad example:

```java
String username = request.getParameter("username");

String sql =
    "SELECT * FROM users WHERE username = '" + username + "'";

Statement stmt = con.createStatement();

ResultSet rs = stmt.executeQuery(sql);
```

The problem is that user input becomes part of the SQL syntax.

Conceptually:

```
User input
     ↓
String concatenation
     ↓
SQL statement
     ↓
Database
```

An attacker may provide specially crafted input that changes the query's meaning.

### Prevention

Use parameterized queries:

```java
PreparedStatement ps = con.prepareStatement(
    "SELECT * FROM users WHERE username = ?"
);

ps.setString(1, username);

ResultSet rs = ps.executeQuery();
```

Now:

```
SQL structure
     +
parameter value
     ↓
Database
```

The parameter is treated as data rather than being directly interpreted as part of the SQL command structure.

Important:

> `PreparedStatement` is the standard JDBC mechanism for preventing SQL injection in parameter values.
> 

However, it does not automatically make every part of a SQL statement safe. For example, table names, column names, or SQL keywords generally cannot be supplied as ordinary `?` parameters. Those cases require controlled allowlists or other safe query construction.

---

# Statement vs PreparedStatement

## 19. `PreparedStatement` vs `Statement`

This is a **very common interview question**.

| Feature | Statement | PreparedStatement |
| --- | --- | --- |
| SQL | Usually static/dynamically built | Parameterized |
| Parameters | No `?` parameter binding | Supports `?` |
| SQL injection protection | Poor when concatenating input | Strong protection for bound parameters |
| Repeated execution | Less suitable | Well-suited |
| Readability | Can become messy with concatenation | Cleaner |
| Type binding | No parameter API | `setInt`, `setString`, etc. |
| Performance | Depends on use/driver | Can benefit from prepared execution/caching |
| Recommended for user input | No | Yes |

### Statement

```java
String sql =
    "SELECT * FROM students WHERE id = " + id;

Statement stmt = con.createStatement();

ResultSet rs = stmt.executeQuery(sql);
```

### PreparedStatement

```java
PreparedStatement ps = con.prepareStatement(
    "SELECT * FROM students WHERE id = ?"
);

ps.setInt(1, id);

ResultSet rs = ps.executeQuery();
```

For application code involving external/user-provided values:

> Prefer `PreparedStatement`.
> 

---

# 20. Complete JDBC Flow

This is the flow you should memorize for interviews:

```
1. Load/obtain JDBC driver
          ↓
2. Establish Connection
          ↓
3. Create Statement /
   PreparedStatement /
   CallableStatement
          ↓
4. Execute SQL
          ↓
5. Process ResultSet
   (for queries)
          ↓
6. Commit / Rollback
   (for transactions)
          ↓
7. Close resources
```

Modern JDBC commonly uses **try-with-resources**:

```java
String sql =
    "SELECT id, name FROM students WHERE age > ?";

try (
    Connection con =
        DriverManager.getConnection(url, username, password);

    PreparedStatement ps = con.prepareStatement(sql)
) {

    ps.setInt(1, 20);

    try (ResultSet rs = ps.executeQuery()) {

        while (rs.next()) {
            System.out.println(
                rs.getInt("id") + " " +
                rs.getString("name")
            );
        }
    }

} catch (SQLException e) {
    e.printStackTrace();
}
```

This automatically closes:

```
Connection
PreparedStatement
ResultSet
```

---

# 21. JDBC Classes/Interfaces You Must Know

For interviews, remember this hierarchy/relationship:

```
                 JDBC
                  │
        ┌─────────┴──────────┐
        │                    │
    DriverManager        DataSource
        │                    │
        ↓                    ↓
    Connection         Connection Pool
        │
        ├── Statement
        │
        ├── PreparedStatement
        │
        └── CallableStatement

PreparedStatement
        ↓
    executeQuery()
    executeUpdate()
    execute()

Query
  ↓
ResultSet
```

And the most important distinction:

```
Statement
→ SQL directly

PreparedStatement
→ parameterized SQL

CallableStatement
→ stored procedures/functions
```

---

# 22. Fresher Interview Questions You Should Be Able to Answer

**Q1. What is JDBC?**

JDBC is the standard Java API for connecting Java applications to relational databases and executing SQL operations.

**Q2. What is a JDBC driver?**

A database-specific implementation that translates JDBC API calls into the protocol understood by the database.

**Q3. What is Connection?**

An interface representing a session/connection between a Java application and a database.

**Q4. Difference between Statement and PreparedStatement?**

`Statement` executes SQL directly, while `PreparedStatement` supports parameterized SQL using `?`, provides type-safe parameter binding, and protects against SQL injection for those parameters.

**Q5. What is ResultSet?**

An object representing tabular data returned by a query, with a cursor used to iterate through rows.

**Q6. Difference between `executeQuery()` and `executeUpdate()`?**

`executeQuery()` is primarily for queries returning a `ResultSet`; `executeUpdate()` is for DML such as INSERT, UPDATE, and DELETE and returns the affected-row count.

**Q7. What is a transaction?**

A group of database operations treated as one logical unit of work.

**Q8. What is commit?**

Permanently applies the current transaction's changes.

**Q9. What is rollback?**

Reverts uncommitted changes in the current transaction, subject to transaction/savepoint boundaries.

**Q10. What is batch processing?**

Executing multiple SQL operations as a batch to improve efficiency and reduce overhead.

**Q11. What is connection pooling?**

Maintaining reusable database connections so applications can borrow and return connections instead of repeatedly creating physical database connections.

**Q12. How do you prevent SQL injection in JDBC?**

Use `PreparedStatement` with parameter binding instead of concatenating untrusted input into SQL.

---

# JDBC — What You Need to Memorize

```
JDBC
│
├── Architecture
│
├── Driver
│
├── Connection
│
├── Statement
├── PreparedStatement
├── CallableStatement
│
├── ResultSet
│
├── executeQuery()
├── executeUpdate()
├── execute()
│
├── Transactions
│   ├── setAutoCommit(false)
│   ├── commit()
│   └── rollback()
│
├── Batch Processing
│   ├── addBatch()
│   └── executeBatch()
│
├── Connection Pooling
│   └── DataSource / HikariCP
│
├── SQL Injection
│   └── PreparedStatement
│
└── Statement vs PreparedStatement
```

For your placement preparation, **the highest-priority JDBC topics are** **`Connection`**, **`PreparedStatement`**, **`ResultSet`**, **`executeQuery()`** **vs** **`executeUpdate()`**, transactions, connection pooling, SQL injection, and **`Statement`** **vs** **`PreparedStatement`**.
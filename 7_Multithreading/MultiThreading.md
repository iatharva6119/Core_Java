# MultiThreading

### 1. Process

A **process** is an independent program in execution.

For example, when you open:

```
Chrome
IntelliJ IDEA
Spotify
```

each runs as a process managed by the operating system.

A process has its own:

- Memory/address space
- Resources
- Program state
- Threads

Conceptually:

```
Process
├── Memory
├── Resources
└── Threads
```

Processes are relatively expensive to create and communicate between because they have separate memory spaces.

---

### 2. Thread

A **thread** is the smallest unit of execution within a process.

A single Java application starts with at least one thread: the **main thread**.

```java
public class Main {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName());
    }
}
```

Output:

```
main
```

A process can contain multiple threads:

```
Java Process
├── Main Thread
├── Thread 1
├── Thread 2
└── Thread 3
```

Threads within the same process share resources such as heap memory, while each thread has its own execution stack.

---

### 3. Process vs Thread

| Process | Thread |
| --- | --- |
| Independent program execution | Execution unit within a process |
| Has separate memory space | Shares process memory |
| More expensive to create | Cheaper to create |
| Communication is comparatively expensive | Communication is easier through shared memory |
| More isolated | Less isolated |
| Contains one or more threads | Exists inside a process |

Example:

```
Process = Java application
Thread  = individual task executing inside it
```

**Interview answer:**

> A process is an independent execution environment, while a thread is a lightweight execution unit inside a process.
> 

---

### 4. Multitasking

**Multitasking** means the operating system manages multiple tasks so they can make progress seemingly at the same time.

There are two major forms:

```
Multitasking
├── Process-based multitasking
└── Thread-based multitasking
```

Process-based:

```
Chrome
   +
IntelliJ
   +
Spotify
```

Thread-based:

```
One application
├── Download thread
├── UI thread
└── Background processing thread
```

The OS scheduler decides when CPU time is given to different runnable tasks.

---

### 5. Multithreading

**Multithreading** means executing multiple threads within a single process.

Example:

```
Java Application
│
├── Thread 1 → Download file
├── Thread 2 → Process data
└── Thread 3 → Handle user input
```

Simple Java example:

```java
class Task extends Thread {

    @Override
    public void run() {
        System.out.println("Task running");
    }
}

public class Main {
    public static void main(String[] args) {

        Task t1 = new Task();
        Task t2 = new Task();

        t1.start();
        t2.start();
    }
}
```

Important:

```java
t1.start(); // Correct
```

not:

```java
t1.run(); // Does not create a new thread
```

`start()` asks the JVM to start a new thread, which then invokes `run()`.

---

### 6. Concurrency

**Concurrency** means multiple tasks are **in progress during overlapping periods**. They do not necessarily execute simultaneously.

On a single CPU core, the system can rapidly switch between tasks:

```
Time →
Task A → Task B → Task A → Task C → Task B
```

This is concurrency.

The key idea:

> Concurrency is about **dealing with multiple tasks at the same time**, not necessarily executing them simultaneously.
> 

Java example:

```java
Thread t1 = new Thread(() -> {
    // Task A
});

Thread t2 = new Thread(() -> {
    // Task B
});

t1.start();
t2.start();
```

The JVM/OS scheduler determines how their execution is interleaved.

---

### 7. Parallelism

**Parallelism** means multiple tasks are **actually executing simultaneously**, typically on multiple CPU cores.

```
CPU Core 1 → Task A
CPU Core 2 → Task B
CPU Core 3 → Task C
```

For example, a machine with four cores can potentially execute four independent CPU-bound tasks simultaneously.

---

### Concurrency vs Parallelism

| Concurrency | Parallelism |
| --- | --- |
| Multiple tasks make progress | Multiple tasks execute simultaneously |
| Can happen on one CPU core | Typically requires multiple cores |
| Focuses on task coordination | Focuses on simultaneous execution |
| Can involve context switching | Uses multiple execution resources |

Easy way to remember:

```
Concurrency → dealing with many things
Parallelism → doing many things simultaneously
```

A useful analogy:

```
Concurrency:
One chef switches between preparing 3 dishes.

Parallelism:
Three chefs prepare 3 dishes simultaneously.
```

### Interview Trap

**Q: Is multithreading always parallel?**

No.

Multiple threads can execute concurrently on a single CPU core through scheduling/context switching. True parallel execution requires sufficient hardware execution resources, such as multiple CPU cores.

**Q: Can concurrency exist without parallelism?**

Yes. A single-core CPU can provide concurrency through rapid context switching, but it cannot execute two CPU instructions simultaneously on that one core.

---

### 8. Extending `Thread`

You can create a thread by extending the `Thread` class and overriding `run()`.

```java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("Thread is running");
    }
}

public class Main {
    public static void main(String[] args) {

        MyThread t = new MyThread();
        t.start();
    }
}
```

Important:

```java
t.start(); // creates a new thread
```

Do not directly call:

```java
t.run(); // normal method call; no new thread
```

The main disadvantage is that Java supports **single class inheritance**, so extending `Thread` prevents the class from extending another class.

---

### 9. Implementing `Runnable`

A more flexible approach is implementing `Runnable`.

```java
class MyTask implements Runnable {

    @Override
    public void run() {
        System.out.println("Task is running");
    }
}
```

Create the thread:

```java
Runnable task = new MyTask();

Thread t = new Thread(task);

t.start();
```

With a lambda:

```java
Thread t = new Thread(
    () -> System.out.println("Task is running")
);

t.start();
```

Why is `Runnable` generally preferred over extending `Thread`?

Because it separates:

```
Task → Runnable
Execution mechanism → Thread
```

The class can also extend another class.

---

### 10. `Callable`

`Callable<V>` is similar to `Runnable`, but it can:

- Return a result
- Throw checked exceptions

`Runnable`:

```java
void run()
```

`Callable`:

```java
V call() throws Exception
```

Example:

```java
Callable<Integer> task = () -> {
    return 10 + 20;
};
```

A `Callable` is normally submitted to an `ExecutorService`, not directly started using `Thread`.

---

### 11. `Future`

`Future<V>` represents the **result of an asynchronous computation**.

When you submit a `Callable`:

```java
ExecutorService executor =
    Executors.newSingleThreadExecutor();

Callable<Integer> task = () -> 10 + 20;

Future<Integer> future =
    executor.submit(task);
```

You can retrieve the result:

```java
Integer result = future.get();

System.out.println(result); // 30
```

`future.get()` waits if the task hasn't finished yet.

Important methods:

```
get()         → get result, possibly wait
isDone()      → check whether completed
cancel()      → attempt to cancel
isCancelled() → check cancellation
```

You can also specify a timeout:

```java
Integer result =
    future.get(2, TimeUnit.SECONDS);
```

---

### 12. `ExecutorService`

`ExecutorService` provides a higher-level API for managing and executing threads.

Instead of manually creating:

```java
new Thread(...)
```

for every task, you can use a **thread pool**.

```java
ExecutorService executor =
    Executors.newFixedThreadPool(3);
```

Submit tasks:

```java
executor.submit(
    () -> System.out.println("Task 1")
);

executor.submit(
    () -> System.out.println("Task 2")
);

executor.submit(
    () -> System.out.println("Task 3")
);
```

Shutdown:

```java
executor.shutdown();
```

Conceptually:

```
              ExecutorService
                     |
                 Thread Pool
                /     |     \
           Thread 1 Thread 2 Thread 3
                \     |     /
                    Tasks
```

### Common Executor types

```java
Executors.newFixedThreadPool(3);
```

Creates a pool with a fixed number of threads.

```java
Executors.newSingleThreadExecutor();
```

Uses one worker thread.

```java
Executors.newCachedThreadPool();
```

Creates/reuses threads dynamically.

```java
Executors.newScheduledThreadPool(2);
```

Supports delayed and periodic execution.

---

## `Runnable` vs `Callable`

| Runnable | Callable |
| --- | --- |
| `run()` | `call()` |
| Returns nothing | Returns a value |
| Cannot directly throw checked exceptions | Can throw checked exceptions |
| Used for tasks without results | Used when a result is required |

```
Runnable → Task → no result
Callable → Task → result
```

---

## `Thread` vs `Runnable` vs `Callable`

```
Thread
  ↓
Represents/exposes a thread of execution

Runnable
  ↓
Represents a task with no result

Callable
  ↓
Represents a task that returns a result
```

In modern Java applications, a common pattern is:

```java
ExecutorService executor =
    Executors.newFixedThreadPool(4);

Future<Integer> future =
    executor.submit(() -> 10 + 20);

System.out.println(future.get());

executor.shutdown();
```

The flow is:

```
Callable
   ↓
ExecutorService.submit()
   ↓
Future
   ↓
future.get()
   ↓
Result
```

### Interview points to remember

- `start()` creates a new thread; `run()` alone does not.
- Prefer `Runnable` when there is no return value.
- Use `Callable` when you need a return value or checked exceptions.
- `Future` represents the pending/completed result.
- `ExecutorService` manages a pool of worker threads.
- Always properly shut down an `ExecutorService` when it is no longer needed.

---

### 13. Thread Lifecycle

Java defines **6 thread states** through the `Thread.State` enum:

```
NEW
  ↓
RUNNABLE
  ↓
  ┌───────────────┐
  ↓               ↓
BLOCKED       WAITING
  ↓               ↓
  └───────┬───────┘
          ↓
    TIMED_WAITING
          ↓
       RUNNABLE
          ↓
     TERMINATED
```

A thread can move between states depending on scheduling, locks, waiting, sleeping, etc.

---

### 14. `NEW`

A thread is in `NEW` state when the `Thread` object has been created but `start()` has **not** been called.

```java
Thread t = new Thread(() -> {
    System.out.println("Running");
});

System.out.println(t.getState());
```

Output:

```
NEW
```

Transition:

```java
t.start();
```

```
NEW → RUNNABLE
```

---

### 15. `RUNNABLE`

A thread is `RUNNABLE` when it is eligible to run.

It may be:

- Actually executing on a CPU
- Ready and waiting for CPU scheduling

Example:

```java
Thread t = new Thread(() -> {
    System.out.println("Running");
});

t.start();

System.out.println(t.getState());
```

Conceptually:

```
RUNNABLE
   ↕
CPU scheduling
```

**Important interview point:** Java does not have a separate `RUNNING` state in `Thread.State`. A running thread is represented as `RUNNABLE`.

---

### 16. `BLOCKED`

A thread enters `BLOCKED` when it is waiting to acquire a **monitor lock** to enter a `synchronized` block/method.

Example:

```java
class Task {

    synchronized void work() {

        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

If Thread 1 is inside `work()` and holds the object's monitor, Thread 2 trying to enter the same synchronized method may become:

```
Thread 2
   ↓
BLOCKED
   ↓
Waiting for monitor lock
```

Important distinction:

> `BLOCKED` means waiting to **acquire a lock**.
> 

---

### 17. `WAITING`

A thread enters `WAITING` when it waits indefinitely for another thread to perform some action.

Common causes:

```
Object.wait()
Thread.join()
LockSupport.park()
```

Example:

```java
Thread t = new Thread(() -> {

    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

t.start();

t.join();
```

The calling thread can enter `WAITING` while waiting indefinitely for `t` to terminate.

`Object.wait()` is another important example:

```java
synchronized (obj) {
    obj.wait();
}
```

The thread waits until another thread calls:

```java
obj.notify();
```

or:

```java
obj.notifyAll();
```

---

### 18. `TIMED_WAITING`

A thread enters `TIMED_WAITING` when it waits for a **specified amount of time**.

Common methods:

```
Thread.sleep()
Object.wait(timeout)
Thread.join(timeout)
LockSupport.parkNanos()
LockSupport.parkUntil()
```

Example:

```java
Thread.sleep(2000);
```

The thread enters:

```
TIMED_WAITING
```

for approximately 2 seconds.

Another example:

```java
t.join(2000);
```

The calling thread waits for at most 2 seconds.

---

### 19. `TERMINATED`

A thread enters `TERMINATED` after its `run()` method completes or terminates due to an uncaught exception.

```java
Thread t = new Thread(() -> {
    System.out.println("Task");
});

t.start();

try {
    t.join();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}

System.out.println(t.getState());
```

Output:

```
TERMINATED
```

A terminated thread **cannot be started again**.

```java
t.start(); // IllegalThreadStateException
```

---

## Most Important Comparison

| State | What is the thread doing? |
| --- | --- |
| `NEW` | Created but not started |
| `RUNNABLE` | Ready/running under JVM scheduling |
| `BLOCKED` | Waiting to acquire a monitor lock |
| `WAITING` | Waiting indefinitely for another thread/action |
| `TIMED_WAITING` | Waiting for a specified time |
| `TERMINATED` | Execution completed |

### `BLOCKED` vs `WAITING` vs `TIMED_WAITING`

This is a common interview question.

```
BLOCKED
→ Waiting for a monitor lock

WAITING
→ Waiting indefinitely for another thread/action

TIMED_WAITING
→ Waiting for a specific amount of time
```

Examples:

```
BLOCKED        → synchronized lock unavailable
WAITING        → wait(), join()
TIMED_WAITING  → sleep(), wait(timeout), join(timeout)
```

### One important correction to remember

Do not describe `RUNNABLE` as simply "running."

In Java's `Thread.State`, `RUNNABLE` includes both **ready-to-run and currently executing** threads. There is no separate `RUNNING` enum state.

---

### 20. `start()`

`start()` starts a **new thread of execution**. The JVM then invokes that thread's `run()` method.

```java
Thread t = new Thread(
    () -> System.out.println("Running")
);

t.start();
```

Flow:

```
start()
  ↓
New thread created
  ↓
run()
```

Important:

```java
t.start(); // New thread
t.run();   // Normal method call
```

A thread can be started only **once**.

```java
t.start();
t.start(); // IllegalThreadStateException
```

---

### 21. `run()`

`run()` contains the code that the thread executes.

```java
Thread t = new Thread(() -> {
    System.out.println("Task");
});
```

The lambda is effectively the implementation of `run()`.

You normally **do not call** **`run()`** **directly** when you want multithreading.

```java
t.run();
```

This executes on the **current thread**, not a new thread.

```
t.start() → new thread → run()
t.run()   → current thread → run()
```

---

### 22. `sleep()`

`Thread.sleep()` pauses the **currently executing thread** for a specified duration.

```java
Thread.sleep(2000);
```

This pauses the current thread for approximately 2 seconds.

Example:

```java
try {
    Thread.sleep(1000);
    System.out.println("After 1 second");
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

Important points:

- `sleep()` is a **static method** of `Thread`.
- It affects the **current thread**.
- It throws `InterruptedException`.
- It does **not release a lock/monitor** held by the thread.

Example:

```java
synchronized (obj) {
    Thread.sleep(2000);
}
```

During the sleep, the thread continues to hold `obj`'s monitor.

---

### 23. `join()`

`join()` makes the **calling thread wait for another thread to finish**.

```java
Thread t = new Thread(() -> {
    System.out.println("Task running");
});

t.start();

try {
    t.join();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}

System.out.println("Task completed");
```

Conceptually:

```
Main Thread
    |
    | join()
    ↓
waits for Thread t
    |
    ↓
Thread t finishes
    |
    ↓
Main continues
```

You can also specify a timeout:

```java
t.join(2000);
```

The calling thread waits for at most approximately 2 seconds.

**Interview point:**

> `join()` waits for another thread; `sleep()` pauses the current thread.
> 

---

### 24. `interrupt()`

`interrupt()` is used to **request that a thread stop what it is doing or respond to interruption**.

It does not forcibly kill the thread.

```java
Thread t = new Thread(() -> {

    try {
        Thread.sleep(5000);
    } catch (InterruptedException e) {
        System.out.println("Thread interrupted");
    }
});

t.start();

t.interrupt();
```

Because the thread is sleeping, `interrupt()` causes `sleep()` to throw `InterruptedException`.

Important:

```
interrupt() ≠ forcefully terminate thread
```

For a thread not currently in an interruptible blocking operation, the interrupt status is generally set.

You can check it using:

```java
Thread.currentThread().isInterrupted();
```

or:

```java
Thread.interrupted();
```

Important difference:

```
isInterrupted()
→ checks status without clearing it

interrupted()
→ checks current thread's status and clears it
```

---

### 25. `yield()`

`yield()` is a hint to the scheduler that the current thread is willing to give other runnable threads an opportunity to execute.

```java
Thread.yield();
```

Example:

```java
Thread t = new Thread(() -> {

    for (int i = 0; i < 5; i++) {

        System.out.println(i);

        Thread.yield();
    }
});
```

Important:

`yield()` does **not guarantee** that another thread will run.

The scheduler may:

- Switch to another thread
- Continue running the current thread
- Ignore the hint

Therefore, don't use `yield()` for synchronization or correctness.

---

## Most Important Comparison

| Method | Purpose | Affects |
| --- | --- | --- |
| `start()` | Starts new thread | New thread |
| `run()` | Contains/executes task | Current thread if called directly |
| `sleep()` | Pauses execution temporarily | Current thread |
| `join()` | Waits for another thread | Calling thread |
| `interrupt()` | Requests interruption | Target thread |
| `yield()` | Gives scheduler a hint | Current thread |

### `sleep()` vs `join()` — very common interview question

```
sleep()
→ "Pause me for some time."

join()
→ "Wait until that thread finishes."
```

### `interrupt()` — important interview trap

If asked:

**"Does `interrupt()` kill a thread?"**

No.

It is a **cooperative interruption mechanism**. The target thread must respond appropriately, either through an interruptible operation throwing `InterruptedException` or by checking/interpreting its interrupt status.

---

### 26. Race Condition

A **race condition** occurs when multiple threads access shared mutable data concurrently and the final result depends on the timing/interleaving of their execution.

Example:

```java
class Counter {

    int count = 0;

    void increment() {
        count++;
    }
}
```

If two threads execute:

```java
counter.increment();
```

simultaneously, you might expect:

```
0 → 1 → 2
```

But `count++` is not atomic. It is effectively:

```
read count
add 1
write count
```

Possible interleaving:

```
Thread 1 → read 0
Thread 2 → read 0
Thread 1 → write 1
Thread 2 → write 1
```

Final result:

```
1
```

instead of:

```
2
```

This is a race condition.

---

### 27. Critical Section

A **critical section** is a section of code that accesses shared resources and must not be executed concurrently by multiple threads when doing so would cause incorrect behavior.

Example:

```java
void increment() {
    count++; // critical section
}
```

Conceptually:

```
Thread 1 ──┐
           ↓
      Critical Section
           ↓
Thread 2 ──┘
```

Only one thread should execute the protected critical section at a time.

---

### 28. `synchronized`

`synchronized` is a Java mechanism used to provide **mutual exclusion** and establish memory-visibility guarantees around a monitor.

Example:

```java
class Counter {

    private int count = 0;

    synchronized void increment() {
        count++;
    }
}
```

If multiple threads call `increment()`, only one thread at a time can execute that synchronized method for the same object.

```
Thread 1 → acquires lock → executes
Thread 2 → waits
Thread 1 → releases lock
Thread 2 → acquires lock → executes
```

It helps prevent race conditions when protecting shared mutable state.

---

### 29. Synchronized Method

A method can be declared with `synchronized`.

```java
class Counter {

    private int count;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

For an **instance synchronized method**, the lock is associated with the current object:

```
this
```

Conceptually:

```java
public synchronized void increment() {
}
```

is similar to:

```java
public void increment() {

    synchronized (this) {
        // method body
    }
}
```

Therefore, two threads cannot simultaneously execute synchronized instance methods protected by the **same object's monitor**.

---

### 30. Synchronized Block

Instead of synchronizing an entire method, you can synchronize only the critical section.

```java
class Counter {

    private int count;

    public void increment() {

        // non-critical code

        synchronized (this) {
            count++;
        }

        // more non-critical code
    }
}
```

You can also use a dedicated lock object:

```java
class Counter {

    private int count;

    private final Object lock = new Object();

    public void increment() {

        synchronized (lock) {
            count++;
        }
    }
}
```

This is often preferable because it gives you **more precise control over the locking scope**.

---

### 31. Object Monitor

Every Java object is associated with a **monitor** that can be used for synchronization.

When a thread enters:

```java
synchronized (obj) {
    // critical section
}
```

it must acquire `obj`'s monitor.

Conceptually:

```
Object
  |
  └── Monitor
        |
        └── Lock ownership
```

If another thread tries to synchronize on the same object while the monitor is already held, that thread must wait until the monitor becomes available.

Example:

```java
Object lock = new Object();

synchronized (lock) {
    // only one thread at a time
}
```

---

### 32. Intrinsic Lock

An **intrinsic lock** is the lock associated with an object's monitor.

`synchronized` uses these intrinsic locks.

```java
synchronized (obj) {
    // obj's intrinsic lock is acquired
}
```

For an instance synchronized method:

```java
synchronized void work() {
}
```

the intrinsic lock is effectively:

```
this
```

For a static synchronized method:

```java
static synchronized void work() {
}
```

the lock is associated with the **Class object**, conceptually:

```
MyClass.class
```

---

## Most Important Relationship

These concepts are closely connected:

```
Shared mutable data
       ↓
Multiple threads access it
       ↓
Race condition possible
       ↓
Identify critical section
       ↓
Protect it with synchronized
       ↓
Thread acquires object's monitor
       ↓
Intrinsic lock provides mutual exclusion
```

### Synchronized method vs synchronized block

| Synchronized Method | Synchronized Block |
| --- | --- |
| Locks entire method | Locks only selected code |
| Simpler | More flexible |
| Can protect unnecessary code | Smaller critical section |
| Instance method locks `this` | You choose the lock object |
| Static method locks class object | Can use any appropriate object |

**Interview point:** `synchronized` provides **mutual exclusion** and also ensures appropriate **visibility/happens-before guarantees** between lock release and a subsequent acquisition of the same monitor.

---

### 33. `wait()`

`wait()` causes the current thread to **release the monitor and wait** until another thread notifies it.

It must be called while the thread owns that object's monitor, normally inside `synchronized`.

```java
synchronized (lock) {
    lock.wait();
}
```

When `wait()` is called:

```
Thread
  ↓
releases lock
  ↓
WAITING
  ↓
another thread calls notify/notifyAll
  ↓
tries to reacquire lock
  ↓
continues
```

Important:

> `wait()` releases the object's monitor.
> 

Example:

```java
synchronized (lock) {

    while (!ready) {
        lock.wait();
    }

    System.out.println("Processing");
}
```

Use `while`, not `if`, to re-check the condition after waking.

---

### 34. `notify()`

`notify()` wakes **one** thread waiting on the same object's monitor.

```java
synchronized (lock) {
    lock.notify();
}
```

Important:

- The notifying thread does **not immediately give up the lock**.
- The awakened thread must reacquire the monitor before continuing.
- If multiple threads are waiting, the JVM does not provide a general guarantee about which one is selected.

Conceptually:

```
Waiting threads:
T1
T2
T3

notify()
  ↓
One waiting thread is made eligible to continue
```

---

### 35. `notifyAll()`

`notifyAll()` wakes **all threads waiting on that object's monitor**.

```java
synchronized (lock) {
    lock.notifyAll();
}
```

However, they don't all execute simultaneously inside the synchronized block. They must compete to reacquire the monitor.

```
T1 ─┐
T2 ─┼─ notifyAll() → compete for lock
T3 ─┘
```

### `notify()` vs `notifyAll()`

| `notify()` | `notifyAll()` |
| --- | --- |
| Wakes one waiting thread | Wakes all waiting threads |
| Less overhead | More overhead |
| Can be appropriate when one waiter can make progress | Safer when multiple waiting conditions may be relevant |

### Important Interview Rule

You cannot correctly do this:

```java
lock.wait(); // IllegalMonitorStateException
```

unless the current thread owns `lock`'s monitor.

Correct:

```java
synchronized (lock) {
    lock.wait();
}
```

The same monitor-ownership requirement applies to `notify()` and `notifyAll()`.

Also remember:

```
wait()       → releases lock
sleep()      → does NOT release lock
```

This is one of the most common multithreading interview questions.

---

# Concurrency Problems

### 36. Race Condition

A race condition occurs when multiple threads access shared mutable state concurrently and the result depends on execution timing.

```java
class Counter {

    int count = 0;

    void increment() {
        count++;
    }
}
```

`count++` is not atomic:

```
read → modify → write
```

Two threads can interfere with each other.

Solution examples:

```java
synchronized void increment() {
    count++;
}
```

or use appropriate atomic/concurrent utilities such as:

```
AtomicInteger
```

---

### 37. Deadlock

A **deadlock** occurs when two or more threads wait indefinitely for resources/locks held by each other.

Classic example:

```java
Object lock1 = new Object();
Object lock2 = new Object();

Thread t1 = new Thread(() -> {

    synchronized (lock1) {

        synchronized (lock2) {
            System.out.println("T1");
        }
    }
});

Thread t2 = new Thread(() -> {

    synchronized (lock2) {

        synchronized (lock1) {
            System.out.println("T2");
        }
    }
});
```

Possible situation:

```
T1 → holds lock1 → waits for lock2

T2 → holds lock2 → waits for lock1
```

Neither can continue.

```
T1 ──waits for──> lock2
 ↑                  |
 |                  ↓
lock1 <──held by── T2
```

Common prevention:

- Always acquire multiple locks in a consistent order.
- Keep critical sections small.
- Avoid unnecessary nested locks.
- Use higher-level concurrency utilities where appropriate.
- Consider timed lock acquisition with `tryLock()` when using `Lock`.

---

### 38. Starvation

**Starvation** occurs when a thread is continually denied the resources/CPU time it needs to make progress.

Example concept:

```
Thread T1 → repeatedly gets the lock
Thread T2 → keeps waiting
Thread T2 → makes little/no progress
```

Possible causes:

- Unfair lock/resource scheduling
- High-priority thread monopolizing resources
- Long-running synchronized sections
- Poor thread-pool configuration

Unlike deadlock, the system as a whole may still be making progress.

```
Deadlock   → threads stuck waiting on each other
Starvation → one thread keeps getting denied
```

---

### 39. Livelock

A **livelock** occurs when threads are not blocked, but they continuously respond to each other and **still make no useful progress**.

Example:

```
Thread A → detects conflict → backs off
Thread B → detects conflict → backs off
Thread A → retries
Thread B → retries
Thread A → backs off
Thread B → backs off
...
```

The threads are active, but no useful work gets completed.

Real-world analogy:

Two people walking toward each other in a hallway:

```
Person A moves left
Person B moves left

A moves right
B moves right

A moves left
B moves left
```

Both keep reacting but remain stuck.

---

## Deadlock vs Starvation vs Livelock

| Problem | Thread state/behavior | Main idea |
| --- | --- | --- |
| Race condition | Concurrent execution | Incorrect result due to timing |
| Deadlock | Waiting indefinitely | Threads wait for each other's resources |
| Starvation | Repeatedly denied resources | One thread cannot get enough opportunity |
| Livelock | Actively running/retrying | Threads respond continuously but make no progress |

### Very important distinction

```
Deadlock:
"I am waiting for you, and you are waiting for me."

Starvation:
"Everyone keeps getting the resource except me."

Livelock:
"I keep reacting to you, but neither of us gets anywhere."

Race condition:
"The result depends on who gets there first."
```

These are four concepts you should be able to explain with a simple example in an interview.

---

### 40. `volatile`

`volatile` tells the JVM that a variable may be accessed by multiple threads and that reads/writes should have the required **visibility semantics** across threads.

```java
class Worker {

    private volatile boolean running = true;

    void stop() {
        running = false;
    }

    void work() {

        while (running) {
            // do work
        }
    }
}
```

Without `volatile`, another thread may not promptly observe the updated value.

Important:

> `volatile` provides **visibility and ordering guarantees**, but it does **not make compound operations atomic**.
> 

This is not safe:

```java
volatile int count = 0;

count++; // NOT atomic
```

Because:

```
read → add → write
```

For atomic increment, use `AtomicInteger`.

---

### 41. Atomic Classes

Atomic classes are provided in:

```
java.util.concurrent.atomic
```

They provide thread-safe operations on individual variables without requiring traditional `synchronized` blocks for those operations.

Common classes:

```
AtomicInteger
AtomicLong
AtomicBoolean
AtomicReference
```

Example:

```java
AtomicInteger count =
    new AtomicInteger(0);

count.incrementAndGet();
count.incrementAndGet();

System.out.println(count.get()); // 2
```

Useful when you need lightweight thread-safe operations on individual values.

---

### 42. `AtomicInteger`

`AtomicInteger` provides atomic operations on an `int`.

```java
AtomicInteger count =
    new AtomicInteger(0);

count.incrementAndGet();

System.out.println(count.get());
```

Important methods:

```
get()
set()
incrementAndGet()
getAndIncrement()
decrementAndGet()
getAndDecrement()
addAndGet()
compareAndSet()
```

Difference:

```java
count.incrementAndGet();
```

returns the **new value**.

```java
count.getAndIncrement();
```

returns the **old value** and then increments.

Example:

```java
AtomicInteger x =
    new AtomicInteger(5);

System.out.println(
    x.getAndIncrement()
); // 5

System.out.println(
    x.get()
); // 6

System.out.println(
    x.incrementAndGet()
); // 7
```

---

### 43. `AtomicLong`

`AtomicLong` provides atomic operations on a `long`.

```java
AtomicLong counter =
    new AtomicLong(0);

counter.incrementAndGet();

System.out.println(counter.get());
```

Useful for things such as:

```
Counters
Sequence numbers
Request counts
Statistics
```

It provides operations similar to `AtomicInteger`.

---

### 44. CAS — Compare-And-Set

**CAS (Compare-And-Set)** is a fundamental lock-free synchronization technique used by many atomic classes.

Concept:

```
if currentValue == expectedValue
        ↓
    update value
else
    don't update
```

Example:

```java
AtomicInteger count =
    new AtomicInteger(10);

boolean success =
    count.compareAndSet(10, 20);

System.out.println(success); // true
```

If the current value isn't `10`:

```java
count.compareAndSet(10, 20);
```

the update fails.

Conceptually:

```
Current = 10
Expected = 10
New = 20

10 == 10
   ↓
Update → 20
```

CAS is commonly used to build **non-blocking/lock-free algorithms**.

Important interview point:

> CAS avoids blocking in the same way a traditional mutex does, but it can suffer from repeated retries under contention.
> 

---

### 45. `ReentrantLock`

`ReentrantLock` is an explicit lock implementation from:

```
java.util.concurrent.locks
```

Example:

```java
Lock lock =
    new ReentrantLock();

lock.lock();

try {
    // critical section
} finally {
    lock.unlock();
}
```

"Reentrant" means the same thread can acquire the same lock multiple times without deadlocking itself.

```java
lock.lock();
lock.lock();

try {
    // work
} finally {
    lock.unlock();
    lock.unlock();
}
```

Important features compared with `synchronized` include:

- `tryLock()`
- Interruptible lock acquisition
- Optional fairness policy
- Explicit lock/unlock control

Example:

```java
if (lock.tryLock()) {

    try {
        // critical section
    } finally {
        lock.unlock();
    }
}
```

**Critical rule:** Always unlock in `finally`.

---

### 46. `ReadWriteLock`

`ReadWriteLock` provides two locks:

```
Read Lock
Write Lock
```

Multiple threads can hold the **read lock simultaneously**, while the write lock is exclusive.

```java
ReadWriteLock lock =
    new ReentrantReadWriteLock();

lock.readLock().lock();

try {
    // read data
} finally {
    lock.readLock().unlock();
}
```

Write:

```java
lock.writeLock().lock();

try {
    // modify data
} finally {
    lock.writeLock().unlock();
}
```

Conceptually:

```
Read + Read    → allowed
Read + Write   → blocked
Write + Write  → blocked
Write + Read   → blocked
```

Useful when:

```
Reads >> Writes
```

For example, a configuration/cache structure that is read frequently but updated infrequently.

---

### 47. `Semaphore`

A `Semaphore` controls access to a resource using a fixed number of **permits**.

```java
Semaphore semaphore =
    new Semaphore(3);
```

At most 3 permits can be acquired simultaneously.

```java
semaphore.acquire();

try {
    // use limited resource
} finally {
    semaphore.release();
}
```

Example:

```
Semaphore(3)

Thread 1 → permit
Thread 2 → permit
Thread 3 → permit
Thread 4 → waits
```

When one thread releases:

```
Thread 1 → release
Thread 4 → can acquire
```

Common use cases:

```
Connection pools
Rate limiting
Limited resources
Controlling concurrent tasks
```

---

### 48. `CountDownLatch`

`CountDownLatch` allows one or more threads to wait until a counter reaches zero.

Example:

```java
CountDownLatch latch =
    new CountDownLatch(3);
```

Three worker tasks:

```java
latch.countDown();
```

Waiting thread:

```java
latch.await();

System.out.println(
    "All tasks completed"
);
```

Conceptually:

```
Count = 3

Task 1 → countDown() → 2
Task 2 → countDown() → 1
Task 3 → countDown() → 0
                         ↓
                      await() ends
```

Important:

> A `CountDownLatch` is **one-shot**. Once the count reaches zero, it cannot be reset.
> 

---

### 49. `CyclicBarrier`

`CyclicBarrier` allows multiple threads to **wait for each other at a common synchronization point**.

```java
CyclicBarrier barrier =
    new CyclicBarrier(3);
```

Each thread calls:

```java
barrier.await();
```

The barrier opens when all 3 participating threads reach it.

```
Thread 1 ──┐
Thread 2 ──┼── await()
Thread 3 ──┘
     ↓
All arrived
     ↓
Continue
```

Unlike `CountDownLatch`, a `CyclicBarrier` can be **reused** for another cycle.

Typical use:

```
Phase 1 → all threads finish
          ↓
Phase 2 → all threads start
          ↓
Phase 3 → all threads finish
```

---

### 50. `BlockingQueue`

`BlockingQueue` is a thread-safe queue designed for producer-consumer scenarios.

Important implementations:

```
ArrayBlockingQueue
LinkedBlockingQueue
PriorityBlockingQueue
DelayQueue
```

Example:

```java
BlockingQueue<Integer> queue =
    new ArrayBlockingQueue<>(10);
```

Producer:

```java
queue.put(10);
```

Consumer:

```java
Integer value =
    queue.take();
```

If the queue is full:

```
put() → waits
```

If the queue is empty:

```
take() → waits
```

Conceptually:

```
Producer
   ↓
put()
   ↓
BlockingQueue
   ↓
take()
   ↓
Consumer
```

This lets you implement producer-consumer communication without manually coordinating `wait()` and `notify()`.

---

## Must-Know Comparisons

### `volatile` vs Atomic

```
volatile
→ visibility + ordering
→ NOT compound-operation atomicity

AtomicInteger
→ atomic operations
→ uses CAS-based mechanisms
```

### `synchronized` vs `ReentrantLock`

```
synchronized
→ simpler
→ automatic lock release
→ language-level construct

ReentrantLock
→ explicit lock/unlock
→ tryLock()
→ interruptible acquisition
→ fairness option
```

### `CountDownLatch` vs `CyclicBarrier`

| CountDownLatch | CyclicBarrier |
| --- | --- |
| One-shot | Reusable |
| Wait for count to reach zero | Wait for all participating threads |
| `countDown()` + `await()` | `await()` |
| Threads don't need to wait for each other symmetrically | Threads meet at a common barrier |

Easy way to remember:

```
CountDownLatch
→ "Wait until these tasks are done."

CyclicBarrier
→ "Everyone reach this point before continuing."
```

### `Semaphore` vs `ReentrantLock`

```
ReentrantLock
→ Usually one owner thread at a time

Semaphore
→ N permits
→ Up to N threads can enter
```

### `BlockingQueue` vs `Queue`

```
Queue
→ Basic queue abstraction

BlockingQueue
→ Thread-safe
→ Can block when empty/full
→ Excellent for producer-consumer systems
```

The most important advanced-concurrency mental model is:

```
volatile          → visibility
AtomicInteger     → atomic variable operations
CAS               → compare-and-update
ReentrantLock     → explicit mutual exclusion
ReadWriteLock     → concurrent reads / exclusive writes
Semaphore         → limit concurrent access
CountDownLatch    → wait for tasks/events
CyclicBarrier     → synchronize phases
BlockingQueue     → producer-consumer coordination
```

---

### 51. `Executor`

`Executor` is the simplest interface in the Executor Framework. It separates **task submission** from the mechanism used to execute the task.

```java
Executor executor =
    command -> {
        new Thread(command).start();
    };

executor.execute(
    () -> System.out.println("Task running")
);
```

Main method:

```java
void execute(Runnable command)
```

Key idea:

```
Task → Executor → Execution
```

`Executor` does not provide methods for shutdown, `Future`, or task results.

---

### 52. `ExecutorService`

`ExecutorService` extends `Executor` and provides a complete API for managing asynchronous tasks and thread pools.

```java
ExecutorService executor =
    Executors.newFixedThreadPool(3);
```

Submit a `Runnable`:

```java
executor.submit(
    () -> System.out.println("Task")
);
```

Submit a `Callable`:

```java
Future<Integer> future =
    executor.submit(() -> 10 + 20);

System.out.println(future.get());
```

Important methods:

```
execute()
submit()
shutdown()
shutdownNow()
isShutdown()
isTerminated()
awaitTermination()
```

Always shut down an executor when it is no longer needed:

```java
executor.shutdown();
```

---

### 53. `ScheduledExecutorService`

`ScheduledExecutorService` is used for **delayed and periodic task execution**.

```java
ScheduledExecutorService scheduler =
    Executors.newScheduledThreadPool(2);
```

Run after a delay:

```java
scheduler.schedule(
    () -> System.out.println("Hello"),
    5,
    TimeUnit.SECONDS
);
```

Run periodically:

```java
scheduler.scheduleAtFixedRate(
    () -> System.out.println("Running"),
    0,
    5,
    TimeUnit.SECONDS
);
```

Important methods:

```
schedule()
scheduleAtFixedRate()
scheduleWithFixedDelay()
```

Difference:

```
scheduleAtFixedRate()
→ attempts to maintain a fixed period between scheduled start times

scheduleWithFixedDelay()
→ waits for the previous execution to finish, then waits for the specified delay
```

---

### 54. `ThreadPoolExecutor`

`ThreadPoolExecutor` is the configurable implementation behind many thread-pool use cases.

You can control:

```
Core threads
Maximum threads
Keep-alive time
Work queue
Thread factory
Rejected execution policy
```

Example:

```java
ThreadPoolExecutor executor =
    new ThreadPoolExecutor(
        2,
        5,
        60,
        TimeUnit.SECONDS,
        new LinkedBlockingQueue<>()
    );
```

Conceptually:

```
              ThreadPoolExecutor
                     |
           -----------------------
           |          |          |
        Worker 1   Worker 2   Worker 3
           |
        Task Queue
```

Important interview point:

> A thread pool reuses worker threads instead of creating a new thread for every task.
> 

---

### 55. Fixed Thread Pool

Created using:

```java
ExecutorService executor =
    Executors.newFixedThreadPool(3);
```

It maintains a fixed number of worker threads.

```
3 threads
   ↓
Task Queue
```

If all three threads are busy, additional tasks wait in the queue.

Good for:

- Controlled concurrency
- CPU-bound workloads
- Applications where you want a predictable number of worker threads

Example:

```java
for (int i = 0; i < 10; i++) {

    executor.submit(
        () -> System.out.println("Task")
    );
}
```

Only up to the configured number of worker threads execute concurrently.

---

### 56. Cached Thread Pool

Created using:

```java
ExecutorService executor =
    Executors.newCachedThreadPool();
```

It creates new threads when needed and reuses previously created idle threads when possible.

Conceptually:

```
Tasks
 ↓
Cached Pool
 ↓
Reuse idle threads
or
Create new thread
```

Useful for many **short-lived asynchronous tasks**, but it can create a large number of threads under heavy/unbounded submission.

For production systems, an explicitly configured `ThreadPoolExecutor` is often preferable when you need strict resource bounds.

---

### 57. Scheduled Thread Pool

Created using:

```java
ScheduledExecutorService executor =
    Executors.newScheduledThreadPool(2);
```

Used for delayed and periodic tasks.

```java
executor.schedule(
    () -> System.out.println("Delayed"),
    3,
    TimeUnit.SECONDS
);
```

Periodic:

```java
executor.scheduleAtFixedRate(
    () -> System.out.println("Periodic"),
    0,
    10,
    TimeUnit.SECONDS
);
```

Think:

```
Fixed Thread Pool
→ execute tasks

Scheduled Thread Pool
→ execute tasks at specified times/intervals
```

---

### 58. `Future`

`Future` represents the result of an asynchronous computation.

```java
ExecutorService executor =
    Executors.newFixedThreadPool(2);

Future<Integer> future =
    executor.submit(() -> 10 + 20);
```

Get the result:

```java
Integer result =
    future.get();

System.out.println(result); // 30
```

Important methods:

```
get()
isDone()
isCancelled()
cancel()
```

`get()` can block until the computation completes.

With timeout:

```java
future.get(
    2,
    TimeUnit.SECONDS
);
```

---

### 59. `Callable`

`Callable<V>` represents a task that:

- Returns a result
- Can throw checked exceptions

```java
Callable<Integer> task = () -> {
    return 10 + 20;
};
```

Submit it:

```java
Future<Integer> future =
    executor.submit(task);
```

Difference:

```
Runnable
→ run()
→ no return value

Callable
→ call()
→ returns value
→ can throw checked exceptions
```

---

### 60. `CompletableFuture`

`CompletableFuture` provides a more powerful model for **asynchronous, composable, non-blocking workflows**.

Example:

```java
CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 10);

future
    .thenApply(x -> x * 2)
    .thenAccept(System.out::println);
```

Conceptually:

```
supplyAsync()
      ↓
thenApply()
      ↓
thenAccept()
```

Output:

```
20
```

Unlike a simple `Future`, you can compose multiple asynchronous operations.

Example:

```java
CompletableFuture
    .supplyAsync(() -> getUser())
    .thenApply(user -> getOrders(user))
    .thenAccept(
        orders -> System.out.println(orders)
    );
```

Important methods:

```
supplyAsync()
runAsync()
thenApply()
thenAccept()
thenRun()
thenCompose()
thenCombine()
exceptionally()
handle()
whenComplete()
allOf()
anyOf()
```

### `thenApply()` vs `thenCompose()`

Very important interview concept.

`thenApply()` transforms a result:

```java
future.thenApply(
    user -> user.getName()
);
```

`thenCompose()` chains another asynchronous operation and avoids nested futures:

```
thenApply:
CompletableFuture<T>
      ↓
CompletableFuture<CompletableFuture<R>>

thenCompose:
CompletableFuture<T>
      ↓
CompletableFuture<R>
```

Similar idea to `map()` vs `flatMap()`.

---

# Concurrent Collections

### 61. `ConcurrentHashMap`

`ConcurrentHashMap` is a thread-safe Map designed for concurrent access.

```java
ConcurrentHashMap<String, Integer> map =
    new ConcurrentHashMap<>();

map.put("Java", 10);
map.put("Spring", 20);
```

Multiple threads can safely access/update it concurrently.

Important:

```
ConcurrentHashMap
→ thread-safe
→ high concurrency
→ does not allow null keys or null values
```

Unlike synchronizing an entire `HashMap`, `ConcurrentHashMap` is designed to allow a high degree of concurrent access.

Example:

```java
map.compute(
    "Java",
    (key, value) ->
        value == null ? 1 : value + 1
);
```

This is useful because the computation is performed as part of the map's concurrent operation rather than requiring a separate external check/update sequence.

---

### 62. `CopyOnWriteArrayList`

`CopyOnWriteArrayList` is a thread-safe List optimized for situations with:

```
Many reads
Few writes
```

When the list is modified, it creates a new underlying array.

```java
CopyOnWriteArrayList<String> list =
    new CopyOnWriteArrayList<>();

list.add("Java");
list.add("Spring");
```

Conceptually:

```
Read
 ↓
Existing array

Write
 ↓
Copy array
 ↓
Modify new array
 ↓
Replace reference
```

Advantages:

- Safe concurrent iteration
- Readers don't need to lock
- Iterators see a stable snapshot

Disadvantages:

- Writes are expensive
- Extra memory is required
- Poor choice for frequently modified large lists

Typical use cases:

```
Configuration data
Listener lists
Read-heavy shared data
```

---

### 63. `BlockingQueue`

`BlockingQueue` is a thread-safe queue designed especially for **producer-consumer systems**.

```java
BlockingQueue<Integer> queue =
    new ArrayBlockingQueue<>(10);
```

Producer:

```java
queue.put(100);
```

Consumer:

```java
int value =
    queue.take();
```

Important behavior:

```
Queue full
→ put() waits

Queue empty
→ take() waits
```

Common implementations:

```
ArrayBlockingQueue
LinkedBlockingQueue
PriorityBlockingQueue
DelayQueue
```

Example producer-consumer model:

```
Producer
   |
   | put()
   ↓
BlockingQueue
   |
   | take()
   ↓
Consumer
```

This avoids manually implementing coordination with `wait()` and `notify()` in many producer-consumer scenarios.

---

## Must-Know Executor Hierarchy

```
Executor
   ↓
ExecutorService
   ↓
ScheduledExecutorService
```

`ThreadPoolExecutor` is an important concrete implementation of `ExecutorService`.

```
Executor
   ↓
ExecutorService
   ↓
ThreadPoolExecutor
```

`ScheduledThreadPoolExecutor` is the scheduled implementation used for scheduled executor behavior.

---

## Most Important Comparisons

### `Future` vs `CompletableFuture`

| Future | CompletableFuture |
| --- | --- |
| Basic async result | Advanced async pipeline |
| `get()` commonly used to retrieve result | Supports callback/composition APIs |
| Limited composition | `thenApply`, `thenCompose`, `thenCombine`, etc. |
| Mostly pull-based | Supports continuation-style workflows |
| Cancellation supported | Cancellation + rich completion/error handling |

### Fixed vs Cached Thread Pool

```
Fixed
→ fixed number of worker threads
→ predictable concurrency

Cached
→ dynamically creates/reuses threads
→ suitable for many short-lived tasks
→ can grow substantially under load
```

### `ConcurrentHashMap` vs `CopyOnWriteArrayList` vs `BlockingQueue`

```
ConcurrentHashMap
→ concurrent key-value data

CopyOnWriteArrayList
→ read-heavy concurrent List

BlockingQueue
→ producer-consumer communication
```

The most important advanced-concurrency mental model is:

```
volatile          → visibility
AtomicInteger     → atomic variable operations
CAS               → compare-and-update
ReentrantLock     → explicit mutual exclusion
ReadWriteLock     → concurrent reads / exclusive writes
Semaphore         → limit concurrent access
CountDownLatch    → wait for tasks/events
CyclicBarrier     → synchronize phases
BlockingQueue     → producer-consumer coordination
```

### One important modern-Java note

For new Java applications, don't blindly use `Executors.newFixedThreadPool()` or `newCachedThreadPool()` simply because they are convenient factory methods. If you need strict control over queue capacity, maximum threads, rejection behavior, or other resource limits, configure a `ThreadPoolExecutor` explicitly.

Also, in modern Java, **virtual threads** are an important additional concurrency model, which we should cover separately when you reach that topic.
# Chapter 13: Concurrency — Bullet Points

## OCP Exam Objectives Covered in This Chapter

**Managing Concurrent Code Execution**
- Create both platform and virtual threads. Use both Runnable and Callable objects, manage the thread lifecycle, and use different Executor services and concurrent API to run tasks.
- Develop thread-safe code, using locking mechanisms and concurrent API.
- Process Java collections concurrently and utilize parallel streams.

**Working with Streams and Lambda Expressions**
- Perform decomposition, concatenation, and reduction, and grouping and partitioning on sequential and parallel streams.

---

## Random Number Generation — `ThreadLocalRandom` `ints()` Methods

- `ints()`: Infinite stream of any int values
- `ints(lowestInclusive, highestExclusive)`: Unlimited stream of int values between the two parameters, excluding the second one
- `ints(numberIntsToInclude)`: Finite stream of the requested number of int values
- `ints(numberIntsToInclude, lowestInclusive, highestExclusive)`: Finite stream of the requested number of int values between the two parameters, excluding the second one
- `nextInt(highestExclusive)`: Single int between 0 and the parameter, not including the parameter
- `nextInt(lowestInclusive, highestExclusive)`: Single int between the first parameter and the second parameter, not including the second parameter

(Similar methods exist for `doubles()` and `longs()`, e.g. `nextDouble()`, `nextLong()`, etc.)

---

## Reviewing the Lock Framework

The `ReentrantLock` class supports the same features as a synchronized block while adding these improvements:
- Ability to request a lock without blocking.
- Ability to request a lock while blocking for a specified amount of time.
- A lock can be created with a fairness property, in which the lock is granted to threads in the order in which it was requested.

---

## Possible Outcomes for a Race Condition (Username Creation Example)

- Both users are able to create accounts with the username ZooFan.
- Neither user is able to create an account with the username ZooFan, and an error message is returned to both users.
- One user is able to create an account with the username ZooFan, while the other user receives an error message.

---

## Requirements for Parallel Reduction with `collect()`

- The stream is parallel.
- The parameter of the `collect()` operation has the `Characteristics.CONCURRENT` characteristic.
- Either the stream is unordered or the collector has the characteristic `Characteristics.UNORDERED`.

---

## Table 13.13 — Synchronized Collections Methods (java.util.Collections)

- `synchronizedCollection(Collection<T> c)`
- `synchronizedList(List<T> list)`
- `synchronizedMap(Map<K,V> m)`
- `synchronizedNavigableMap(NavigableMap<K,V> m)`
- `synchronizedNavigableSet(NavigableSet<T> s)`
- `synchronizedSet(Set<T> s)`
- `synchronizedSortedMap(SortedMap<K,V> m)`
- `synchronizedSortedSet(SortedSet<T> s)`

---

## Exam Essentials

- **Identify the differences between platform threads and virtual threads.** Platform threads map to the underlying operating system threads. Virtual threads are lighter weight, using a carrier thread only when they need to run. A carrier thread runs on an operating system thread as well. Virtual threads can be run on `Executors.newVirtualThreadPerTaskExecutor()`. Since they are so lightweight, they don't need to be pooled.
- **Be able to write thread-safe code.** Thread-safety is about protecting shared data from concurrent access. A monitor can be used to ensure that only one thread processes a particular section of code at a time. In Java, monitors can be implemented with a synchronized block or method or using an instance of `Lock`. `ReentrantLock` has a number of advantages over using a synchronized block, including the ability to check whether a lock is available without blocking it, as well as supporting the fair acquisition of locks. To achieve synchronization, two or more threads must coordinate on the same shared object.
- **Be able to apply the atomic classes.** An atomic operation is one that occurs without interference from another thread. The Concurrency API includes a set of atomic classes that are similar to the primitive classes, except that they ensure that operations on them are performed atomically. Know the difference between an atomic variable and one marked with the `volatile` modifier.
- **Create concurrent tasks with a thread executor service using Runnable and Callable.** An `ExecutorService` creates and manages a single thread or a pool of threads. Instances of `Runnable` and `Callable` can both be submitted to a thread executor and will be completed using the available threads in the service. `Callable` differs from `Runnable` in that `Callable` returns a generic data type and can throw a checked exception. A `ScheduledExecutorService` can be used to schedule tasks at a fixed rate or with a fixed interval between executions.
- **Be able to use the concurrent collection classes.** The Concurrency API includes numerous collection classes that include built-in support for multithreaded processing, such as `ConcurrentHashMap`. It also includes a class `CopyOnWriteArrayList` that creates a copy of its underlying list structure every time it is modified and is useful in highly concurrent environments.
- **Identify potential threading problems.** Deadlock, starvation, and livelock are three threading problems that can occur and result in threads never completing their task. Deadlock occurs when two or more threads are blocked forever. Starvation occurs when a single thread is perpetually denied access to a shared resource. Livelock is a form of starvation where two or more threads are active but conceptually blocked forever. Finally, race conditions occur when two threads execute at the same time, resulting in an unexpected outcome.
- **Understand the impact of using parallel streams.** The Stream API allows for the easy creation of parallel streams. Using a parallel stream can cause unexpected results, since the order of operations may no longer be predictable. Some operations, such as `reduce()` and `collect()`, require special consideration to achieve optimal performance when applied to a parallel stream.

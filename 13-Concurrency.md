# Chapter 13: Concurrency

## OCP Exam Objectives Covered in This Chapter

**Managing Concurrent Code Execution**
- Create both platform and virtual threads. Use both Runnable and Callable objects, manage the thread lifecycle, and use different Executor services and concurrent API to run tasks.
- Develop thread-safe code, using locking mechanisms and concurrent API.
- Process Java collections concurrently and utilize parallel streams.

**Working with Streams and Lambda Expressions**
- Perform decomposition, concatenation, and reduction, and grouping and partitioning on sequential and parallel streams.

---

## Creating Threads: Platform vs. Virtual
- A **platform thread** maps to an operating system thread. A **virtual thread** is lightweight and uses a carrier thread (which runs on an operating system thread) only while it is running.
- Ways to create platform threads: `new Thread(runnable)`, `Thread.ofPlatform()` (builder, for example `Thread.ofPlatform().priority(1).unstarted(task)`), and pooled executors such as `Executors.newCachedThreadPool()`, `newFixedThreadPool(n)`, and `newSingleThreadExecutor()`. There is no `newPlatformThreadPool()` or `ofPlatformThread()`.
- Ways to create virtual threads: `Thread.ofVirtual()` and `Executors.newVirtualThreadPerTaskExecutor()`.
- Virtual threads are not pooled. `newVirtualThreadPerTaskExecutor()` creates a **new** virtual thread for each task, since they are cheap. Platform threads are the ones that get pooled.
- Virtual threads are daemon threads, so the JVM can exit before they finish. If the executor is not closed or the results are not waited on, output from a virtual-thread task cannot be determined ahead of time.
- `thread.start()` creates a new thread and runs the task there. `thread.run()` just runs the task on the **calling** thread, like a normal method call. For example, calling `.unstarted(task).run()` 500 times in a loop runs `task` 500 times sequentially on one thread, so a shared counter reaches exactly 500.
- `Thread.sleep()` throws the checked `InterruptedException`, so the caller must handle or declare it.

---

## Runnable and Callable
- `Runnable`: `void run()`, cannot throw checked exceptions, can throw unchecked exceptions.
- `Callable<V>`: `V call() throws Exception`, returns a generic type, can throw checked exceptions and unchecked exceptions.
- Both are functional interfaces, so both can be implemented with lambda expressions.
- A lambda is a valid `Callable` only if it takes **no parameters** and **returns a value**: `() -> 5`, `() -> "The" + "Zoo"`, and `() -> { System.out.print("Giraffe"); return 10; }`. A lambda with parameters (`a -> {return 10;}`), a block without a `return` (`() -> { String s = ""; }`), or a missing semicolon (`return null}`) is not valid.
- A lambda whose body is a statement expression with no return value, such as `() -> System.out.println("x")`, matches `Runnable`. A lambda that returns a value matches `Callable`.

---

## ExecutorService
- `ExecutorService` creates and manages a single thread or a pool of threads. Create one with the `Executors` factory methods.
- If all threads are busy, a submitted task is added to an internal queue and runs when a thread becomes available. The caller does not wait, and the task is not discarded.
- `execute(Runnable)` returns `void`. Since there is nothing to store, `list.add(service.execute(...))` does not compile.
- `submit(Runnable)` returns `Future<?>`, and `future.get()` returns `null` when the task finishes. `submit(Callable<T>)` returns `Future<T>`.
- A task defined as `() -> performCount(animal)` where `performCount()` returns `void` is a `Runnable`, so `get()` returns `null`. If the task throws, `get()` throws an `ExecutionException`.
- `Future` methods: `get()`, `get(timeout, unit)` (can throw `TimeoutException`), `isDone()`, `isCancelled()`, `cancel(boolean)`. `get()` throws the checked `InterruptedException` and `ExecutionException`.
- `shutdown()` stops accepting new tasks and lets running and queued tasks finish. It does not wait. `shutdownNow()` attempts to stop running tasks.
- `awaitTermination(timeout, unit)` waits up to the timeout and returns `true` if the executor finished or `false` if the time ran out. It does not cancel anything. For example, `shutdown()` followed by `awaitTermination(2, SECONDS)` with 5-second tasks pauses about 2 seconds and then continues.
- `ExecutorService` implements `AutoCloseable`, so it can be used in a try-with-resources statement. `close()` waits for all submitted tasks to finish. For example, three 5-second tasks on a four-thread pool take about 5 seconds, because they run at the same time.
- Assign the result of `Executors.newSingleThreadScheduledExecutor()` to a `ScheduledExecutorService`. A variable declared as `ExecutorService` cannot call the schedule methods.
- `ScheduledExecutorService` methods: `schedule(Runnable or Callable, delay, unit)`, `scheduleAtFixedRate(Runnable, initialDelay, period, unit)`, and `scheduleWithFixedDelay(Runnable, initialDelay, delay, unit)`. The last two accept only `Runnable`, so a lambda that returns `null` or a value does not compile.
- A `newSingleThreadExecutor()` runs tasks one at a time in order, so a plain `int` incremented by those tasks is updated by one thread only.

---

## Random Number Generation — `ThreadLocalRandom` `ints()` Methods

- `ints()`: Infinite stream of any int values
- `ints(lowestInclusive, highestExclusive)`: Unlimited stream of int values between the two parameters, excluding the second one
- `ints(numberIntsToInclude)`: Finite stream of the requested number of int values
- `ints(numberIntsToInclude, lowestInclusive, highestExclusive)`: Finite stream of the requested number of int values between the two parameters, excluding the second one
- `nextInt(highestExclusive)`: Single int between 0 and the parameter, not including the parameter
- `nextInt(lowestInclusive, highestExclusive)`: Single int between the first parameter and the second parameter, not including the second parameter

(Similar methods exist for `doubles()` and `longs()`, e.g. `nextDouble()`, `nextLong()`, etc.)
- `ThreadLocalRandom.current().doubles()` returns a `DoubleStream` (sequential by default). Call `.parallel()` on it to make it parallel.

---

## Thread Safety and Synchronization
- Thread safety means protecting shared data from concurrent access. Two or more threads must coordinate on the **same** shared object (the same monitor or lock) for synchronization to work.
- Atomic classes (`AtomicInteger`, `AtomicLong`, `AtomicBoolean`, and so on) make single operations such as `incrementAndGet()` and `getAndIncrement()` atomic.
- Ordinary operations such as `count++`, `++array[0]`, and `total += value` are **not** atomic. Running them from a parallel stream or several threads produces unpredictable results.
- Declaring a variable `volatile` guarantees that changes are visible to other threads, but it does not make compound operations such as `++` atomic. An atomic class does.
- A `synchronized` block locks on the object you give it. A `synchronized` **instance** method locks on `this`. A `static synchronized` method locks on the `Class` object. These are different locks, so a static synchronized method and a `synchronized (this)` block do not block each other.
- Any object, including an `Integer`, can be used as the monitor in `synchronized(obj)`.
- A method that changes shared state without synchronization (for example, `addTickets()` while another method is synchronized) leaves the class open to race conditions.
- The lazy singleton `static synchronized getInstance()` that checks `instance == null` creates at most one instance.

---

## Reviewing the Lock Framework

The `ReentrantLock` class supports the same features as a synchronized block while adding these improvements:
- Ability to request a lock without blocking.
- Ability to request a lock while blocking for a specified amount of time.
- A lock can be created with a fairness property, in which the lock is granted to threads in the order in which it was requested.

- Methods: `lock()` (blocks until the lock is available), `tryLock()` (returns a `boolean` immediately without waiting), `tryLock(timeout, unit)` (waits up to the time given), and `unlock()`. There are no `testLock()` or `attemptLock()` methods.
- A `ReentrantLock` is **not** fair by default. Use `new ReentrantLock(true)` for fair ordering.
- The lock is reentrant: a thread can acquire it more than once, and must call `unlock()` the same number of times. One `unlock()` call does not always release it for other threads.
- Calling `unlock()` on a thread that does not hold the lock throws `IllegalMonitorStateException`.
- Correct pattern: `if (lock.tryLock()) { try { /* work */ } finally { lock.unlock(); } }`. Calling `tryLock()` and then `unlock()` in a `finally` without checking the result is a bug, because `unlock()` throws when the lock was not obtained, and the protected code runs without the lock.

---

## Possible Outcomes for a Race Condition (Username Creation Example)

- Both users are able to create accounts with the username ZooFan.
- Neither user is able to create an account with the username ZooFan, and an error message is returned to both users.
- One user is able to create an account with the username ZooFan, while the other user receives an error message.

---

## Threading Problems
- **Deadlock**: two or more threads are blocked forever, each waiting for a resource held by the other. Example: one task locks `o1` then `o2`, while another locks `o2` then `o1`. The program may print nothing and hang, or it may run fine, depending on timing.
- **Starvation**: a single thread is perpetually denied access to a shared resource.
- **Livelock**: a form of starvation where two or more threads are active but conceptually blocked forever (they keep responding to each other but make no progress).
- **Race condition**: two or more threads try to complete a related task at the same time, resulting in invalid or unexpected data.
- Memory aid: livelock looks busy but makes no progress; deadlock is fully blocked; a race condition produces wrong data.

---

## Concurrent Collections
- Use concurrent classes such as `ConcurrentHashMap`, `ConcurrentLinkedQueue`, `ConcurrentSkipListMap`, `ConcurrentSkipListSet`, `CopyOnWriteArrayList`, and `CopyOnWriteArraySet` when several threads share a collection.
- Modifying an ordinary collection (such as an `ArrayList`) while iterating it throws a `ConcurrentModificationException`. The concurrent collections do not.
- `CopyOnWriteArrayList` copies its underlying array on every change. Its iterator works on a snapshot taken when the loop starts, so adding inside a for-each loop does not affect the current loop. Looping over a 3-element list and adding once per iteration results in 3 additions.
- `ConcurrentSkipListSet` and `ConcurrentSkipListMap` are sorted, and their iterators are weakly consistent. Adding a duplicate value to a set has no effect.
- `new CopyOnWriteArrayList<>(collection)` and `new ArrayList<>(collection)` copy the original, so changes to the copy do not affect the source collection.

---

## Synchronized Collections

### Table 13.13 — Synchronized Collections Methods (java.util.Collections)

- `synchronizedCollection(Collection<T> c)`
- `synchronizedList(List<T> list)`
- `synchronizedMap(Map<K,V> m)`
- `synchronizedNavigableMap(NavigableMap<K,V> m)`
- `synchronizedNavigableSet(NavigableSet<T> s)`
- `synchronizedSet(Set<T> s)`
- `synchronizedSortedMap(SortedMap<K,V> m)`
- `synchronizedSortedSet(SortedSet<T> s)`
- These wrap an existing collection so that each individual method call is synchronized. Iterating still needs external synchronization or a concurrent collection.

---

## CyclicBarrier
- `new CyclicBarrier(limit)` and `new CyclicBarrier(limit, Runnable barrierAction)` create a barrier. Each thread calls `await()`, which blocks until `limit` threads have called it. Then the optional barrier action runs once and the threads continue.
- If fewer than `limit` threads reach the barrier, the waiting threads block forever, and the barrier action never runs. For example, nine tasks awaiting on a barrier of ten waits forever.
- `await()` throws checked exceptions (`InterruptedException`, `BrokenBarrierException`), so wrap it in try/catch.

---

## Parallel Streams
- Ways to create a parallel stream: `collection.parallelStream()` on a `Collection`, and `stream.parallel()` on an existing stream (including primitive streams). A `List` has no `parallel()` method, and a stream has no `parallelStream()` method. There is no `ParallelStream` class.
- Calling `parallel()` on a stream that is already parallel is fine.
- In a parallel stream, the order in which elements are processed is unpredictable. `forEach()` and `peek()` print in an unpredictable order.
- `forEachOrdered()` processes elements in their original encounter order, even on a parallel stream (it can slow down the stream).
- `findFirst()` always returns the first element in encounter order. `findAny()` may return any element on a parallel stream, even after `sorted()`. Even on a sequential stream, `findAny()` is not guaranteed to return the first element.
- Shared variables (such as `count++` or `value[0]++`) updated from a parallel stream cause race conditions. Use atomic classes or avoid shared state.
- Code inside a parallel stream should be stateless and free of side effects.
- Three-argument `reduce(identity, accumulator, combiner)` must have an accumulator that fits the identity's type. For `reduce(0, (c1, c2) -> c1.length() + c2.length(), (s1, s2) -> s1 + s2)` on a stream of `String`, `c1` is an `Integer`, which has no `length()` method, so it does not compile. The accumulator should add an Integer and a String, such as `(total, s) -> total + s.length()`.
- The combiner and accumulator must be associative, stateless, and compatible with the identity for `reduce()` to behave predictably in parallel.

---

## Requirements for Parallel Reduction with `collect()`

- The stream is parallel.
- The parameter of the `collect()` operation has the `Characteristics.CONCURRENT` characteristic.
- Either the stream is unordered or the collector has the characteristic `Characteristics.UNORDERED`.
- `Collectors.groupingByConcurrent()` and `Collectors.toConcurrentMap()` are the concurrent versions of `groupingBy()` and `toMap()`. They return a `ConcurrentMap`, which works the same way as a `Map` for calls like `get(true)`.
- Streams can be combined before collecting, for example `Stream.of(cats, bears).flatMap(s -> s)` where each element is itself a stream.

---

## Exam Essentials

- **Identify the differences between platform threads and virtual threads.** Platform threads map to the underlying operating system threads. Virtual threads are lighter weight, using a carrier thread only when they need to run. A carrier thread runs on an operating system thread as well. Virtual threads can be run on `Executors.newVirtualThreadPerTaskExecutor()`. Since they are so lightweight, they don't need to be pooled.
- **Be able to write thread-safe code.** Thread-safety is about protecting shared data from concurrent access. A monitor can be used to ensure that only one thread processes a particular section of code at a time. In Java, monitors can be implemented with a synchronized block or method or using an instance of `Lock`. `ReentrantLock` has a number of advantages over using a synchronized block, including the ability to check whether a lock is available without blocking it, as well as supporting the fair acquisition of locks. To achieve synchronization, two or more threads must coordinate on the same shared object.
- **Be able to apply the atomic classes.** An atomic operation is one that occurs without interference from another thread. The Concurrency API includes a set of atomic classes that are similar to the primitive classes, except that they ensure that operations on them are performed atomically. Know the difference between an atomic variable and one marked with the `volatile` modifier.
- **Create concurrent tasks with a thread executor service using Runnable and Callable.** An `ExecutorService` creates and manages a single thread or a pool of threads. Instances of `Runnable` and `Callable` can both be submitted to a thread executor and will be completed using the available threads in the service. `Callable` differs from `Runnable` in that `Callable` returns a generic data type and can throw a checked exception. A `ScheduledExecutorService` can be used to schedule tasks at a fixed rate or with a fixed interval between executions. Know that `execute()` returns `void`, `submit()` returns a `Future`, and `close()`, `shutdown()`, and `awaitTermination()` differ in how long they wait.
- **Be able to use the concurrent collection classes.** The Concurrency API includes numerous collection classes that include built-in support for multithreaded processing, such as `ConcurrentHashMap`. It also includes a class `CopyOnWriteArrayList` that creates a copy of its underlying list structure every time it is modified and is useful in highly concurrent environments.
- **Identify potential threading problems.** Deadlock, starvation, and livelock are three threading problems that can occur and result in threads never completing their task. Deadlock occurs when two or more threads are blocked forever. Starvation occurs when a single thread is perpetually denied access to a shared resource. Livelock is a form of starvation where two or more threads are active but conceptually blocked forever. Finally, race conditions occur when two threads execute at the same time, resulting in an unexpected outcome.
- **Understand the impact of using parallel streams.** The Stream API allows for the easy creation of parallel streams. Using a parallel stream can cause unexpected results, since the order of operations may no longer be predictable. Some operations, such as `reduce()` and `collect()`, require special consideration to achieve optimal performance when applied to a parallel stream. Know which operations keep order (`forEachOrdered()`, `findFirst()`) and which do not (`forEach()`, `findAny()`, `peek()`).
- **Check for compile errors first, then runtime behavior.** Look for a `Runnable` lambda that returns a value, a method called on the wrong type (`ExecutorService` vs `ScheduledExecutorService`), a `void` result being stored, and checked exceptions that are not handled. Then decide whether the result is predictable, or whether the code can hang or throw an exception.

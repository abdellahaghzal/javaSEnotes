# Chapter 10: Streams

## Optional Basics
- An `Optional<T>` is a "box" that either holds a value or is empty — used to express "no data" instead of returning `null` or a misleading default (like 0 for an average).
- Created via factory methods only (no public constructor): `Optional.empty()`, `Optional.of(value)`, `Optional.ofNullable(value)`.
- `Optional.ofNullable()` is shorthand for the null-check ternary pattern.

### Optional Instance Methods
- `get()` — returns value if present, throws `NoSuchElementException` if empty.
- `isPresent()` — returns true/false.
- `ifPresent(Consumer c)` — runs the Consumer only if a value exists (an "if with no else").
- `orElse(T other)` — returns the value, or `other` if empty.
- `orElseGet(Supplier s)` — returns the value, or the Supplier's result if empty.
- `orElseThrow()` — returns the value, or throws `NoSuchElementException`.
- `orElseThrow(Supplier s)` — returns the value, or throws the exception created by the Supplier.

### Optional vs. null
- Optional makes "no value" explicit in the API, unlike null which is ambiguous.
- Optional supports functional-style code (`ifPresent()`, chaining) instead of if/else logic.

### Chaining Optionals
- `filter(Predicate p)` — keeps the value only if it matches, otherwise empty.
- `map(Function f)` — transforms the value if present.
- `flatMap(Function f)` — like map, but flattens a nested Optional (avoids `Optional<Optional<T>>`).

## Stream Pipeline Concepts
- A stream is a sequence of data; a **stream pipeline** is source → intermediate operations → terminal operation.
- **Source**: where the stream comes from (required).
- **Intermediate operations**: transform the stream into another stream; zero or more allowed; lazily evaluated (don't run until a terminal operation triggers them).
- **Terminal operation**: produces a result and consumes the stream; exactly one required; the stream is invalid afterward.
- Streams use **lazy evaluation** — data isn't generated until needed.
- Streams can be finite or infinite.
- A stream can be used only once — reusing a spent stream throws an exception.

### Intermediate vs. Terminal Operations (comparison)
- Required for a useful pipeline: Intermediate = No, Terminal = Yes.
- Can appear multiple times in a pipeline: Intermediate = Yes, Terminal = No.
- Return type is a stream type: Intermediate = Yes, Terminal = No.
- Executes on method call: Intermediate = No, Terminal = Yes.
- Stream still valid after the call: Intermediate = Yes, Terminal = No.

## Creating Stream Sources
- `Stream.empty()` — finite, zero elements.
- `Stream.of(varargs)` — finite, from listed elements.
- `collection.stream()` — finite, from a Collection.
- `collection.parallelStream()` — finite, can run in parallel.
- `Stream.generate(Supplier)` — infinite, calls Supplier repeatedly.
- `Stream.iterate(seed, UnaryOperator)` — infinite, seed then repeated function calls.
- `Stream.iterate(seed, Predicate, UnaryOperator)` — finite or infinite; stops when the Predicate returns false.
- Printing a stream reference shows an object hash, not its contents (unlike a Collection).

## Common Terminal Operations
- `count()` — number of elements; reduction; never terminates on an infinite stream.
- `min(Comparator)` / `max(Comparator)` — smallest/largest per comparator; reductions; return `Optional<T>`; never terminate on an infinite stream.
- `findAny()` / `findFirst()` — return `Optional<T>`; terminate even on infinite streams; not reductions (don't need to see every element).
- `allMatch()` / `anyMatch()` / `noneMatch()` — return boolean; not reductions; may or may not terminate on infinite streams depending on the data.
- `forEach(Consumer)` — void return; not a reduction; never terminates on an infinite stream; can't be used with a for-each loop syntax on the stream itself (Stream doesn't implement Iterable).
- `reduce()` — combines all elements into one value; is a reduction; three overloads:
  - `reduce(identity, accumulator)` — returns T directly.
  - `reduce(accumulator)` — returns `Optional<T>` (empty if stream empty, single element if only one, else combined).
  - `reduce(identity, accumulator, combiner)` — for differing types, useful for parallel streams.
- `collect()` — a "mutable reduction" (more efficient, reuses one mutable container); two overloads:
  - `collect(supplier, accumulator, combiner)` — manual collector logic.
  - `collect(Collector)` — uses a prebuilt `Collectors` class collector.

## Common Intermediate Operations
- `filter(Predicate)` — keeps elements matching the predicate.
- `distinct()` — removes duplicates (uses `equals()`), not required to be adjacent.
- `limit(long maxSize)` — restricts stream to at most that many elements; can turn infinite into finite.
- `skip(long n)` — skips the first n elements.
- `map(Function)` — one-to-one transformation of elements.
- `flatMap(Function)` — flattens nested streams (e.g., streams of lists) into one level, removing empty containers.
- `Stream.concat(s1, s2)` — joins two streams together.
- `sorted()` / `sorted(Comparator)` — sorts using natural order or a given Comparator.
- `peek(Consumer)` — inspects each element without altering the stream; useful for debugging; should avoid side effects that mutate shared state.

## Pipeline Execution Behavior
- Operations run element-by-element in "assembly line" fashion, not stage-by-stage across the whole dataset (except operations that must see everything, like `sorted()`).
- `limit()` can short-circuit the entire pipeline once satisfied, avoiding unnecessary work upstream.
- Order of intermediate operations matters — e.g., `filter → sorted → limit` vs `filter → limit → sorted` can produce different results or hang forever with infinite streams.
- Chained pipelines: a `.collect()` followed by `.stream()` on the result starts a second, independent pipeline.

## Primitive Streams
- Three primitive stream types: `IntStream` (int, short, byte, char), `LongStream` (long), `DoubleStream` (double, float).
- Provide extra numeric methods beyond generic `Stream<T>`.
- Case matters: `Stream` (capital S) is the generic interface; lowercase "stream" is a general concept covering all four types.

### Primitive Stream Methods (highlights)
- `average()` — returns `OptionalDouble` (all three primitive types).
- `boxed()` — converts to `Stream<T>` of the wrapper type.
- `max()` / `min()` — return `OptionalInt`, `OptionalLong`, or `OptionalDouble` depending on stream type.
- `range(a, b)` — exclusive of b (IntStream/LongStream only).
- `rangeClosed(a, b)` — inclusive of b (IntStream/LongStream only).
- `sum()` — returns a primitive (int/long/double), not an Optional; empty stream sums to 0.
- `summaryStatistics()` — returns an `IntSummaryStatistics`, `LongSummaryStatistics`, or `DoubleSummaryStatistics` object.

### Mapping Between Stream Types
- Same-type mapping method is always called `map()`.
- Mapping to a `Stream<T>` (object stream) uses `mapToObj()`.
- Otherwise, the method name includes the target primitive type (`mapToInt()`, `mapToDouble()`, `mapToLong()`).
- `flatMapToInt()`, `flatMapToDouble()`, `flatMapToLong()` — primitive equivalents of `flatMap()`.

### Primitive Optional Differences
- `OptionalDouble`, `OptionalInt`, `OptionalLong` are distinct from `Optional<Double>` etc.
- Retrieval methods are `getAsDouble()`, `getAsInt()`, `getAsLong()` instead of `get()`.
- `orElseGet()` takes a primitive-specific supplier (`DoubleSupplier`, `IntSupplier`, `LongSupplier`).

### Summary Statistics Methods
- `getCount()` — number of values (long).
- `getAverage()` — average as double (0.0 if empty).
- `getSum()` — sum (double for DoubleSummaryStatistics; long for Int/LongSummaryStatistics).
- `getMin()` — smallest value (largest possible value if empty).
- `getMax()` — largest value (smallest possible value if empty).

## Advanced Stream Concepts
- Streams are linked lazily to their source data — changes to a backing collection made *before* the terminal operation runs are reflected in the stream's results.
- `Spliterator` lets you manually split a stream/collection's data for parallel-style processing:
  - `trySplit()` — removes roughly half the remaining data into a new Spliterator; returns null when no longer splittable.
  - `tryAdvance(Consumer)` — processes a single remaining element; returns whether one was processed.
  - `forEachRemaining(Consumer)` — processes everything left.
  - On infinite streams, `trySplit()` doesn't try to take "half" — it takes a large chunk instead.

## Collectors (Collecting Results)
- Collectors are static factory methods on the `Collectors` class, passed into `collect()`.
- Basic collectors: `joining()`, `averagingInt/Long/Double()`, `summingInt/Long/Double()`, `counting()`, `minBy()`/`maxBy()`, `toList()`, `toSet()`, `toCollection(Supplier)`.
- `Collectors.toList()` returns a mutable list; the newer `stream.toList()` shortcut returns an **immutable** list.
- `toMap(keyFunction, valueFunction)` — builds a Map; throws `IllegalStateException` on duplicate keys unless a merge function is supplied.
- `toMap()` optional overloads add a merge `BinaryOperator` and/or a Map-type `Supplier` (e.g., `TreeMap::new`).
- `groupingBy(Function)` — groups elements into a `Map<K, List<T>>` keyed by the function's result; keys cannot be null.
  - Optional downstream collector changes the value type (e.g., `Collectors.toSet()`, `Collectors.counting()`, `Collectors.mapping()`).
  - Optional Map-type supplier changes the returned Map implementation (e.g., `TreeMap::new`).
- `partitioningBy(Predicate)` — special case of grouping with exactly two keys, `true` and `false`, always present even if one group is empty.
  - Map type cannot be customized (only two keys, so it doesn't matter).
  - Can also take a downstream collector to change the value type.
- `mapping(Function, Collector)` — adds another transformation level within a downstream collector.
- `teeing(Collector1, Collector2, BiFunction)` — combines results of two collectors into a single object in one pass.

## Exam Essentials (Key Takeaways)
- Know how to create and use `Optional` (`empty()`, `of()`, `isPresent()`, `get()`, `ifPresent()`, `orElseGet()`, etc.).
- Know that intermediate operations don't execute until a terminal operation runs; without a terminal operation, nothing executes.
- Know which terminal operations are reductions: `collect()`, `count()`, `max()`, `min()`, `reduce()`.
- Know the core intermediate operations: `filter()`, `map()`, `flatMap()`, and their behavior.
- Know the three primitive stream types and their unique numeric methods (`average()`, `sum()`, etc.) and matching Optional types (`OptionalDouble`, `OptionalInt`, `OptionalLong`).
- Know the correct mapping method names between stream types (`mapToObj()`, `mapToDouble()`, `mapToInt()`, `mapToLong()`).
- Know that `peek()` is for inspecting/debugging a stream without altering its result.
- Know the search/matching operations (`findFirst()`, `findAny()`, `anyMatch()`, `allMatch()`, `noneMatch()`) and their infinite-stream hang risks.
- Know `sorted()` in both its no-argument and Comparator-argument forms.
- Know the difference between `groupingBy()` (arbitrary keys, Collection values) and `partitioningBy()` (always exactly `true`/`false` keys, even if empty).

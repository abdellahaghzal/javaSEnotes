# Chapter 9: Collections and Generics

## Four Main Collections Framework Interfaces
- **List**: An ordered collection of elements that allows duplicate entries. Elements can be accessed by an int index.
- **Set**: A collection that does not allow duplicate entries.
- **Queue**: A collection that orders its elements in a specific order for processing. A Deque is a subinterface of Queue that allows access at both ends.
- **Map**: A collection that maps keys to values, with no duplicate keys allowed. The elements in a map are key/value pairs.
- A Map is **not** a `Collection` and does not extend it. It has no `contains()` method and no `iterator()`. Use `containsKey()` or `containsValue()` instead.
- Need duplicates allowed, such as a list of products for sale? Pick a List (`ArrayList` or `LinkedList`).
- Need sorted lookup by an ID (retrieve a value by key, in sorted key order)? Pick a `TreeMap`.
- There is no class named `SequencedTreeSet` or `TreeSetMap`. Any answer choice using such a name is a distractor.

## Methods Common to All Collections
- `boolean add(E element)` — Adds an element. Available on all Collection APIs.
- `boolean contains(Object o)` — Returns whether the element is in the collection.
- `boolean remove(Object o)` — Removes the first matching element and returns whether one was removed.
- `boolean removeIf(Predicate<? super E> filter)` — Removes all elements that match the predicate. Returns true if anything was removed.
- `void forEach(Consumer<? super E> action)` — Loops through each element. A `List`, `Set`, or `Queue` can use `forEach(System.out::println)`.
- A `Map` is not a Collection. Its `forEach()` takes a `BiConsumer` (two parameters), so `map.forEach(System.out::println)` does not compile. Use `map.keySet().forEach(...)` or `map.values().forEach(...)` instead.
- `Collections.unmodifiableCollection(c)` returns a read-only **view** of the original. Changes made to the original collection are visible through the view.

## Naming Conventions for Generics
- **E** for an element
- **K** for a map key
- **V** for a map value
- **N** for a number
- **T** for a generic data type
- **S, U, V,** and so forth for multiple generic types

## Table 9.1 — Factory Methods to Create a List
- `Arrays.asList(varargs)` — Returns fixed size list backed by an array. Can add elements? No. Can replace elements? Yes. Can delete elements? No.
- `List.of(varargs)` — Returns immutable list. Can add elements? No. Can replace elements? No. Can delete elements? No.
- `List.copyOf(collection)` — Returns immutable list with copy of original collection's values. Can add elements? No. Can replace elements? No. Can delete elements? No.
- Calling `add()`, `remove()`, `removeIf()`, `replaceAll()`, `set()`, or `sort()` on an immutable list compiles but throws `UnsupportedOperationException` at runtime. This is true even if `removeIf()` would not remove anything.
- Calling `add()` or `remove()` on an `Arrays.asList()` list also throws `UnsupportedOperationException`. `set()`, `replaceAll()`, `sort()`, and `Collections.reverse()` work because they only replace elements.
- `Set.of()`, `Set.copyOf()`, `Map.of()`, and `Map.copyOf()` are also immutable.
- To get a mutable copy of an immutable list, pass it to a constructor: `new ArrayList<>(immutableList)`.
- `Map.of(k1, v1, k2, v2)` takes alternating keys and values. Its entries can be put in a list with `List.copyOf(map.entrySet())`, and that list's elements are `Map.Entry` objects, not the values.

## Table 9.2 — List Methods
- `boolean add(E element)` — Adds element to end (available on all Collection APIs).
- `void add(int index, E element)` — Adds element at index and moves the rest toward the end.
- `E get(int index)` — Returns element at index.
- `int indexOf(Object o)` — Returns the index of the first matching element or -1 if not found.
- `int lastIndexOf(Object o)` — Returns the index of the last matching element or -1 if not found.
- `E remove(int index)` — Removes element at index and moves the rest toward the front.
- `default void replaceAll(UnaryOperator<E> op)` — Replaces each element in list with the result of operator.
- `E set(int index, E e)` — Replaces element at index and returns original. Throws IndexOutOfBoundsException if index is invalid.
- `default void sort(Comparator<? super E> c)` — Sorts list.
- `remove(int index)` and `remove(Object o)` are overloaded. With a `List<Integer>`, `remove(1)` removes the element at **index** 1, while `remove(Integer.valueOf(1))` removes the **value** 1.
- With a reference type of `Queue<Integer>` or `Collection<Integer>`, `remove(1)` can only call `remove(Object)`, which removes the value 1 (autoboxed) and does nothing if the value is absent.
- `get(int index)` exists only on `List`. It is not available through a `SequencedCollection`, `Set`, `Queue`, or `Deque` reference type, even if the actual object is a `LinkedList`.
- The reference type, not the object type, determines which methods can be called.

## Table 9.3 — Queue Methods
- Add to back: `boolean add(E e)`, `boolean offer(E e)`
- Read from front: `E element()`, `E peek()`
- Get and remove from front: `E remove()`, `E poll()`
- `add()`, `element()`, and `remove()` throw an exception on failure (such as an empty queue). `offer()`, `peek()`, and `poll()` return false or null instead.

## Table 9.4 — Deque Methods
- Add to front: `void addFirst(E e)`, `boolean offerFirst(E e)`
- Add to back: `void addLast(E e)`, `public boolean offerLast(E e)`
- Read from front: `E getFirst()`, `E peekFirst()`
- Read from back: `E getLast()`, `E peekLast()`
- Get and remove from front: `E removeFirst()`, `E pollFirst()`
- Get and remove from back: `E removeLast()`, `E pollLast()`
- Tracing tip: write out the deque after every call. `offerLast("hello")`, `offerLast("hi")`, then `offerFirst("ola")` gives `[ola, hello, hi]`.

## Table 9.5 — Using a Deque as a Stack
- Add to the front/top: `void push(E e)`
- Remove from the front/top: `E pop()`
- Get first element: `E peek()`
- `push()`, `pop()`, and `peek()` all work on the **front** of the deque, the same end as `offerFirst()`.
- `pop()` on an empty deque throws `NoSuchElementException`, while `peek()` returns null.
- A `while (deque.peek() != null)` loop is a common way to drain a deque.

## Table 9.6 — Map Methods
- `void clear()` — Removes all keys and values from map.
- `boolean containsKey(Object key)` — Returns whether key is in map.
- `boolean containsValue(Object value)` — Returns whether value is in map.
- `Set<Map.Entry<K,V>> entrySet()` — Returns Set of key/value pairs.
- `void forEach(BiConsumer<K, V> action)` — Loops through each key/value pair.
- `V get(Object key)` — Returns value mapped by key or null if none is mapped.
- `V getOrDefault(Object key, V defaultValue)` — Returns value mapped by key or default value if none is mapped.
- `boolean isEmpty()` — Returns whether map is empty.
- `Set<K> keySet()` — Returns set of all keys.
- `V merge(K key, V value, BiFunction<V, V, V> func)` — Sets value if key not set. Runs function if key is set, to determine new value. Removes if value is null.
- `V put(K key, V value)` — Adds or replaces key/value pair. Returns previous value or null.
- `V putIfAbsent(K key, V value)` — Adds value if key not present and returns null. Otherwise, returns existing value.
- `V remove(Object key)` — Removes and returns value mapped to key. Returns null if none.
- `V replace(K key, V value)` — Replaces value for given key if key is set. Returns original value or null if none.
- `void replaceAll(BiFunction<K, V, V> func)` — Replaces each value with results of function.
- `int size()` — Returns number of entries (key/value pairs) in map.
- `Collection<V> values()` — Returns Collection of all values.
- There is no `contains()`, `keys()`, or `valueSet()` method on a `Map`. Those names are distractors.

## Table 9.7 — Behavior of the merge() Method
- Key has a null value in map, mapping function N/A (not called) → Update key's value in map with value parameter.
- Key has a non-null value in map, mapping function returns null → Remove key from map.
- Key has a non-null value in map, mapping function returns a non-null value → Set value to mapping function result.
- Key is not in map, mapping function N/A (not called) → Add key with value parameter to map directly without calling mapping function.
- A key that is mapped to `null` is treated the same as a missing key, so the function is never called and the key ends up with the new value.
- Example: with `{1=10, 2=20, 3=null}`, `merge(1, 3, (a,b) -> a + b)` sets key 1 to 13, and `merge(3, 3, (a,b) -> a + b)` sets key 3 to 3, giving `{1=13, 2=20, 3=3}`.

## Comparable and Comparator Return Value Rules
- The number 0 is returned when the current object is equivalent to the argument to compareTo().
- A negative number (less than 0) is returned when the current object is smaller than the argument to compareTo().
- A positive number (greater than 0) is returned when the current object is larger than the argument to compareTo().
- String ordering is by Unicode value: digits come before uppercase letters, which come before lowercase letters (`"123" < "Abb" < "aab"`).
- Calling `toLowerCase()` before comparing makes the comparison case-insensitive, so `"Abb"` and `"abb"` are equal.
- To reverse a Comparator, swap the operands (`b.compareTo(a)`) or write `o2 - o1`.

## Table 9.8 — Comparison of Comparable and Comparator
- Package name: Comparable = `java.lang`; Comparator = `java.util`
- Interface must be implemented by class comparing?: Comparable = Yes; Comparator = No
- Method name in interface: Comparable = `compareTo()`; Comparator = `compare()`
- Number of parameters: Comparable = 1; Comparator = 2
- Common to declare using a lambda: Comparable = No; Comparator = Yes
- A single class can implement both Comparable and Comparator, but it is the way it is passed that decides which one is used.
- `new TreeSet<>()` and `new TreeMap<>()` use the natural ordering (`compareTo()`).
- `new TreeSet<>(comparator)` and `new TreeMap<>(comparator)` use the Comparator (`compare()`) instead.
- `TreeSet` also has constructors that accept a `Collection` or a `SortedSet`. The compiler picks the constructor from the argument's type.
- `TreeSet` and `TreeMap` throw a `ClassCastException` at runtime if the elements are not Comparable and no Comparator is provided.

## Table 9.9 — Helper Static Methods for Building a Comparator
- `comparing(function)` — Compare by results of function that returns any Object (or primitive autoboxed into Object).
- `comparingDouble(function)` — Compare by results of function that returns double.
- `comparingInt(function)` — Compare by results of function that returns int.
- `comparingLong(function)` — Compare by results of function that returns long.
- `naturalOrder()` — Sort using order specified by the Comparable implementation on the object itself.
- `reverseOrder()` — Sort using reverse of order specified by Comparable implementation on the object itself.

## Table 9.10 — Helper Default Methods for Building a Comparator
- `reversed()` — Reverse order of chained Comparator.
- `thenComparing(function)` — If previous Comparator returns 0, use this comparator function that returns Object or can be autoboxed into one. Otherwise, return result from previous Comparator.
- `thenComparingDouble(function)` — If previous Comparator returns 0, use this comparator function that returns double. Otherwise, return result from previous Comparator.
- `thenComparingInt(function)` — If previous Comparator returns 0, use this comparator function that returns int. Otherwise, return result from previous Comparator.
- `thenComparingLong(function)` — If previous Comparator returns 0, use this comparator function that returns long. Otherwise, return result from previous Comparator.
- `reversed()` reverses the **entire chain built so far**. `comparing(A).thenComparingInt(B).reversed()` reverses both A and B.
- To reverse only the second comparison, reverse it inside: `comparing(A).thenComparing(Comparator.comparing(B).reversed())`.
- There is no `thenComparingNumber()` method. Only `thenComparing`, `thenComparingInt`, `thenComparingLong`, and `thenComparingDouble` exist.

## Collections Utility Methods
- `Collections.sort(list)` — Sorts using natural order. The elements must be Comparable.
- `Collections.sort(list, comparator)` — Sorts using the given Comparator.
- `Collections.reverse(list)` — Reverses the current order of the list. It does not sort. Calling it twice restores the original order.
- `Collections.binarySearch(list, key)` — Searches a list that is sorted in **ascending natural order**. Returns the index if found, or `-(insertion point) - 1` if not found.
- `Collections.binarySearch(list, key, comparator)` — Searches a list sorted with that Comparator.
- If the list is not sorted in the order binarySearch expects (for example, sorted in descending order but searched without a Comparator), the result is undefined.
- `Arrays.asList()` returns a fixed-size list, so `Collections.sort()` and `Collections.reverse()` work on it.
- `Arrays.sort(array, comparator)` sorts an array using a Comparator. The array must hold objects, not primitives.

## Sequenced Collections (New to Java 21)
- `SequencedCollection`
- `SequencedSet`
- `SequencedMap`
- A sequenced type has a defined encounter order, so it has a first and last element and can be reversed.
- `HashSet` and `HashMap` are not sequenced.

## Table 9.11 — SequencedCollection Methods
- `addFirst(E e)` — Adds element as the first element in the collection.
- `addLast(E e)` — Adds element as the last element in the collection.
- `getFirst()` — Retrieves the first element in the collection.
- `getLast()` — Retrieves the last element in the collection.
- `removeFirst()` — Removes the first element in the collection.
- `removeLast()` — Removes the last element in the collection.
- `reversed()` — Returns a reverse-ordered view of the collection.
- `SequencedCollection` does **not** have `get(int index)`. That method exists only on `List`.
- `TreeSet` (and other sorted collections) decide their own order, so calling `addFirst()` or `addLast()` on one compiles but throws `UnsupportedOperationException` at runtime. Reading and removing (`getFirst()`, `removeLast()`, and so on) work fine.
- `new LinkedList<>()`, `new ArrayList<>()`, `new ArrayDeque<>()`, and `new LinkedHashSet<>()` can all be assigned to a `SequencedCollection` reference. `HashSet` and `HashMap` cannot.

## Table 9.12 — Common SequencedMap Methods
- `firstEntry()` — Retrieves the first key/value pair in the map.
- `lastEntry()` — Retrieves the last key/value pair in the map.
- `pollFirstEntry()` — Removes and retrieves the first key/value pair in the map.
- `pollLastEntry()` — Removes and retrieves the last key/value pair in the map.
- `putFirst(K k, V v)` — Adds the key/value pair as the first element in the map.
- `putLast(K k, V v)` — Adds the key/value pair as the last element in the map.
- `reversed()` — Returns a reverse-ordered view of the map.
- `firstEntry()` and `lastEntry()` only read. `pollFirstEntry()` and `pollLastEntry()` also remove the entry.
- On a `TreeMap`, `putFirst()` and `putLast()` throw `UnsupportedOperationException` at runtime, just like `addFirst()` and `addLast()` on a `TreeSet`.
- Tracing tip: keep a running copy of the map after every `poll`. For a `TreeMap`, "first" means the smallest key.

## Table 9.13 — Java Collections Framework Types
- **List**: Can contain duplicate elements? Yes. Elements always ordered? Yes (by index). Has keys and values? No. Must add/remove in specific order? No.
- **Queue**: Can contain duplicate elements? Yes. Elements always ordered? Yes (retrieved in defined order). Has keys and values? No. Must add/remove in specific order? Yes.
- **Set**: Can contain duplicate elements? No. Elements always ordered? No. Has keys and values? No. Must add/remove in specific order? No.
- **Map**: Can contain duplicate elements? Yes (for values). Elements always ordered? No. Has keys and values? Yes. Must add/remove in specific order? No.

## Table 9.14 — Collection Classes
- **ArrayDeque**: Interfaces = Deque, SequencedCollection. Ordered? Yes. Sorted? No. Calls hashCode? No. Calls compareTo? No.
- **ArrayList**: Interfaces = List, SequencedCollection. Ordered? Yes. Sorted? No. Calls hashCode? No. Calls compareTo? No.
- **HashMap**: Interfaces = Map. Ordered? No. Sorted? No. Calls hashCode? Yes. Calls compareTo? No.
- **HashSet**: Interfaces = Set. Ordered? No. Sorted? No. Calls hashCode? Yes. Calls compareTo? No.
- **LinkedList**: Interfaces = Deque, List, SequencedCollection. Ordered? Yes. Sorted? No. Calls hashCode? No. Calls compareTo? No.
- **LinkedHashSet**: Interfaces = Set, SequencedSet. Ordered? Yes. Sorted? No. Calls hashCode? Yes. Calls compareTo? No.
- **LinkedHashMap**: Interfaces = Map, SequencedMap. Ordered? Yes. Sorted? No. Calls hashCode? Yes. Calls compareTo? No.
- **TreeMap**: Interfaces = Map, SequencedMap. Ordered? Yes. Sorted? Yes. Calls hashCode? No. Calls compareTo? Yes.
- **TreeSet**: Interfaces = Set, SequencedCollection, SequencedSet. Ordered? Yes. Sorted? Yes. Calls hashCode? No. Calls compareTo? Yes.
- "Ordered" means a predictable encounter order (insertion order or sorted order). "Sorted" means the order is determined by comparing elements.
- `LinkedList` is the only common class that is both a `List` and a `Deque`.
- A `TreeSet` does not allow null elements, and a `TreeMap` does not allow null keys.

## Older Collections (Real World Scenario)
- **Vector**: Implements List
- **Hashtable**: Implements Map
- **Stack**: Implements List
- These older classes are synchronized and are rarely used in new code. Prefer `ArrayList`, `HashMap`, and `ArrayDeque` (as a stack).

## Generic Classes, Methods, and Raw Types
- A generic class declares its type parameter after the class name: `class Box<T> { }`. A record can do the same: `record Hello<T>(T t) { }`.
- A generic method declares its type parameter **before the return type**: `public static <T> T identity(T t)`. Without `<T>` there, `T` is treated as a class name and the code does not compile (unless a class named `T` exists).
- Modifiers come first, then the type parameter, then the return type: `public static <T> T identity(T t)`.
- A method can declare its own type parameter with the same name as the class's (`<T>`). The method's `T` **shadows** the class's `T` and is a separate type.
- Likewise, in `class Z<Y>`, the `Y` is a type parameter that shadows any existing class named `Y`. Writing `new Y()` or `Y y = new W()` inside `Z` then fails to compile.
- When calling a generic method, a type can be passed explicitly before the method name: `Helper.<String>printIt("a")`. This syntax requires the class or `this` before the dot for static and instance calls.
- A bounded type parameter such as `<U extends Exception>` only accepts `Exception` or its subclasses. `<Throwable>` or a `Throwable` argument is not allowed for that method.
- A raw type (a generic class used without a type, such as `Map m = new HashMap();`) compiles with a warning. Type safety is lost and elements are treated as `Object`.
- With a raw `Map`, `put(123, "456")` and `put("abc", "def")` both compile, but a method that does not exist on `Map`, such as `contains()`, is still a compile error.
- A record's canonical constructor may be written out in full (`public Hello(T t) { this.t = t; }`) or in compact form (no parameter list).

## Generics and Inheritance
- Generics are **not covariant**. `List<Object> list = new ArrayList<String>();` does not compile, and neither does `HashSet<Number> hs = new HashSet<Integer>();`. The type arguments must match exactly (unless a wildcard is used).
- The left side must be a supertype of the right side for the *raw* type: `List<String> a = new ArrayList<String>();` compiles, but `List<String> b = new HashSet<String>();` does not, because `HashSet` is not a `List`.
- The diamond operator `<>` is only allowed on the right side of an assignment (`new ArrayList<>()`). `List<> list = ...` does not compile.
- You cannot instantiate a class using a wildcard: `new ArrayList<? extends Object>()` and `new ArrayList<?>()` do not compile. Wildcards are only for variable and parameter types.
- Overriding: the method parameters must match **exactly**, including generics. `List<String>` is not the same as `List<CharSequence>`.
- Two methods with the same name and the same *erased* parameters (`List<String>` vs `List<Integer>`) cannot coexist. This is a compile error caused by type erasure, not an overload.
- A return type must be the same as, or a covariant subtype of, the overridden return type. `ArrayList<String>` is valid for `List<String>`. `Object` and `List<CharSequence>` are not valid for `List<String>`.

## Table 9.15 — Types of Bounds
- **Unbounded wildcard**: Syntax `?` — Example: `List<?> a = new ArrayList<String>();`
- **Wildcard with upper bound**: Syntax `? extends type` — Example: `List<? extends Exception> a = new ArrayList<RuntimeException>();`
- **Wildcard with lower bound**: Syntax `? super type` — Example: `List<? super Exception> a = new ArrayList<Object>();`
- With `List<?>` or `List<? extends X>`, you can **read** elements (as `Object` or `X`) but cannot add anything except null. You also cannot call methods that need a specific type on the elements, so `list.removeIf(String::isEmpty)` and `s.length()` do not compile with `List<?>`.
- With `List<? super X>`, you can add `X` (and subtypes) but reading only gives you `Object`.
- A `List<?>` variable can refer to any list, including `List.of("a", "b")`, but the elements are only known as `Object`.
- A wildcard variable can be passed to a method that takes `List<?>`, for example `ArrayList<? super Date>` or `ArrayList<? extends Number>`.
- Methods that only need the list size, such as `list.size()`, work with any `List<?>`.
- The type used with `var` is inferred from the right side. `var v = List.of("mouse", "parrot");` is a `List<String>`, so `String` methods can be used in lambdas.

## Table 9.16 — Why We Need a Lower Bound (addSound method)
- `List<?>` — Method compiles? No. Can pass a List\<String\>? Yes. Can pass a List\<Object\>? Yes.
- `List<? extends Object>` — Method compiles? No. Can pass a List\<String\>? Yes. Can pass a List\<Object\>? Yes.
- `List<Object>` — Method compiles? Yes. Can pass a List\<String\>? No (with generics, must pass exact match). Can pass a List\<Object\>? Yes.
- `List<? super String>` — Method compiles? Yes. Can pass a List\<String\>? Yes. Can pass a List\<Object\>? Yes.

## What You Can't Do with Generic Types (Real World Scenario)
- **Call a constructor**: Writing `new T()` is not allowed because at runtime, it would be `new Object()`.
- **Create an array of that generic type**: This one is the most annoying, but it makes sense because you'd be creating an array of Object values.
- **Call instanceof**: This is not allowed because at runtime `List<Integer>` and `List<String>` look the same to Java, thanks to type erasure.
- **Use a primitive type as a generic type parameter**: This isn't a big deal because you can use the wrapper class instead. If you want a type of int, just use Integer.
- **Create a static variable as a generic type parameter**: This is not allowed because the type is linked to the instance of the class.
- **Catch an exception of type T**: Even if T extends Exception, it cannot be used in a catch block since the precise type is not known.

## Chapter Summary — Key Collection Types
- **List**: An ordered collection of elements that allows duplicate entries.
  - **ArrayList**: Standard resizable list.
  - **LinkedList**: Can easily add/remove from beginning or end.
- **Set**: Does not allow duplicates.
  - **HashSet**: Uses hashCode() to find unordered elements.
  - **LinkedHashSet**: Well-defined encounter order.
  - **TreeSet**: Sorted. Does not allow null values.
- **Queue/Deque**: Orders elements for processing.
  - **ArrayDeque**: Double-ended queue.
  - **LinkedList**: Double-ended queue and list.
- **Map**: Maps unique keys to values.
  - **HashMap**: Uses hashCode() to find keys.
  - **LinkedHashMap**: Well-defined encounter order.
  - **TreeMap**: Sorted map. Does not allow null keys.

## Java 21 Sequenced Collections Summary
- **SequencedCollection**: ArrayDeque, ArrayList, LinkedList, LinkedHashSet, and TreeSet.
- **SequencedSet**: LinkedHashSet and TreeSet.
- **SequencedMap**: LinkedHashMap and TreeMap.

## Exam Essentials
- **Pick the correct type of collection from a description.** A List allows duplicates and orders the elements. A Set does not allow duplicates. A Deque orders its elements to facilitate retrievals from the front or back. A Map maps keys to values. Be familiar with the differences in implementations of these interfaces.
- **Work with convenience methods.** The Collections Framework contains many methods such as contains(), forEach(), and removeIf() that you need to know for the exam. Know that immutable collections throw an `UnsupportedOperationException` at runtime when modified, and that `Map.forEach()` takes two parameters.
- **Understand how to use sequenced collections.** Other than HashSet and HashMap, most collection classes now implement a sequenced collection interface (SequencedCollection or SequencedMap or SequencedSet). This includes methods that support iterating over the collection in a predictable encounter order. Sorted types such as TreeSet and TreeMap throw an exception if you try to add or put at a specific end.
- **Differentiate between Comparable and Comparator.** Classes that implement Comparable are said to have a natural ordering and implement the compareTo() method. A class is allowed to have only one natural ordering. A Comparator takes two objects in the compare() method. Different ones can have different sort orders. A Comparator is often implemented using a lambda such as `(a, b) -> a.num - b.num`. Remember that `reversed()` flips everything chained before it.
- **Identify valid and invalid uses of generics and wildcards.** `<T>` represents a type parameter. Any name can be used, but a single uppercase letter is the convention. `<?>` is an unbounded wildcard. `<? extends X>` is an upper-bounded wildcard. `<? super X>` is a lower-bounded wildcard. Generic methods declare their type parameter before the return type, generics are not covariant, and wildcards cannot be instantiated.
- **Trace code carefully.** Write down the contents of the collection after each line, and watch for overloaded methods (`remove(int)` vs `remove(Object)`), reference-type limitations, and runtime exceptions that only occur after the code compiles.

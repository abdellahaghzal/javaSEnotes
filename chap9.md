# Chapter 9: Collections and Generics

## Four Main Collections Framework Interfaces
- **List**: A list is an ordered collection of elements that allows duplicate entries. Elements in a list can be accessed by an int index.
- **Set**: A set is a collection that does not allow duplicate entries.
- **Queue**: A queue is a collection that orders its elements in a specific order for processing. A Deque is a subinterface of Queue that allows access at both ends.
- **Map**: A map is a collection that maps keys to values, with no duplicate keys allowed. The elements in a map are key/value pairs.

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

## Table 9.3 — Queue Methods
- Add to back: `boolean add(E e)`, `boolean offer(E e)`
- Read from front: `E element()`, `E peek()`
- Get and remove from front: `E remove()`, `E poll()`

## Table 9.4 — Deque Methods
- Add to front: `void addFirst(E e)`, `boolean offerFirst(E e)`
- Add to back: `void addLast(E e)`, `public boolean offerLast(E e)`
- Read from front: `E getFirst()`, `E peekFirst()`
- Read from back: `E getLast()`, `E peekLast()`
- Get and remove from front: `E removeFirst()`, `E pollFirst()`
- Get and remove from back: `E removeLast()`, `E pollLast()`

## Table 9.5 — Using a Deque as a Stack
- Add to the front/top: `void push(E e)`
- Remove from the front/top: `E pop()`
- Get first element: `E peek()`

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

## Table 9.7 — Behavior of the merge() Method
- Key has a null value in map, mapping function N/A (not called) → Update key's value in map with value parameter.
- Key has a non-null value in map, mapping function returns null → Remove key from map.
- Key has a non-null value in map, mapping function returns a non-null value → Set value to mapping function result.
- Key is not in map, mapping function N/A (not called) → Add key with value parameter to map directly without calling mapping function.

## Comparable vs. Comparable Return Value Rules
- The number 0 is returned when the current object is equivalent to the argument to compareTo().
- A negative number (less than 0) is returned when the current object is smaller than the argument to compareTo().
- A positive number (greater than 0) is returned when the current object is larger than the argument to compareTo().

## Table 9.8 — Comparison of Comparable and Comparator
- Package name: Comparable = `java.lang`; Comparator = `java.util`
- Interface must be implemented by class comparing?: Comparable = Yes; Comparator = No
- Method name in interface: Comparable = `compareTo()`; Comparator = `compare()`
- Number of parameters: Comparable = 1; Comparator = 2
- Common to declare using a lambda: Comparable = No; Comparator = Yes

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

## Sequenced Collections (New to Java 21)
- `SequencedCollection`
- `SequencedSet`
- `SequencedMap`

## Table 9.11 — SequencedCollection Methods
- `addFirst(E e)` — Adds element as the first element in the collection.
- `addLast(E e)` — Adds element as the last element in the collection.
- `getFirst()` — Retrieves the first element in the collection.
- `getLast()` — Retrieves the last element in the collection.
- `removeFirst()` — Removes the first element in the collection.
- `removeLast()` — Removes the last element in the collection.
- `reversed()` — Returns a reverse-ordered view of the collection.

## Table 9.12 — Common SequencedMap Methods
- `firstEntry()` — Retrieves the first key/value pair in the map.
- `lastEntry()` — Retrieves the last key/value pair in the map.
- `pollFirstEntry()` — Removes and retrieves the first key/value pair in the map.
- `pollLastEntry()` — Removes and retrieves the last key/value pair in the map.
- `putFirst(K k, V v)` — Adds the key/value pair as the first element in the map.
- `putLast(K k, V v)` — Adds the key/value pair as the last element in the map.
- `reversed()` — Returns a reverse-ordered view of the map.

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
- **LinkedHashSet**: Interfaces = Set, SequencedSet. Ordered? Yes. Sorted? No. Calls hashCode? No. Calls compareTo? No.
- **LinkedHashMap**: Interfaces = Map, SequencedMap. Ordered? Yes. Sorted? No. Calls hashCode? No. Calls compareTo? No.
- **TreeMap**: Interfaces = Map, SequencedMap. Ordered? Yes. Sorted? Yes. Calls hashCode? No. Calls compareTo? Yes.
- **TreeSet**: Interfaces = Set, SequencedCollection, SequencedSet. Ordered? Yes. Sorted? Yes. Calls hashCode? No. Calls compareTo? Yes.

## Older Collections (Real World Scenario)
- **Vector**: Implements List
- **Hashtable**: Implements Map
- **Stack**: Implements List

## Table 9.15 — Types of Bounds
- **Unbounded wildcard**: Syntax `?` — Example: `List<?> a = new ArrayList<String>();`
- **Wildcard with upper bound**: Syntax `? extends type` — Example: `List<? extends Exception> a = new ArrayList<RuntimeException>();`
- **Wildcard with lower bound**: Syntax `? super type` — Example: `List<? super Exception> a = new ArrayList<Object>();`

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
- **Work with convenience methods.** The Collections Framework contains many methods such as contains(), forEach(), and removeIf() that you need to know for the exam.
- **Understand how to use sequenced collections.** Other than HashSet and HashMap, most collection classes now implement a sequenced collection interface (SequencedCollection or SequencedMap or SequencedSet). This includes methods that support iterating over the collection in a predictable encounter order.
- **Differentiate between Comparable and Comparator.** Classes that implement Comparable are said to have a natural ordering and implement the compareTo() method. A class is allowed to have only one natural ordering. A Comparator takes two objects in the compare() method. Different ones can have different sort orders. A Comparator is often implemented using a lambda such as `(a, b) -> a.num - b.num`.
- **Identify valid and invalid uses of generics and wildcards.** `<T>` represents a type parameter. Any name can be used, but a single uppercase letter is the convention. `<?>` is an unbounded wildcard. `<? extends X>` is an upper-bounded wildcard. `<? super X>` is a lower-bounded wildcard.

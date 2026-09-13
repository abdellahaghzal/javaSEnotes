# Chapter 4: Core APIs

## Strings

- Strings are immutable; every "modifying" method returns a new `String` rather than changing the original.
- String literals don't require `new`; `String s = "text"` and `String s = new String("text")` behave similarly but are subtly different (string pool vs. heap object).
- Concatenation rules:
  - Numeric + numeric → addition
  - Anything + String → concatenation
  - Evaluation happens strictly left to right
  - `null` concatenated with a String prints as `"null"`
- `length()` returns character count (not zero-based); `charAt(index)` is zero-based and throws an exception if out of range.
- `substring(begin, end)` starts at `begin` (inclusive) and stops *before* `end` (exclusive); equal begin/end gives an empty string; begin > end throws an exception.
- `indexOf()` returns `-1` when no match is found (never throws).
- Case methods: `toUpperCase()`, `toLowerCase()`.
- Equality: `equals()` compares character content and is case-sensitive; `equalsIgnoreCase()` ignores case.
- Search helpers: `startsWith()`, `endsWith()`, `contains()`.
- `replace()` swaps characters or subsequences.
- Whitespace removal: `trim()` (ASCII only) vs. `strip()`/`stripLeading()`/`stripTrailing()` (Unicode-aware).
- Indentation methods `indent()` and `stripIndent()` also normalize line endings to `\n` (and `indent()` can add a trailing line break).
- `isEmpty()` checks for zero length; `isBlank()` checks for whitespace-only content.
- Formatting: `String.format()`, `formatted()`, with symbols `%s`, `%d`, `%f`, `%n`; flags can control decimal precision and padding.
- Method chaining reads left to right, each method operating on the previous result.
- The **string pool** reuses identical literal values; `==` on pooled literals returns `true`, but `new String(...)` or runtime-computed strings create separate objects so `==` returns `false`.
- `intern()` forces a string to use (or be added to) the string pool.
- Best practice: always use `equals()`, never `==` or `intern()`, to compare String content in real code.

## StringBuilder

- Mutable alternative to String; avoids creating many throwaway String objects (e.g., in loops).
- Method chaining on StringBuilder modifies the **same object** and returns a reference to it (unlike String, which creates new objects each time).
- Three constructors: no-arg (empty), from a String, or with an initial capacity.
- Key methods: `append()`, `insert(offset, value)`, `delete(start, end)`, `deleteCharAt(index)`, `replace(start, end, newStr)`, `reverse()`, `toString()`.
- `substring()` on a StringBuilder returns a String and does **not** modify the builder.
- `equals()` on StringBuilder checks object reference, not content (StringBuilder doesn't override `equals()`); convert to String first if you need content comparison.
- `==` on StringBuilder always checks reference identity.
- A String and StringBuilder can never be compared with `==` — code won't compile due to incompatible types.

## Arrays

- Fixed-size, zero-indexed containers that can hold primitives or object references.
- Can declare with brackets before or after the variable name; multiple declarations on one line can produce different types depending on bracket placement.
- Uninitialized array elements get default values (0, false, null, etc.); array size is fixed at creation regardless of contents.
- `length` is a field, not a method (no parentheses).
- Casting a broader-typed array reference to a narrower array type is legal, but assigning an incompatible object into it throws `ArrayStoreException` at runtime.
- `Arrays.sort()` sorts arrays; numeric sort is numeric order, String/char sort is alphabetic with numbers < uppercase < lowercase.
- `Arrays.binarySearch()` requires a sorted array; returns the match index if found, or a negative "insertion point" value if not; result is undefined on unsorted arrays.
- `Arrays.equals()` compares size and content (order matters); `==` only compares references.
- `Arrays.compare()` returns negative/zero/positive to indicate relative order, following rules similar to `compareTo()` (null < value, shorter-with-same-prefix < longer, numbers < letters, uppercase < lowercase).
- `Arrays.mismatch()` returns `-1` if arrays match, otherwise the first differing index.
- Varargs parameters (`Type... name`) can be treated like normal arrays inside a method.
- Arrays of arrays ("2D/3D arrays") can be jagged (each sub-array a different length); looping typically uses nested `for` or `for-each` loops.

## Math API

- `Math.min()` / `Math.max()`: overloaded for `int`, `long`, `float`, `double`.
- `Math.round()`: rounds half-up; returns `long` for a `double` input and `int` for a `float` input.
- `Math.ceil()` rounds up to the next whole double; `Math.floor()` rounds down.
- `Math.pow(base, exponent)` always returns a `double`.
- `Math.random()` returns a `double` in the range `[0, 1)`.
- `BigInteger` and `BigDecimal` handle values/precision beyond primitive limits (e.g., money calculations where floating-point rounding errors matter); both are immutable, support method chaining, and are best created via `valueOf()`.

## Date and Time API (`java.time`)

- Four core types:
  - `LocalDate` — date only
  - `LocalTime` — time only
  - `LocalDateTime` — date + time, no zone
  - `ZonedDateTime` — date + time + time zone
- All are created via static factory methods (`now()`, `of()`) — constructors are private, so `new LocalDate()` doesn't compile.
- Months are 1-based (unlike most Java indexing, which is 0-based).
- Invalid date/time values throw `DateTimeException`.
- All date/time objects are immutable — the return value of `plus...()`/`minus...()`/`with...()` methods must be reassigned or the change is lost.
- Not every method applies to every type: `LocalDate` has no time-based methods (e.g., can't call `plusMinutes()`), and `LocalTime` has no date-based methods.
- `with...()` methods create a copy with one field changed (e.g., `withDayOfMonth()`).
- `at...()` methods combine types (e.g., `LocalDate.atTime()` → `LocalDateTime`; `LocalDateTime.atZone()` → `ZonedDateTime`).
- `Period` represents date-based amounts (years/months/days); can't be created via chained factory calls (only the last call takes effect) — use `Period.of(y, m, d)` for combinations.
- `Duration` represents time-based amounts (hours/minutes/seconds/nanos); works with types that have a time component, not with `LocalDate` alone.
- `ChronoUnit` (e.g., `ChronoUnit.HOURS.between(...)`) measures the difference between two temporal values and truncates rather than rounds; mismatched types (date vs. time) throw exceptions.
- `Instant` represents a fixed point in time in GMT; a `ZonedDateTime` can convert `toInstant()`, but a `LocalDateTime` cannot (it has no time zone to anchor it).
- Daylight saving time: clocks spring forward (skip an hour) in March and fall back (repeat an hour) in November in the U.S.; Java automatically adjusts nonexistent or ambiguous local times to the correct GMT-equivalent moment.

## Exam Essentials (chapter recap)

- Be able to trace String output: know it's immutable, indexes are zero-based, and `substring()`'s end index is exclusive.
- Be able to trace StringBuilder output: know it's mutable, and that `append()`, `insert()`, and `delete()` change state while `substring()` does not.
- Understand `==` (reference/object equality) vs. `equals()` (content equality, as implemented by the class).
- Be able to trace array output: correct declaration/instantiation, valid index ranges, and sorting/searching behavior.
- Know the return types of Math methods, since they vary by which overload is invoked.
- Recognize invalid date/time operations: `LocalDate` has no time fields and `LocalTime` has no date fields; watch for ignored return values and daylight-saving edge cases.

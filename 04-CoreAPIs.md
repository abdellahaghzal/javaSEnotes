# Chapter 4: Core APIs

## Strings

- Strings are immutable; every "modifying" method returns a new `String` rather than changing the original, so calling a method like `toUpperCase()`, `trim()`, `substring()`, or `concat()` without using the return value has no effect.
- String literals don't require `new`; `String s = "text"` and `String s = new String("text")` behave similarly but are subtly different (string pool vs. heap object).
- A numeric value cannot be assigned directly to a `String` variable; `String s = numFish + 1;` does not compile because the result is an `int`.
- Concatenation rules:
  - Numeric + numeric → addition
  - Anything + String → concatenation
  - Evaluation happens strictly left to right (`1 + 2 + "a"` is `"3a"`, while `"a" + 1 + 2` is `"a12"`)
  - `null` concatenated with a String prints as `"null"`
  - `+=` can append any type to a String (`s += 2; s += 'c'; s += false;` produces `"2cfalse"`)
- `length()` returns character count (not zero-based); `charAt(index)` is zero-based and throws an exception if out of range.
- `length()` is a method on `String` and `StringBuilder`, while `length` (no parentheses) is a field on arrays; writing `string.length` does not compile.
- `substring(begin, end)` starts at `begin` (inclusive) and stops *before* `end` (exclusive); equal begin/end gives an empty string; begin > end throws an exception; `substring(begin)` runs to the end of the string.
- `indexOf()` returns `-1` when no match is found (never throws).
- Case methods: `toUpperCase()`, `toLowerCase()`.
- Equality: `equals()` compares character content and is case-sensitive; `equalsIgnoreCase()` ignores case.
- Search helpers: `startsWith()`, `endsWith()`, `contains()`.
- `replace()` swaps characters or subsequences (`"12345".replace("123", "1")` returns `"145"`).
- Whitespace removal: `trim()` (ASCII only) vs. `strip()`/`stripLeading()`/`stripTrailing()` (Unicode-aware).
- Indentation methods `indent()` and `stripIndent()` also normalize line endings to `\n`; `indent(n)` adds `n` spaces to the start of each line and appends a line break at the end, so the result is one character longer than expected.
- A text block ending on the same line as its last text adds no trailing line break, so `"""` followed by `purr"""` is `"purr"` (length 4).
- `isEmpty()` checks for zero length; `isBlank()` checks for whitespace-only content.
- Formatting: `String.format()`, `formatted()`, with symbols `%s`, `%d`, `%f`, `%n`; flags can control decimal precision and padding.
- Method chaining reads left to right, each method operating on the previous result.
- The **string pool** reuses identical literal values; `==` on pooled literals returns `true`, but `new String(...)` or runtime-computed strings create separate objects so `==` returns `false`.
- A String built at runtime (for example, with `+=`) is not in the pool, so `s == "2cfalse"` is `false` even when `s.equals("2cfalse")` is `true`.
- `intern()` forces a string to use (or be added to) the string pool and returns the pooled reference, so `new String("Hello").intern() == "Hello"` is `true`, while `"Hello".intern() == new String("Hello")` is `false`.
- Best practice: always use `equals()`, never `==` or `intern()`, to compare String content in real code.

## StringBuilder

- Mutable alternative to String; avoids creating many throwaway String objects (e.g., in loops).
- Method chaining on StringBuilder modifies the **same object** and returns a reference to it (unlike String, which creates new objects each time).
- Arguments in a chained call are evaluated when that call is reached, so `sb.delete(0, 3).deleteCharAt(sb.length() - 1)` uses the length after the delete.
- Three constructors: no-arg (empty), from a String, or with an initial capacity.
- Key methods: `append()`, `insert(offset, value)`, `delete(start, end)`, `deleteCharAt(index)`, `replace(start, end, newStr)`, `reverse()`, `toString()`.
- `insert(offset, value)` places the new text before the character at that index (for example, `"abbaa"` with `insert(4, "ccc")` becomes `"abbaccca"`).
- `delete(start, end)` and `replace(start, end, newStr)` remove from `start` up to but not including `end`; `replace(2, 4, "6")` on `"12345"` gives `"1265"`.
- `deleteCharAt(index)` throws an exception if the index equals or exceeds the length.
- `charAt()`, `indexOf()`, and `length()` work on a StringBuilder as they do on a String.
- `substring()` on a StringBuilder returns a String and does **not** modify the builder; it follows the same begin/end rules as String (`substring(6, 5)` throws, `substring(2, 2)` returns an empty String).
- A chained call ending in `substring()` returns a String, so the StringBuilder itself keeps whatever earlier calls in the chain changed.
- `equals()` on StringBuilder checks object reference, not content (StringBuilder doesn't override `equals()`); convert to String first if you need content comparison.
- `==` on StringBuilder always checks reference identity.
- A String and StringBuilder can never be compared with `==` — code won't compile due to incompatible types.
- A method that receives a StringBuilder can change the caller's object, but a method that receives a String cannot, because Strings are immutable (calling `roar1.concat("!!!")` inside a method leaves the caller's String unchanged).

## Arrays

- Fixed-size, zero-indexed containers that can hold primitives or object references; the first element is at index 0 and the last at `length - 1`.
- Can declare with brackets before or after the variable name; multiple declarations on one line can produce different types depending on bracket placement (`int[] a, b[];` makes `a` one-dimensional and `b` two-dimensional).
- Instantiating with `new` requires a size on the first dimension (`new int[5]`, `new int[2][]`, `new Object[3][0][5]`), while `new int[]` and `new int[][]` without a size do not compile; omitting the size is only legal with an initializer (`new int[] {1, 2}`).
- The type after `new` must be an actual type (`new String[6]`, not `new beans[6]`), and its dimensions must be consistent with the declaration.
- Uninitialized array elements get default values (0, false, null, etc.); array size is fixed at creation regardless of contents.
- Arrays are not immutable: the elements can change even though the size cannot.
- `length` is a field, not a method (no parentheses).
- Casting a broader-typed array reference to a narrower array type is legal, but assigning an incompatible object into it throws `ArrayStoreException` at runtime.
- `Arrays.sort()` sorts arrays; numeric sort is numeric order, String/char sort is alphabetic with numbers < uppercase < lowercase (so `{"PIG", "pig", "123"}` sorts to `[123, PIG, pig]`).
- `Arrays.binarySearch()` requires a sorted array; returns the match index if found, or a negative value equal to `-(insertion point) - 1` if not; result is undefined on unsorted arrays.
- For example, searching for `"Pippa"` in `[123, PIG, pig]` gives insertion point 2, so the result is `-3`.
- `Arrays.toString()` prints the contents of an array in brackets.
- Calling `equals()` directly on an array uses reference equality, so two different arrays with identical contents return `false`; `Arrays.equals()` compares size and content (order matters), and `==` only compares references.
- `Arrays.compare()` returns negative/zero/positive to indicate relative order, following rules similar to `compareTo()` (null < value, shorter-with-same-prefix < longer, numbers < letters, uppercase < lowercase), and returns `0` when the arrays are identical.
- `Arrays.mismatch()` returns `-1` if arrays match, otherwise the first differing index; if one array is a prefix of the other, it returns the length of the shorter array.
- Varargs parameters (`Type... name`) can be treated like normal arrays inside a method.
- Arrays of arrays ("2D/3D arrays") can be jagged (each sub-array a different length); looping typically uses nested `for` or `for-each` loops.

## Math API

- `Math.min()` / `Math.max()`: overloaded for `int`, `long`, `float`, `double`.
- `Math.round()`: rounds half-up; returns `long` for a `double` input and `int` for a `float` input, so `int x = Math.round(1.0);` does not compile without a cast.
- `Math.ceil()` rounds up to the next whole double; `Math.floor()` rounds down.
- `Math.pow(base, exponent)` always returns a `double`.
- `Math.random()` returns a `double` in the range `[0, 1)`, so assigning it to a `float` does not compile without a cast.
- Smaller numeric values widen automatically when placed in a larger array or variable type (an `int` and a `long` can both go into a `double[]` initializer).
- `BigInteger` and `BigDecimal` handle values/precision beyond primitive limits (e.g., money calculations where floating-point rounding errors matter); both are immutable, support method chaining, and are best created via `valueOf()`.

## Date and Time API (`java.time`)

- Four core types:
  - `LocalDate` — date only
  - `LocalTime` — time only
  - `LocalDateTime` — date + time, no zone
  - `ZonedDateTime` — date + time + time zone
- All are created via static factory methods (`now()`, `of()`) — constructors are private, so `new LocalDate()` doesn't compile, and neither does `new Instant()`.
- Months are 1-based (unlike most Java indexing, which is 0-based), and the `Month` enum can be used instead of a number (`Month.MARCH`).
- Invalid date/time values (such as March 40 or February 29 in a non-leap year) throw `DateTimeException`.
- All date/time objects are immutable — the return value of `plus...()`/`minus...()`/`with...()` methods must be reassigned or the change is lost.
- Adding months or years to a date at the end of a month clamps the day to the last valid day (October 31 plus 5 months backward and 1 month forward lands on June 30).
- Not every method applies to every type: `LocalDate` has no time-based methods (e.g., can't call `plusMinutes()` or `plusHours()`), and `LocalTime` has no date-based methods.
- `with...()` methods create a copy with one field changed (e.g., `withDayOfMonth()`, `withYear()`).
- `at...()` methods combine types (e.g., `LocalDate.atTime()` → `LocalDateTime`; `LocalDateTime.atZone()` → `ZonedDateTime`).
- `ZonedDateTime.of()` accepts either a `LocalDate` and `LocalTime` plus a `ZoneId`, or a `LocalDateTime` plus a `ZoneId`.
- Printing a `LocalDateTime` shows the format `2026-06-30T13:04`, with the date, a `T`, and the time.
- `Period` represents date-based amounts (years/months/days); can't be created via chained factory calls (only the last call takes effect) — use `Period.of(y, m, d)` for combinations.
- `Duration` represents time-based amounts (hours/minutes/seconds/nanos); works with types that have a time component, not with `LocalDate` alone.
- `ChronoUnit` (e.g., `ChronoUnit.HOURS.between(...)`) measures the difference between two temporal values and truncates rather than rounds; mismatched types (date vs. time) throw exceptions.
- `Instant` represents a fixed point in time in GMT; a `ZonedDateTime` can convert `toInstant()`, but a `LocalDateTime` cannot (it has no time zone to anchor it).
- An offset such as `GMT-04:00` means the local time is four hours behind GMT, so add four hours to get GMT (`05:00 GMT-04:00` is `09:00` GMT, and `09:00 GMT-06:00` is `15:00` GMT).
- To compare two zoned times, convert both to GMT first; the one with the earlier GMT value is earlier, and the difference is the gap between the converted values.
- Daylight saving time: clocks spring forward (skip an hour) in March and fall back (repeat an hour) in November in the U.S.; Java automatically adjusts nonexistent or ambiguous local times to the correct GMT-equivalent moment without throwing an exception.
- A time that does not exist (such as 2:15 on the spring-forward date) is moved forward by an hour (to 3:15), while an ambiguous time on the fall-back date uses the first (earlier) occurrence.
- Adding an hour with `plus(1, ChronoUnit.HOURS)` across a spring-forward gap moves the clock by two hours on the wall (1:30 becomes 3:30) but only one real hour has elapsed, so `ChronoUnit.HOURS.between()` returns 1, and the offset changes.

## Exam Essentials (chapter recap)

- Be able to trace String output: know it's immutable, indexes are zero-based, and `substring()`'s end index is exclusive.
- Be able to trace StringBuilder output: know it's mutable, and that `append()`, `insert()`, and `delete()` change state while `substring()` does not.
- Understand `==` (reference/object equality) vs. `equals()` (content equality, as implemented by the class).
- Be able to trace array output: correct declaration/instantiation, valid index ranges, and sorting/searching behavior.
- Know the return types of Math methods, since they vary by which overload is invoked.
- Recognize invalid date/time operations: `LocalDate` has no time fields and `LocalTime` has no date fields; watch for ignored return values and daylight-saving edge cases.

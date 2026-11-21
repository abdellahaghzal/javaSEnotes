# Chapter 11: Exceptions and Localization

## Understanding Exceptions
- An exception is Java's way of saying it doesn't know what to do and the code must deal with it.
- A method can either handle an exception itself or make it the caller's responsibility.
- Return codes (like `-1`) are sometimes used instead of exceptions (e.g., search methods), but exceptions are generally preferred.
- `Throwable` is the parent class for all exception/error objects.
- Exceptions are for unexpected situations, such as an invalid parameter passed into a method (`IllegalArgumentException`), a missing file, or a failing system resource.
- Exceptions are not the right tool for expected outcomes (an element not found in a list search, where a return code like `-1` or an `Optional` fits), for normal control flow (looping through a list), or for code you have not written yet.
- Problems the program cannot recover from (such as the computer catching fire or running out of memory) are `Error` situations, not something application code should handle.

## Exception Types
- **Checked exception**: subclass of `Exception` but not `RuntimeException`; must be handled or declared (handle or declare rule).
- **Unchecked exception**: subclass of `RuntimeException` or `Error`; does not need to be handled or declared.
- **Error**: subclass of `Error`; represents serious, unrecoverable problems; should not be caught in application code.
- `Throwable` should never be caught directly.
- A class that extends `Throwable` directly (not through `Exception` or `Error`) is treated as **checked**, so it must be handled or declared.
- A subclass of a checked exception is checked, and a subclass of an unchecked exception is unchecked. To classify a custom exception, follow its inheritance chain up to `RuntimeException`, `Error`, `Exception`, or `Throwable`.
- The `throw` keyword throws an exception; `throws` declares that a method might throw one.
- Correct syntax: `public void m() throws Exception { throw new Exception(); }`. Writing `throw` in the method declaration or `throws new ...` as a statement does not compile.
- Exceptions are objects — they can be stored in a variable and thrown later (`var e = new RuntimeException(); throw e;`).
- `throw RuntimeException();` does not compile — missing `new`.
- Code after an unconditional `throw` in the same block is unreachable and won't compile.
- Calling a method that declares a checked exception requires the caller to handle or declare it.
- Declaring an exception a method never actually throws is legal (not unreachable code).
- A method that declares `throws IOException` may throw `IOException` or any subclass of it (for example `FileNotFoundException`) and any unchecked exception, but not a broader checked exception such as `Exception`.
- Declaring a custom exception class without a constructor is fine, because the default constructor is created automatically. A class that declares a constructor does not affect its subclasses (a subclass is fine as long as a matching super constructor is available).
- **Overriding rule**: an overridden method cannot declare new or broader checked exceptions than the parent method; it *can* declare fewer or narrower ones.
- Three ways to print an exception: `System.out.println(e)`, `e.getMessage()`, `e.printStackTrace()`.

## Common Exception Classes
- **Unchecked**: `ArithmeticException`, `ArrayIndexOutOfBoundsException`, `ClassCastException`, `NullPointerException`, `IllegalArgumentException`, `NumberFormatException` (subclass of `IllegalArgumentException`).
- **Checked**: `FileNotFoundException` (subclass of `IOException`), `IOException`, `NotSerializableException` (subclass of `IOException`), `ParseException`.
- **Errors**: `ExceptionInInitializerError`, `StackOverflowError`, `NoClassDefFoundError`.
- Helpful NullPointerExceptions show the variable name for instance/static variables; local variables/parameters show `<localX>`/`<parameterX>` unless compiled with `-g:vars`, in which case the real variable names appear.
- A helpful NullPointerException names only the **one** reference that was null when the exception occurred. When a line has several null references, the first one actually dereferenced at runtime is reported.
- In a method call like `huey.substring(dewey.intValue())`, the arguments are evaluated before the call is made on the target. If both `huey` and `dewey` are null, `dewey.intValue()` fails first, so `dewey` is named.
- Comparing references with `==` (such as `louie == someValue`) never throws a `NullPointerException`.

## try/catch/finally
- Braces are required for `try` and `catch` blocks (unlike `if`/loops).
- A traditional `try` block must be followed by a `catch`, a `finally`, or both. A `try` without a `finally` needs at least one `catch`, and a `try` without a `catch` needs a `finally`.
- Catch blocks are checked in order; a superclass catch block before a subclass catch block causes unreachable code (compiler error). For example, `catch (RuntimeException e)` before `catch (ArithmeticException e)` does not compile.
- Catching a checked exception that the `try` block can never throw is a compiler error (except for `Exception` and `Throwable`).
- The exception variable in a catch block is scoped only to that catch block, and its name cannot be the same as a local variable or parameter already in scope (for example, `catch (Exception s)` in a method with a parameter named `s` does not compile).
- **Multi-catch**: uses `|` to separate exception types; only one variable name, listed once at the end; cannot combine related (subclass/superclass) exceptions. Writing `catch (SneezeException | SniffleException e)` where one extends the other does not compile. Unrelated (sibling) types are fine.
- The multi-catch variable is implicitly final and cannot be reassigned.
- Throwing a new checked exception inside a `catch` or `finally` block requires handling or declaring it too, just like anywhere else.
- `finally` always executes, regardless of exceptions, unless `System.exit()` is called in the try/catch.
- A `finally` block's `return` or exception overrides any `return`/exception from `try`/`catch`.
- A `finally` block may not fully complete if it throws its own exception midway.
- When an exception is not caught by any matching `catch`, the `finally` block still runs, and then the exception propagates to the caller with a stack trace. Code after the try statement in that method does not run.
- Tracing tip: write each print in the order it executes, and check at which line an exception is thrown to see which code is skipped.

## Try-with-Resources
- Automatically closes resources implementing `AutoCloseable` (or `Closeable`, which extends it). A class must implement one of these two interfaces to be used. Names like `Resource`, `AutomaticResource`, `Serializable`, or `RuntimeException` do not qualify.
- `AutoCloseable.close()` can throw `Exception`; `Closeable.close()` throws `IOException`.
- If `close()` declares a checked exception, the code using the resource must handle or declare it, even if there is no `catch` block (for example, add `throws Exception` to the method or a `catch`). Adding an empty `finally` does not help.
- Multiple resources are closed in the **reverse order** of declaration.
- Neither `catch` nor `finally` is required with try-with-resources (unlike traditional try), as long as all checked exceptions are handled or declared.
- Each resource must be declared with its own data type, separated by semicolons (not commas). Writing `try (FileReader r = null, p = new FileReader(""))` does not compile, because `p` has no type.
- `var` can be used to declare resources.
- Resources go out of scope at the end of the `try` block — inaccessible in `catch`/`finally`.
- Resources can be declared outside the try clause if they are `final` or effectively final (just reference the variable name, separated by `;`). A variable that is reassigned anywhere in the method (even after the try block, such as `walk = null;`) is not effectively final and cannot be used.
- The implicit (compiler-generated) `finally` block that closes resources always runs before any programmer-defined `catch`/`finally`.
- Order of execution: try block → resources closed (reverse order) → matching `catch` → `finally`. For example, a try that prints `T`, a `Window` close that prints `W` and throws, and a `Door` close that prints `D`, followed by a catch printing `E` and a finally printing `F`, outputs `TWDEF`.
- A `null` resource is allowed. Java skips calling `close()` on it, so no `NullPointerException` occurs.

## Suppressed Exceptions
- When both the try block and `close()` throw exceptions, the first (primary) exception is thrown to the caller; subsequent ones become **suppressed exceptions**.
- Suppressed exceptions are retrieved via `getSuppressed()`, which returns a `Throwable[]` (so `.length` gives the count).
- The catch block matches against the **primary** exception type, not suppressed ones.
- If more than one resource's `close()` throws, the primary exception comes from the last-declared resource (since resources close in reverse order).
- Suppressed exceptions apply only to exceptions thrown in the `try` clause; if a `finally` block also throws, the original (and any suppressed) exceptions are lost.
- Example: if the try block throws `"blizzard"` and two resources each throw `"flurry"` while closing, the caught exception is `"blizzard"` with 2 suppressed exceptions. If the try block throws nothing, the first `close()` exception is primary and later ones are suppressed.

## Formatting Numbers
- `NumberFormat` is abstract; `DecimalFormat` is the concrete implementation used with pattern strings.
- Pattern symbols: `#` omits the digit if not present; `0` fills with a zero if not present.
- The number of `0` characters to the left of the decimal point sets the minimum number of integer digits, and the count of `0`/`#` characters to the right of it sets the fraction digits (`0` is required, `#` is optional). A `,` in the pattern adds grouping separators.
- Examples with `new DecimalFormat("#,###,000.0#")`: `5.21` → `005.21`, `8.49` → `008.49`, `1234` → `1,234.0`.
- With `"0,000.0#"` the minimum is four integer digits, so `5.21` would print as `0,005.21`.
- `CompactNumberFormat` (subclass of `NumberFormat`) produces shortened output (e.g., `7M` for 7 million) and is locale-specific.
  - Default style is `SHORT` if not specified. `NumberFormat.getCompactNumberInstance()` and `getCompactNumberInstance(Locale.getDefault(), Style.SHORT)` give the same result (for example `100K` for 100,102.2).
  - `LONG` style spells the unit out (for example `100 thousand`).
  - Rounds to the first three digits of the highest range (K, M, B, T).
  - Can parse compact ("1M"), full ("1000000"), or partially formatted ("1,000,000" → stops at comma, returns 1) strings; unrecognized symbols like `$` throw `ParseException`.

## Formatting Dates and Times
- `DateTimeFormatter` provides predefined formats (e.g., `ISO_LOCAL_DATE`) and custom patterns via `ofPattern()`.
- Formatting a date-only object with a time-only formatter (or vice versa) throws a `RuntimeException` (specifically `DateTimeException`) at runtime.
- Pattern symbols are case-sensitive: `y` = year, `M` = month, `d` = day, `H` = 24-hour, `h` = 12-hour, `m` = minute, `s` = second, `a` = AM/PM, `z` = time zone name, `Z` = time zone offset.
- The number of repeated symbols affects output length/format (e.g., `M` vs `MM` vs `MMM` vs `MMMM`).
- Not all symbols work with all date/time types (e.g., `z`/`Z` require `ZonedDateTime`; month symbols don't work with `LocalTime`). Using `z` with a `LocalDateTime` throws a `DateTimeException` (`UnsupportedTemporalTypeException`) when `format()` is called, even though `ofPattern()` itself succeeds.
- Hour/minute symbols (`h`, `H`, `m`) require a type with a time (`LocalTime`, `LocalDateTime`, `ZonedDateTime`), so using them with `LocalDate` throws a runtime exception.
- Both `date.format(formatter)` and `formatter.format(date)` are valid and equivalent.
- Literal text in a pattern must be escaped with single quotes (`'text'`); two single quotes together (`''`) represent a literal single quote.
- Unescaped, unrecognized characters in a pattern throw `IllegalArgumentException` when `ofPattern()` is called (for example, the unquoted `o` in `"hh o'clock"`, or an unterminated quote); this happens at runtime, not compile time.
- Classes such as `ZonedTime` and `LocalTimestamp` do not exist. The date/time classes are `LocalDate`, `LocalTime`, `LocalDateTime`, and `ZonedDateTime`.
- `LocalDate.parse(text, formatter)` needs a formatter that matches the text. Parsing `"2025-04-30"` with `ISO_LOCAL_DATE_TIME` throws a `DateTimeParseException` at runtime because the time part is missing. Use `ISO_LOCAL_DATE` (the default) for a date-only string.
- When a statement inside a `try` block throws, the rest of the block is skipped, so later `print` calls never run.

## Locales
- A `Locale` combines a required lowercase language code and an optional uppercase country code (e.g., `en`, `en_US`).
- Invalid formats: missing underscore, reversed language/country order, country without language, uppercase language code.
- Ways to create a `Locale`: built-in constants (`Locale.GERMAN`, `Locale.GERMANY`), factory method `Locale.of(language, country)`, and the `Locale.Builder` class (`setLanguage()`, `setRegion()`, `.build()`).
- `Locale.of("qw")` and `Locale.of("wp", "VW")` are properly formatted. `Locale.of("xM")` (uppercase in language) and `Locale.of("MQ", "ks")` (uppercase language and lowercase country) are not.
- There is no `Locale.create()` method. A `Locale.Builder` chain must end with `.build()`; without it the expression is a `Builder`, not a `Locale`.
- The deprecated `Locale` constructor should be avoided in favor of the above.
- `Locale.getDefault()` / `Locale.setDefault(locale)` get/set the JVM's default locale (only affects the current run, not the system).
- `Locale.Category` (`DISPLAY` and `FORMAT`) allows setting locale-related behavior independently; `Locale.setDefault(locale)` without a category sets both.
- A locale passed explicitly to a factory method (such as `NumberFormat.getCurrencyInstance(Locale.GERMANY)`) overrides the default locale, so changing the default has no effect on that call.

## Localizing Numbers, Currency, and Percentages
- `NumberFormat` factory methods: `getInstance()`, `getNumberInstance()`, `getCurrencyInstance()`, `getPercentInstance()`, `getIntegerInstance()`, `getCompactNumberInstance()` — each with default-locale and locale-parameter overloads.
- Formatting converts a number to a `String`; parsing converts a `String` to a `Number` (via `parse()`, which throws checked `ParseException`).
- Locale affects grouping/decimal separators (e.g., `,` vs `.` vs space) and currency symbols/placement. For example, 2.4 formatted with the German currency instance prints `2,40 €`.
- `NumberFormat.parse()` on currency strips currency symbols/grouping characters automatically.
- Format classes (`NumberFormat`, `DecimalFormat`, etc.) are **not thread-safe** — don't store them in shared/static/instance variables.
- Use `int` or `BigDecimal` for money, not `double` (floating-point rounding errors).
- Common things to localize: dates, numbers, and currency. Code elements such as lambda expressions, class names, and variable names are never localized.

## Localizing Dates
- `DateTimeFormatter.ofLocalizedDate/Time/DateTime(FormatStyle)` use `SHORT`, `MEDIUM`, `LONG`, or `FULL` styles based on locale.
- Append `.withLocale(locale)` to apply a specific locale to a formatter.

## Resource Bundles
- A resource bundle stores locale-specific key/value pairs, typically in `.properties` files.
- Naming convention: `BundleName_language_COUNTRY.properties` (country optional). A name with only a country, such as `Dolphins_US.properties`, is not valid.
- Retrieval methods: `ResourceBundle.getBundle("name")` (default locale) or `ResourceBundle.getBundle("name", locale)`.
- `ResourceBundle` is abstract and is loaded with `getBundle()`, never with a `new ResourceBundle()` constructor. Besides properties files, a bundle can also be a Java class (`ListResourceBundle`), so values are not always read with the `Properties` class.
- `keySet()` returns all keys in the bundle; `getString(key)` throws `MissingResourceException` if the key isn't found anywhere in the hierarchy.
- **Resource bundle selection order** (requested locale → default locale → default bundle):
  1. `Bundle_requestedLang_requestedCountry.properties`
  2. `Bundle_requestedLang.properties`
  3. `Bundle_defaultLang_defaultCountry.properties`
  4. `Bundle_defaultLang.properties`
  5. `Bundle.properties`
  6. Otherwise, throw `MissingResourceException`
- When no locale is passed to `getBundle()`, the requested locale **is** the default locale. If the default is `en_US`, Java looks for `Bundle_en_US`, then `Bundle_en`, then `Bundle.properties`.
- The default locale is only used as a fallback when a specific locale is requested and no matching bundle exists. If a bundle for the requested locale exists, the default locale's bundles are not used.
- A default locale always exists (set by the JVM), so you can use a resource bundle without ever calling `Locale.setDefault()`.
- Changing the default locale with `Locale.setDefault()` lasts only for the current run of the program.
- Once a matching bundle is found, Java only looks within **that bundle's own hierarchy** (dropping components down to the base) for individual keys — it does NOT fall back to the default locale's bundles, only to the default (no-locale) bundle. So keys do not all have to be in the same file.
- Example: with `Dolphins.properties` (`name`, `age`), `Dolphins_en.properties` (`name`, `age`), and `Dolphins_fr.properties` (`name` only), requesting `fr` while the default is `en_US` gives `name` from `Dolphins_fr` and `age` from `Dolphins.properties` (the parent), not from `Dolphins_en`.
- `MessageFormat.format(pattern, args...)` substitutes indexed placeholders like `{0}`, `{1}` in resource bundle strings.
- The `Properties` class is like a `String`-keyed/valued `HashMap`; `getProperty(key)` and `getProperty(key, default)` support default values, but plain `get()` does not accept a default.

## Exam Essentials (Chapter Summary Points)
- Know all exceptions descend from `Throwable`; never catch `Error` subclasses; only catch `Exception` subclasses in application code.
- Distinguish checked vs. unchecked exceptions and the handle-or-declare rule. Remember that a direct subclass of `Throwable` is checked.
- Understand `try` statement flow: needs `catch` and/or `finally`; catch-block ordering rules; multi-catch restrictions; `finally` always runs last.
- Understand try-with-resources: resources close in reverse declaration order; implicit `finally` runs before any explicit `catch`/`finally`; resources declared outside the try must be effectively final; `close()` exceptions must be handled or declared.
- Know the difference between `throw` and `throws`, and rules for overriding methods that declare exceptions.
- Know valid locale string rules (lowercase language required, uppercase country optional) and locale-creation methods.
- Know how to format dates, numbers, currency, percentages, and messages, and how locale changes their output. Know which pattern symbols work with which date/time type, and that invalid patterns and unsupported symbols fail at runtime.
- Know the resource bundle lookup order and the value-selection hierarchy once a bundle is chosen.
- Look for compile errors before runtime behavior: invalid syntax, unreachable catch blocks, unhandled checked exceptions, missing types in resource declarations, and variables that are not effectively final.

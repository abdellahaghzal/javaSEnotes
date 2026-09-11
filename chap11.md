# Chapter 11: Exceptions and Localization

## Understanding Exceptions
- An exception is Java's way of saying it doesn't know what to do and the code must deal with it.
- A method can either handle an exception itself or make it the caller's responsibility.
- Return codes (like `-1`) are sometimes used instead of exceptions (e.g., search methods), but exceptions are generally preferred.
- `Throwable` is the parent class for all exception/error objects.

## Exception Types
- **Checked exception**: subclass of `Exception` but not `RuntimeException`; must be handled or declared (handle or declare rule).
- **Unchecked exception**: subclass of `RuntimeException` or `Error`; does not need to be handled or declared.
- **Error**: subclass of `Error`; represents serious, unrecoverable problems; should not be caught in application code.
- `Throwable` should never be caught directly.
- The `throw` keyword throws an exception; `throws` declares that a method might throw one.
- Exceptions are objects — they can be stored in a variable and thrown later (`var e = new RuntimeException(); throw e;`).
- `throw RuntimeException();` does not compile — missing `new`.
- Code after an unconditional `throw` in the same block is unreachable and won't compile.
- Calling a method that declares a checked exception requires the caller to handle or declare it.
- Declaring an exception a method never actually throws is legal (not unreachable code).
- **Overriding rule**: an overridden method cannot declare new or broader checked exceptions than the parent method; it *can* declare fewer or narrower ones.
- Three ways to print an exception: `System.out.println(e)`, `e.getMessage()`, `e.printStackTrace()`.

## Common Exception Classes
- **Unchecked**: `ArithmeticException`, `ArrayIndexOutOfBoundsException`, `ClassCastException`, `NullPointerException`, `IllegalArgumentException`, `NumberFormatException` (subclass of `IllegalArgumentException`).
- **Checked**: `FileNotFoundException` (subclass of `IOException`), `IOException`, `NotSerializableException` (subclass of `IOException`), `ParseException`.
- **Errors**: `ExceptionInInitializerError`, `StackOverflowError`, `NoClassDefFoundError`.
- Helpful NullPointerExceptions show the variable name for instance/static variables; local variables/parameters show `<localX>`/`<parameterX>` unless compiled with `-g:vars`.

## try/catch/finally
- Braces are required for `try` and `catch` blocks (unlike `if`/loops).
- A `try` block must be followed by a `catch`, a `finally`, or both.
- Catch blocks are checked in order; a superclass catch block before a subclass catch block causes unreachable code (compiler error).
- The exception variable in a catch block is scoped only to that catch block.
- **Multi-catch**: uses `|` to separate exception types; only one variable name, listed once at the end; cannot combine related (subclass/superclass) exceptions.
- `finally` always executes, regardless of exceptions, unless `System.exit()` is called in the try/catch.
- A `finally` block's `return` or exception overrides any `return`/exception from `try`/`catch`.
- A `finally` block may not fully complete if it throws its own exception midway.

## Try-with-Resources
- Automatically closes resources implementing `AutoCloseable` (or `Closeable`, which extends it).
- `AutoCloseable.close()` can throw `Exception`; `Closeable.close()` throws `IOException`.
- Multiple resources are closed in the **reverse order** of declaration.
- Neither `catch` nor `finally` is required with try-with-resources (unlike traditional try).
- Each resource must be declared with its own data type, separated by semicolons (not commas).
- `var` can be used to declare resources.
- Resources go out of scope at the end of the `try` block — inaccessible in `catch`/`finally`.
- Resources can be declared outside the try clause if they are `final` or effectively final (just reference the variable name, separated by `;`).
- The implicit (compiler-generated) `finally` block that closes resources always runs before any programmer-defined `catch`/`finally`.

## Suppressed Exceptions
- When both the try block and `close()` throw exceptions, the first (primary) exception is thrown to the caller; subsequent ones become **suppressed exceptions**.
- Suppressed exceptions are retrieved via `getSuppressed()`.
- The catch block matches against the **primary** exception type, not suppressed ones.
- If more than one resource's `close()` throws, the primary exception comes from the last-declared resource (since resources close in reverse order).
- Suppressed exceptions apply only to exceptions thrown in the `try` clause; if a `finally` block also throws, the original (and any suppressed) exceptions are lost.

## Formatting Numbers
- `NumberFormat` is abstract; `DecimalFormat` is the concrete implementation used with pattern strings.
- Pattern symbols: `#` omits the digit if not present; `0` fills with a zero if not present.
- `CompactNumberFormat` (subclass of `NumberFormat`) produces shortened output (e.g., `7M` for 7 million) and is locale-specific.
  - Default style is `SHORT` if not specified.
  - Rounds to the first three digits of the highest range (K, M, B, T).
  - Can parse compact ("1M"), full ("1000000"), or partially formatted ("1,000,000" → stops at comma, returns 1) strings; unrecognized symbols like `$` throw `ParseException`.

## Formatting Dates and Times
- `DateTimeFormatter` provides predefined formats (e.g., `ISO_LOCAL_DATE`) and custom patterns via `ofPattern()`.
- Formatting a date-only object with a time-only formatter (or vice versa) throws a `RuntimeException` (specifically `DateTimeException`) at runtime.
- Pattern symbols are case-sensitive: `y` = year, `M` = month, `d` = day, `H` = 24-hour, `h` = 12-hour, `m` = minute, `s` = second, `a` = AM/PM, `z` = time zone name, `Z` = time zone offset.
- The number of repeated symbols affects output length/format (e.g., `M` vs `MM` vs `MMM` vs `MMMM`).
- Not all symbols work with all date/time types (e.g., `z`/`Z` require `ZonedDateTime`; month symbols don't work with `LocalTime`).
- Both `date.format(formatter)` and `formatter.format(date)` are valid and equivalent.
- Literal text in a pattern must be escaped with single quotes (`'text'`); two single quotes together (`''`) represent a literal single quote.
- Unescaped, unrecognized characters in a pattern throw `IllegalArgumentException`; unterminated quotes also throw an exception.

## Locales
- A `Locale` combines a required lowercase language code and an optional uppercase country code (e.g., `en`, `en_US`).
- Invalid formats: missing underscore, reversed language/country order, country without language, uppercase language code.
- Ways to create a `Locale`: built-in constants (`Locale.GERMAN`, `Locale.GERMANY`), factory method `Locale.of(language, country)`, and the `Locale.Builder` class (`setLanguage()`, `setRegion()`, `.build()`).
- The deprecated `Locale` constructor should be avoided in favor of the above.
- `Locale.getDefault()` / `Locale.setDefault(locale)` get/set the JVM's default locale (only affects the current run, not the system).
- `Locale.Category` (`DISPLAY` and `FORMAT`) allows setting locale-related behavior independently; `Locale.setDefault(locale)` without a category sets both.

## Localizing Numbers, Currency, and Percentages
- `NumberFormat` factory methods: `getInstance()`, `getNumberInstance()`, `getCurrencyInstance()`, `getPercentInstance()`, `getIntegerInstance()`, `getCompactNumberInstance()` — each with default-locale and locale-parameter overloads.
- Formatting converts a number to a `String`; parsing converts a `String` to a `Number` (via `parse()`, which throws checked `ParseException`).
- Locale affects grouping/decimal separators (e.g., `,` vs `.` vs space) and currency symbols/placement.
- `NumberFormat.parse()` on currency strips currency symbols/grouping characters automatically.
- Format classes (`NumberFormat`, `DecimalFormat`, etc.) are **not thread-safe** — don't store them in shared/static/instance variables.
- Use `int` or `BigDecimal` for money, not `double` (floating-point rounding errors).

## Localizing Dates
- `DateTimeFormatter.ofLocalizedDate/Time/DateTime(FormatStyle)` use `SHORT`, `MEDIUM`, `LONG`, or `FULL` styles based on locale.
- Append `.withLocale(locale)` to apply a specific locale to a formatter.

## Resource Bundles
- A resource bundle stores locale-specific key/value pairs, typically in `.properties` files.
- Naming convention: `BundleName_language_COUNTRY.properties` (country optional).
- Retrieval methods: `ResourceBundle.getBundle("name")` (default locale) or `ResourceBundle.getBundle("name", locale)`.
- `keySet()` returns all keys in the bundle; `getString(key)` throws `MissingResourceException` if the key isn't found anywhere in the hierarchy.
- **Resource bundle selection order** (requested locale → default locale → default bundle):
  1. `Bundle_requestedLang_requestedCountry.properties`
  2. `Bundle_requestedLang.properties`
  3. `Bundle_defaultLang_defaultCountry.properties`
  4. `Bundle_defaultLang.properties`
  5. `Bundle.properties`
  6. Otherwise, throw `MissingResourceException`
- Once a matching bundle is found, Java only looks within **that bundle's own hierarchy** (dropping components down to the base) for individual keys — it does NOT fall back to the default locale's bundles, only to the default (no-locale) bundle.
- `MessageFormat.format(pattern, args...)` substitutes indexed placeholders like `{0}`, `{1}` in resource bundle strings.
- The `Properties` class is like a `String`-keyed/valued `HashMap`; `getProperty(key)` and `getProperty(key, default)` support default values, but plain `get()` does not accept a default.

## Exam Essentials (Chapter Summary Points)
- Know all exceptions descend from `Throwable`; never catch `Error` subclasses; only catch `Exception` subclasses in application code.
- Distinguish checked vs. unchecked exceptions and the handle-or-declare rule.
- Understand `try` statement flow: needs `catch` and/or `finally`; catch-block ordering rules; multi-catch restrictions; `finally` always runs last.
- Understand try-with-resources: resources close in reverse declaration order; implicit `finally` runs before any explicit `catch`/`finally`.
- Know the difference between `throw` and `throws`, and rules for overriding methods that declare exceptions.
- Know valid locale string rules (lowercase language required, uppercase country optional) and locale-creation methods.
- Know how to format dates, numbers, currency, percentages, and messages, and how locale changes their output.
- Know the resource bundle lookup order and the value-selection hierarchy once a bundle is chosen.

# Chapter 2: Operators

## Types of Operators
- Java has three operator types: **unary** (1 operand), **binary** (2 operands), **ternary** (3 operands).
- Operators are not always evaluated left-to-right; precedence determines evaluation order.

## Operator Precedence (highest → lowest)
- Post-unary (`expr++`, `expr--`) — left-to-right
- Pre-unary (`++expr`, `--expr`) — left-to-right
- Other unary (`-`, `!`, `~`, `+`, `(type)`) — right-to-left
- Cast `(Type)reference` — right-to-left
- Multiplication/division/modulus (`*`, `/`, `%`) — left-to-right
- Addition/subtraction (`+`, `-`) — left-to-right
- Shift operators (`<<`, `>>`, `>>>`) — left-to-right
- Relational (`<`, `>`, `<=`, `>=`, `instanceof`) — left-to-right
- Equal/not equal (`==`, `!=`) — left-to-right
- Logical AND (`&`) — left-to-right
- Logical exclusive OR (`^`) — left-to-right
- Logical inclusive OR (`|`) — left-to-right
- Conditional AND (`&&`) — left-to-right
- Conditional OR (`||`) — left-to-right
- Ternary (`? :`) — right-to-left
- Assignment operators (`=`, `+=`, `-=`, etc.) — right-to-left
- Arrow operator (`->`) — right-to-left
- Parentheses can always override default precedence.

## Unary Operators
- `!` (logical complement) — flips a boolean's value; boolean only.
- `~` (bitwise complement) — inverts bits of a number; numeric only.
- `+` — indicates a positive number (numbers are positive by default anyway).
- `-` (negation) — reverses the sign of a numeric expression; numeric only.
- `++` / `--` — increment/decrement a numeric value by 1.
- `(type)` — casting operator.
- You cannot mix numeric negation/complement with boolean values, or logical complement with numeric values — these cause compile errors.
- Watch for `0`/`1` being used interchangeably with `false`/`true` — Java does **not** allow this.

## Increment/Decrement Operators
- **Pre-increment (`++w`)**: increases by 1, returns the *new* value.
- **Pre-decrement (`--x`)**: decreases by 1, returns the *new* value.
- **Post-increment (`y++`)**: increases by 1, returns the *original* value.
- **Post-decrement (`z--`)**: decreases by 1, returns the *original* value.
- Increment/decrement have high precedence and are applied before most binary operators.

## Binary Arithmetic Operators
- `+` addition, `-` subtraction, `*` multiplication, `/` division, `%` modulus (remainder after division).
- Multiplicative operators (`*`, `/`, `%`) have higher precedence than additive operators (`+`, `-`).
- All arithmetic operators apply to any primitive except `boolean`.
- Only `+` and `+=` apply to `String` values (concatenation).
- Parentheses can override the default order of operations.
- Parentheses must always be balanced; brackets `[]` cannot substitute for parentheses in Java.

## Division & Modulus
- Integer division truncates to the floor value (drops anything after the decimal).
- Modulus returns the remainder; for divisor `y`, result of a **positive** dividend is between `0` and `y-1`.
- The sign of the divisor is ignored in modulus; the sign of the dividend determines the sign of the result.
- Modulus can be applied to floating-point numbers (out of exam scope).

## Numeric Promotion Rules
1. If two values have different data types, Java automatically promotes the smaller to the larger type.
2. If one value is integral and the other floating-point, the integral value is promoted to the floating-point type.
3. `byte`, `short`, and `char` are promoted to `int` any time they're used with a binary arithmetic operator on a **variable** (not just a literal), even if neither operand is `int`.
4. After promotion, the resulting value has the same data type as the promoted operands.
- Exception: unary increment/decrement (`++`/`--`) do **not** trigger promotion — a `short` stays a `short`.
- Floating-point literals are `double` by default unless suffixed with `f`/`F`.

## Assigning Values & Casting
- The assignment operator (`=`) is evaluated **right to left**.
- Java auto-promotes smaller → larger types; casting is **required** for larger → smaller (narrowing) conversions.
- Casting is a unary operation — it applies only to the value immediately next to it unless parentheses group a larger expression.
- Casting an object only changes the *reference type*, not the underlying object; casting a primitive can actually change the value.
- Integer literals default to `int`; use `L`/`l` suffix for values that need to be `long`.
- **Overflow/underflow**: values that don't fit their data type wrap around (e.g., max `int` + 1 becomes the most negative `int`).
- Literal values that fit into a smaller type compile fine without casting; **variables** always require explicit casting for narrowing conversions (compiler ambiguity with variables vs. literals).

## Compound Assignment Operators
- `+=`, `-=`, `*=`, `/=` (and others) combine an operation with assignment.
- The left side must already be a declared variable — compound operators cannot declare a new variable.
- Compound operators implicitly cast the result back to the left-hand variable's type, avoiding an explicit cast.

## Return Value of Assignment
- An assignment expression itself evaluates to the assigned value (e.g., `(wolf = 3)` both sets `wolf` and evaluates to `3`).
- Exam trap: assignment (`=`) can be mistakenly placed where equality (`==`) was intended, especially inside `if` statements.

## Equality Operators
- `==` and `!=` compare primitives by value and objects/references by identity (same object or both `null`).
- Cannot mix incompatible types (e.g., boolean vs. numeric, numeric vs. String) — compile error.
- `null == null` evaluates to `true` in Java.

## Relational Operators
- `<`, `<=`, `>`, `>=` apply only to numeric values; smaller operand types are promoted as needed.
- `instanceof` checks whether a reference is an instance of a given class/interface (or record, enum, annotation).
- Compiler flags `instanceof` as a compile error if the types are provably incompatible.
- `instanceof` on a `null` literal or `null` reference always returns `false`.
- `null instanceof null` does **not** compile.
- Good practice: use `instanceof` before casting to a narrower reference type.

## Logical Operators (`&`, `|`, `^`) — Boolean or Bitwise
- Applied to `boolean` → **logical** operators; applied to numeric types → **bitwise** operators.
- `&` (AND): true only if both operands are true.
- `|` (inclusive OR): true if at least one operand is true.
- `^` (exclusive OR): true only if exactly one operand is true (operands differ).
- Bitwise: a number `&` itself or `|` itself returns the same number.
- A number `&` its bitwise negation (`~`) is `0`; a number `|` its negation is `-1`.
- A number `^` itself is `0`; a number `^` its negation is `-1`.

## Conditional (Short-Circuit) Operators (`&&`, `||`)
- `&&`: if the left side is `false`, the right side is never evaluated.
- `||`: if the left side is `true`, the right side is never evaluated.
- Commonly used to avoid `NullPointerException` (e.g., `duck != null && duck.getAge() < 5`).
- Watch for **unperformed side effects**: code on the right side (like `++x`) may never execute if short-circuited.

## Ternary Operator (`? :`)
- The only operator with three operands: `booleanExpression ? expression1 : expression2`.
- Condensed form of if/else that returns a value.
- The two result expressions don't need matching data types, unless the overall result is being assigned to a typed variable.
- Parentheses improve readability, especially with nested ternary expressions.
- Like `&&`/`||`, only one of the two result expressions is evaluated — watch for unperformed side effects (e.g., `sheep++` vs `zzz++`).

## Exam Essentials
- Be able to write code that correctly uses all Java operator symbols.
- Recognize which operators apply to which data types (numeric-only, boolean-only, object-only).
- Understand when casting is required (narrowing) vs. automatic (widening/promotion).
- Understand full Java operator precedence order.
- Be able to use parentheses to override default operator precedence.

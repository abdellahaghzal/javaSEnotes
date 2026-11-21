# Chapter 2: Operators

## Types of Operators
- Java has three operator types: **unary** (1 operand), **binary** (2 operands), **ternary** (3 operands).
- The only ternary operator is the conditional `? :`; assignment (`=`), compound assignment (`*=`), `&&`, and `&` are binary, and `++` is unary.
- Operators are not always evaluated left-to-right; precedence determines evaluation order.
- Unary operators have higher precedence than binary and ternary operators, so they are applied before the surrounding binary or ternary operators.

## Operator Precedence (highest → lowest)
- Post-unary (`expr++`, `expr--`) — left-to-right
- Pre-unary (`++expr`, `--expr`) — left-to-right
- Other unary (`-`, `!`, `~`, `+`, `(type)` cast) — right-to-left
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
- Operators on the same line of the list (such as `*`, `/`, `%`) have equal precedence and are evaluated left-to-right.
- Because `&` ranks above `^`, which ranks above `|`, an expression like `a & b ^ c` is evaluated as `(a & b) ^ c`.
- Assignment has the lowest precedence of all, so `=` is always applied last, after the right side is fully evaluated.
- To rank operators, remember the groups: unary/cast, then multiplicative, additive, relational, equality, logical, short-circuit, ternary, and finally assignment (for example, `!` > `*` > `+` > `==` > `&` > `||` > `=`).

## Unary Operators
- `!` (logical complement) — flips a boolean's value; boolean only.
- `~` (bitwise complement) — inverts bits of a number; numeric only.
- `+` — indicates a positive number (numbers are positive by default anyway).
- `-` (negation) — reverses the sign of a numeric expression; numeric only.
- `++` / `--` — increment/decrement a numeric value by 1.
- `(type)` — casting operator.
- You cannot mix numeric negation/complement with boolean values, or logical complement with numeric values — these cause compile errors (`-true` and `!5` do not compile).
- Watch for `0`/`1` being used interchangeably with `false`/`true` — Java does **not** allow this.
- Operators that can be used with `boolean` values: `==`, `!=`, `!`, `&`, `|`, `^`, `&&`, `||`, `? :`, and `=`; arithmetic operators (`+`, `-`, `%`, `++`, `--`) and numeric casts cannot.

## Increment/Decrement Operators
- **Pre-increment (`++w`)**: increases by 1, returns the *new* value.
- **Pre-decrement (`--x`)**: decreases by 1, returns the *new* value.
- **Post-increment (`y++`)**: increases by 1, returns the *original* value.
- **Post-decrement (`z--`)**: decreases by 1, returns the *original* value.
- Increment/decrement have high precedence and are applied before most binary operators.
- Assigning a post-increment back to the same variable (`x = x++;`) leaves the variable unchanged, because the original value is returned and then overwrites the increment.
- Likewise, `b = (condition ? a : b++)` ends with `b` equal to the value returned by the ternary, since the assignment happens after the increment.

## Binary Arithmetic Operators
- `+` addition, `-` subtraction, `*` multiplication, `/` division, `%` modulus (remainder after division).
- Multiplicative operators (`*`, `/`, `%`) have higher precedence than additive operators (`+`, `-`).
- All arithmetic operators apply to any primitive except `boolean`.
- Only `+` and `+=` apply to `String` values (concatenation).
- Parentheses `( )` are the only symbols that change the order of operations; brackets `[ ]`, braces `{ }`, angle brackets `< >`, and quotes cannot substitute for them.
- Parentheses must always be balanced.

## Division & Modulus
- Integer division drops anything after the decimal point (it truncates toward zero rather than rounding).
- Modulus returns the remainder; for a **positive** dividend and divisor `y`, the result is between `0` and `y-1`.
- The sign of the divisor is ignored in modulus; the sign of the dividend determines the sign of the result (for example, `6 % -3` is `0` and `7 % -3` is `1`).
- Any value modulus `1` is `0`, so in `9 % height` where `height` is `1`, the result is `0`.
- Modulus can be applied to floating-point numbers (out of exam scope).

## Numeric Promotion Rules
1. If two values have different data types, Java automatically promotes the smaller to the larger type.
2. If one value is integral and the other floating-point, the integral value is promoted to the floating-point type.
3. `byte`, `short`, and `char` are promoted to `int` any time they're used with a binary arithmetic operator, even if neither operand is `int`.
4. After promotion, the resulting value has the same data type as the promoted operands.
- Because of rule 3, `short + short`, `byte * byte`, and `byte + short` all produce an `int`, so assigning the result to a `short` or `byte` variable requires a cast (unless the expression is a compile-time constant that fits).
- A cast on each operand does not help: `(byte) a * (byte) b` is still promoted to `int`, so storing it in a `short` without casting the whole result does not compile.
- A binary expression combining an `int` and a `long` produces a `long`; combining `long` with `float` or `double` produces a floating-point value.
- Adding a `long` and a `float` yields a `float`, and a `float` cannot be returned or assigned to a `long` or `int` without an explicit cast.
- Exception: unary increment/decrement (`++`/`--`) do **not** trigger promotion — a `short` stays a `short`.
- Floating-point literals are `double` by default unless suffixed with `f`/`F`.

## Assigning Values & Casting
- The assignment operator (`=`) is evaluated **right to left**.
- Java auto-promotes smaller → larger types; casting is **required** for larger → smaller (narrowing) conversions.
- Returning a value from a method follows the same rules as assignment, so a `float` cannot be returned from a method declared to return `long` without a cast.
- Casting is a unary operation — it applies only to the value immediately next to it unless parentheses group a larger expression (`(int)a + b` casts only `a`, while `(int)(a + b)` casts the sum).
- Casting an object only changes the *reference type*, not the underlying object; casting a primitive can actually change the value.
- Integer literals default to `int`; use `L`/`l` suffix for values that need to be `long`.
- **Overflow/underflow**: values that don't fit their data type wrap around (e.g., max `int` + 1 becomes the most negative `int`).
- Casting an out-of-range value wraps it too: `(byte)(Byte.MAX_VALUE + 1)` is `-128`, because `127 + 1` is `128`, which wraps to `-128`.
- Literal values that fit into a smaller type compile fine without casting; **variables** always require explicit casting for narrowing conversions (compiler ambiguity with variables vs. literals).
- A cast can be chained (`(long)(int)(short)2`), and casting to a wider type is always allowed.

## Compound Assignment Operators
- `+=`, `-=`, `*=`, `/=` (and others) combine an operation with assignment.
- The left side must already be a declared variable — compound operators cannot declare a new variable.
- Compound operators implicitly cast the result back to the left-hand variable's type, avoiding an explicit cast (for example, `long goat = 2; goat -= 1.0;` compiles and `goat` becomes `1`).
- In `x += expression`, the right side is evaluated completely first, including any side effects such as `y++`, before being added to the original value of `x`.
- Example: with `sold = 3` and `taken = 1`, `sold += 1 + taken++;` makes `sold` equal to `5` and `taken` equal to `2`.

## Return Value of Assignment
- An assignment expression itself evaluates to the assigned value (e.g., `(wolf = 3)` both sets `wolf` and evaluates to `3`), and it is never `void`.
- This means `(wolf = false)` can be used inside a larger expression, and both the variable and the expression take the assigned value.
- Exam trap: assignment (`=`) can be mistakenly placed where equality (`==`) was intended, especially inside `if` statements.

## Equality Operators
- `==` and `!=` compare primitives by value and objects/references by identity (same object or both `null`).
- Both operators can be used with booleans, numbers, and objects, but the two sides must be compatible.
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
- `&` and `|` always evaluate **both** sides, unlike `&&` and `||`, which may skip the right side.
- Bitwise: a number `&` itself or `|` itself returns the same number.
- A number `&` its bitwise negation (`~`) is `0`; a number `|` its negation is `-1`.
- A number `^` itself is `0`; a number `^` its negation is `-1`.

## Conditional (Short-Circuit) Operators (`&&`, `||`)
- `&&`: if the left side is `false`, the right side is never evaluated.
- `||`: if the left side is `true`, the right side is never evaluated.
- Commonly used to avoid `NullPointerException` (e.g., `duck != null && duck.getAge() < 5`).
- Watch for **unperformed side effects**: code on the right side (like `++x` or `x++`) may never execute if short-circuited, leaving the variable unchanged.
- Only the non-short-circuit `&` and `|` are guaranteed to run both sides; they are never limited to evaluating only the left side.

## Ternary Operator (`? :`)
- The only operator with three operands: `booleanExpression ? expression1 : expression2`.
- Condensed form of if/else that returns a value.
- The two result expressions don't need matching data types, unless the overall result is being assigned to a typed variable.
- Parentheses improve readability, especially with nested ternary expressions.
- Nested ternaries are evaluated right-to-left, so `a > b ? b < c ? b : 2 : 1` groups as `a > b ? (b < c ? b : 2) : 1`.
- Like `&&`/`||`, only one of the two result expressions is evaluated — watch for unperformed side effects (e.g., `sheep++` vs `zzz++`).

## Tracing Tips for Exam Code
- Evaluate each line in order, keeping a running value for every variable, and apply post-increment/decrement side effects after the original value is used.
- Check each line for compile errors first (narrowing without a cast, mixing boolean and numeric, redeclaring variables) before computing any output.
- When a short-circuit operator or ternary skips a side, do not update any variable that appears only on the skipped side.

## Exam Essentials
- Be able to write code that correctly uses all Java operator symbols.
- Recognize which operators apply to which data types (numeric-only, boolean-only, object-only).
- Understand when casting is required (narrowing) vs. automatic (widening/promotion).
- Understand full Java operator precedence order.
- Be able to use parentheses to override default operator precedence.

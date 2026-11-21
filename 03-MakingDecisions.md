# Chapter 3: Making Decisions

## Exam Essentials

- **Understand if and else decision control statements.** The if and else statements come up frequently throughout the exam in questions unrelated to decision control, so make sure you fully understand these basic building blocks of Java.

- **Apply pattern matching and flow scoping to if.** Pattern matching can be used to reduce boilerplate code of some if statements, by applying the instanceof operator and a variable type/name. It can also include a guard, which is an optional conditional clause, after the pattern variable declaration. Pattern matching uses flow scoping in which the pattern variable is in scope as long as the compiler can definitively determine its type.

- **Understand switch statements and their proper usage.** You should be able to spot a poorly formed switch statement on the exam. The switch value and data type should be compatible with the case clauses, and the values for the case clauses must evaluate to compile-time constants. At runtime, a switch statement branches to the first matching case, or default if there is no match, or exits entirely if there is no match and no default branch. The process then continues into any subsequent case or default clause until a break or return statement is reached.

- **Use switch expressions correctly.** Discern the differences between switch statements and switch expressions. Understand how to write switch expressions correctly, including proper use of semicolons, writing case expressions and blocks that yield a consistent value, and making sure all possible values of the switch variable are handled by the switch expression.

- **Apply pattern matching to switch.** Understand how switch statements and expressions support pattern matching and allow any object to be used as the switch variable. It also supports a case branch with a guard, via the when keyword. Pattern matching alters two common rules with switch: a switch statement now must be exhaustive when pattern matching is used, and the ordering of switch expression branches is now important.

- **Write while loops.** Know the syntactical structure of all while and do/while loops. In particular, know when to use one versus the other.

- **Be able to use for loops.** You should be familiar with for and for-each loops and know how to write and evaluate them. Each loop has its own special properties and structures. You should know how to use for-each loops to iterate over lists and arrays.

- **Understand how break, continue, and return can change flow control.** Know how to change the flow control within a statement by applying a break, continue, or return statement. Also know which control statements can accept break statements and which can accept continue statements. Finally, you should understand how these statements work inside embedded loops or switch statements.

## Other Notable Bullet-Style Points from the Chapter

### Three ways to write an exhaustive switch
1. Add a default clause.
2. If the switch takes an enum, add a case clause for every possible enum value.
3. Cover all possible types of the switch variable with pattern matching.
- A switch expression must be exhaustive, so a switch expression on an `int` or `String` without a `default` does not compile.
- A switch statement only has to be exhaustive when it uses pattern matching or `case null`.
- A default clause is still allowed when every enum value is already covered.
- Switching over a type that is not sealed (such as `Number` or `Object`) requires a default clause or an unconditional pattern to be exhaustive.

### Right side of a for-each loop must be one of the following
- A built-in Java array (including multidimensional arrays such as `Double[][]`, whose elements are arrays)
- An object whose type implements `java.lang.Iterable`
- `List`, `Set`, and other `Collection` types work; `Map`, `String`, `Object`, and exceptions do not, because they are not arrays or `Iterable`.
- With `var` in a for-each loop, the variable takes the element type: `int[]` gives `int`, `Character[]` gives `Character`, and `List<Integer>` gives `Integer`.
- The loop variable can also be declared as a compatible type (for example, `Object` or `int` for a `List<Integer>`), since boxing, unboxing, and widening apply.

### Data types supported by switch
- int and Integer
- byte and Byte
- short and Short
- char and Character
- String
- enum values
- All object types (when used with pattern matching)
- var (if the type resolves to one of the preceding types)
- `long`, `float`, `double`, and `boolean` are not supported as switch variables.

### Table 3.1 — Supported control statement features
- **while** — Labels: Yes · break: Yes · continue: Yes · yield: No · when: No
- **do/while** — Labels: Yes · break: Yes · continue: Yes · yield: No · when: No
- **for** — Labels: Yes · break: Yes · continue: Yes · yield: No · when: No
- **switch** — Labels: Yes · break: Yes · continue: No · yield: Yes · when: Yes

### if/else rules
- An `else` always pairs with the closest unmatched `if`, regardless of indentation (the dangling else rule).
- Only one `else` may follow an `if`; a second `else` after an `else` does not compile (use `else if` for additional conditions).
- Using `=` instead of `==` in a condition compiles when the assigned value is a `boolean` (for example, `if (older = true)` always runs the true branch), but it does not compile for numeric assignments.
- The condition of an `if` and a loop must be a boolean expression enclosed in parentheses.
- Braces are optional for a single statement, but without them only the next statement belongs to the `if`.

### Pattern matching with instanceof and flow scoping
- A pattern variable is declared as `obj instanceof Type name`, and it is only in scope where the compiler can prove the match succeeded.
- With `&&`, the pattern variable is available on the right side of the operator (`o instanceof Integer i && i < 10` compiles).
- With `||`, the pattern variable is not definitely matched, so using it on the right side does not compile (`o instanceof Long l || l <= 20` fails).
- With a negated test (`!(o instanceof String s)`), the variable is in scope in the else branch, and in the code after the `if` when the true branch ends with `return` or `throw`.
- Declaring another pattern variable with the same name while the first is still in scope does not compile.
- There is no `default` keyword for if statements; the final fallback is a plain `else`.
- The same pattern variable name may be reused in separate branches only when it is not in scope in those branches.

### switch statement and case label rules
- Case values must be compile-time constants: literals, enum constants, or `final` variables initialized with a constant (including `final var one = 1;`).
- A non-final variable, or a `final` method parameter, cannot be used as a case value.
- Case values must be assignable to the switch variable type (an enum constant cannot be used in an `int` switch, and a `char` switch can use `final char` constants).
- Duplicate case values do not compile.
- The `default` clause can appear anywhere in the switch, but it only runs when no case matches; once reached, execution falls through to the clauses that follow it.
- Execution starts at the first matching case, and every following case runs until a `break`, `return`, or the end of the switch (fall-through).
- `String` cases are case sensitive, so `"VIOLIN"` does not match `"violin"`.
- `continue` cannot be used inside a switch unless the switch is inside a loop; `break` exits the switch.
- Multiple values may be grouped in one clause with a comma (`case 2, 10:`).
- Stacked labels such as `case 'B': case 'C':` share the same statements.

### switch expression rules
- A switch expression returns a value, so it can be assigned to a variable, returned, or used as an argument, and it ends with a semicolon after the closing brace when used as a statement-ending expression.
- An arrow clause with a single expression ends with a semicolon (`case 1 -> "Red";`), and a block arm does not need one.
- An arrow clause that uses a block must end with `yield value;` or `throw`; a block holding only an expression such as `{"Jane";}` does not compile.
- A stray semicolon after a block (`};`) inside the switch does not compile.
- `yield` may appear inside nested statements, such as an `if`, as long as every path yields a value or throws.
- Arrow clauses do not fall through, so no `break` is needed.
- A `default` arm may throw an exception (`default -> throw new RuntimeException();`).
- The yielded values must all be compatible with the type of the variable receiving the result (an `int` result can be stored in a `long`).
- A `byte` or `short` switch variable accepts `int` literals that fit its range as case values.

### Pattern matching in switch
- A type pattern needs a type and a variable name: `case Integer a ->`; writing `case a ->` is treated as a constant and does not compile.
- A guard is added with `when`: `case Integer a when a < 10 ->`.
- Constants such as `case 5` cannot be used when the switch variable is of type `Object`; use patterns instead.
- `case null` is allowed in a pattern switch, and it must not come after a `default` label.
- Negated patterns such as `case !(Number n)` are not valid syntax.
- Ordering matters: a more general pattern placed before a more specific one dominates it and causes a compile error; guarded patterns do not dominate later cases, but an unguarded `case Object o` would.
- A switch over `Object` or `Number` is not exhaustive without a `default`, because the compiler cannot cover every subtype.
- Pattern variables are scoped to their own case, so the same name can be reused in different cases.

### while and do/while loops
- A `while` condition is checked before each iteration, so the body may run zero times.
- A `do/while` body runs once before the condition is checked, so it always runs at least once.
- The condition of both loops must be in parentheses (`while keepGoing;` does not compile), and a `do/while` ends with a semicolon.
- A variable declared inside the `do` body is not in scope in the `while` condition, so using it there does not compile.
- If the variable the loop depends on is never updated inside the loop (or is updated only after the loop), the loop is infinite.
- An empty body `{}` or `;` is legal, and the loop may do all its work in the condition (`while ((x = x + 1) < 10) {}`).
- A post-increment in the condition (`animals++ <= 1`) uses the old value for the test and still increments when the loop exits.
- A pre-increment in the body (`++y`) prints the value after the increment.

### for loops
- A `for` loop has three optional parts: initialization, condition, and update (`for ( ; ; )` is an infinite loop).
- The initialization runs once, the condition is checked before each iteration, and the update runs after each iteration of the body.
- Variables declared in the initialization must share one type, and a second type cannot be declared in the same section (`int i = 0, int j = 3` does not compile).
- Variables declared in the initialization are only in scope inside the loop.
- Reading `array[array.length]` throws an ArrayIndexOutOfBoundsException because the last valid index is `length - 1`.
- Counting down correctly: `for (int i = a.length - 1; i >= 0; i--)`.
- Putting a pre-increment in the condition (`++i < 10`) skips the first value and changes the iterations.
- A loop whose update never changes the loop variable (such as resetting `w = r - 1` with `r` constant) never ends.

### break, continue, and labels
- `break` exits the innermost loop or switch; `continue` skips to the next iteration of the innermost loop.
- A label is an identifier followed by a colon placed before a statement (`OUTER: for (...)`).
- `break label` exits the labeled statement; `continue label` jumps to the next iteration of the labeled loop (in a `do/while`, to its condition check).
- A label can only be used by statements inside the labeled statement, so a label declared later or in another block is not in scope.
- Unreachable code after `break`, `continue`, `return`, or an infinite loop does not compile.
- If a `continue` targets an infinite inner loop itself, the loop never terminates; to stop it, break out of or continue an enclosing label.

### Tracing tips
- Check for compile errors first (syntax, scope, constants, unreachable code, type mismatches) before computing output.
- Track every variable after each loop iteration in a small table.
- Look for loops whose variables never change, and for conditions whose side effects run before the test.

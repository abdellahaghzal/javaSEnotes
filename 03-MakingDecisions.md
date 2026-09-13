# Chapter 3: Making Decisions

## Exam Essentials

- **Understand if and else decision control statements.** The if and else statements come up frequently throughout the exam in questions unrelated to decision control, so make sure you fully understand these basic building blocks of Java.

- **Apply pattern matching and flow scoping to if.** Pattern matching can be used to reduce boilerplate code of some if statements, by applying the instanceof operator and a variable type/name. It can also include a guard, which is an optional conditional clause, after the pattern variable declaration. Pattern matching uses flow scoping in which the pattern variable is in scope as long as the compiler can definitively determine its type.

- **Understand switch statements and their proper usage.** You should be able to spot a poorly formed switch statement on the exam. The switch value and data type should be compatible with the case clauses, and the values for the case clauses must evaluate to compile-time constants. At runtime, a switch statement branches to the first matching case, or default if there is no match, or exits entirely if there is no match and no default branch. The process then continues into any proceeding case or default clause until a break or return statement is reached.

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

### Right side of a for-each loop must be one of the following
- A built-in Java array
- An object whose type implements `java.lang.Iterable`

### Data types supported by switch
- int and Integer
- byte and Byte
- short and Short
- char and Character
- String
- enum values
- All object types (when used with pattern matching)
- var (if the type resolves to one of the preceding types)

### Table 3.1 — Supported control statement features
- **while** — Labels: Yes · break: Yes · continue: Yes · yield: No · when: No
- **do/while** — Labels: Yes · break: Yes · continue: Yes · yield: No · when: No
- **for** — Labels: Yes · break: Yes · continue: Yes · yield: No · when: No
- **switch** — Labels: Yes · break: Yes · continue: No · yield: Yes · when: Yes

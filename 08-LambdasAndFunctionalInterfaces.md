# Chapter 8: Lambdas and Functional Interfaces

## OCP Exam Objectives Covered
- Understand variable scopes, apply encapsulation, and create immutable objects. Use local variable type inference.
- Create and use interfaces, identify functional interfaces, and utilize private, static, and default interface methods.

## Lambda Syntax — Short Form Parts (e.g. `a -> a.canHop()`)
- A single parameter specified with the name `a`
- The arrow operator (`->`) to separate the parameter and body
- A body that calls a single method and returns the result of that method

## Lambda Syntax — Verbose Form Parts (e.g. `(Animal a) -> { return a.canHop(); }`)
- A single parameter specified with the name `a` and stating that the type is `Animal`
- The arrow operator (`->`) to separate the parameter and body
- A body that has one or more lines of code, including a semicolon and a return statement

## Lambda Syntax — Rules
- Parentheses around the parameter list are optional only when there is exactly one parameter and its type is not written; zero parameters (`() ->`) and two or more parameters (`(a, b) ->`) always need parentheses.
- Parameter types are optional, but they must be all explicit or all implicit; mixing a type and an implicit name (`(Cat a, b) ->`) does not compile.
- The keyword `var` can replace parameter types (`(var x, var y) ->`), but `var` cannot be mixed with explicit types (`(Wolf w, var c) ->` does not compile).
- With `var`, each parameter's type is inferred from the functional interface the lambda is passed to. For `Consumer<String>`, `(var x) -> {}` makes `x` a `String`, and for `BinaryOperator<Boolean>`, `(var x, var y) -> false` makes both `Boolean`. The same name in two different lambdas can therefore have different types.
- `var` is not a reserved word, so it is legal as a variable or lambda parameter name (for example `var -> p.age <= 10` compiles).
- `final` can be placed before an explicit parameter type (`(final Camel c) -> {}`).
- The arrow is `->`; `-->` and other forms do not compile.
- A body without braces is a single expression with no `return` keyword and no semicolon (`s -> s.isEmpty()`); using `return` without braces does not compile.
- A body with braces must end each statement with a semicolon, and must use `return value;` when a value is expected (`p -> {p.age < 5}` and `{ return "Poof" }` do not compile).
- A block body that has to return a value must `return` it on every path. A block such as `{ String g = ""; "Sleep: " + e }` (no `return`, no semicolon) does not compile.
- A lambda whose functional interface returns `void` may use an expression body that is a statement (a method call) or an empty block `{}`.
- A lambda body can return any expression, including an object such as `new RuntimeException()` (this does not throw it).
- A lambda body may contain any valid expression, such as a cast or a ternary: `(a, b) -> "Sleep: " + (double)(b == null ? a : a)`. Concatenating a `double` onto a `String` prints something like `Sleep: 10.0`.
- A lambda parameter cannot reuse the name of a local variable or another parameter already in scope, and a local variable declared inside the lambda body cannot reuse a parameter's name (`(e) -> { String e = ""; ... }` and `(a, b, c) -> { int b = 3; ... }` do not compile).
- This includes local variables of the enclosing method that were declared **before** the lambda. If `var s = ...` is already declared, then `s -> System.out.println(s)` is a duplicate variable error.
- Names from a different method (such as the parameter names of the method being called) are not in scope, so they do not conflict with a lambda's parameters.
- A lambda needs a target type: a lambda cannot be assigned to `var` (`var dino = s -> ...` does not compile), and it cannot be used where the type cannot be inferred.
- Lambda parameter types must match the functional interface: a `Predicate<String>` lambda cannot declare a `StringBuilder` parameter, and a lambda for `isTooHigh(int, int)` cannot call methods such as `append()` on an `int`.
- A lambda's return must be compatible with the interface's return type (a `String`-returning method cannot return `int`, and a `boolean` method needs a boolean expression).
- A lambda can be passed anywhere a matching functional interface is expected, including method arguments.
- A lambda can replace an implementing class only if the number and types of its parameters and its return type match the interface's single abstract method.

## Four Formats of Method References
- Static methods
- Instance methods on a particular object
- Instance methods on a parameter to be determined at runtime
- Constructors
- Examples: `Collections::sort` (static), `str::startsWith` (instance on a particular object), `String::isEmpty` (instance on a parameter), and `String::new` or `ArrayList::new` (constructor).
- `System.out::println` is equivalent to `x -> System.out.println(x)` and can replace a `Consumer<Object>` (or any `Consumer` whose type `println` accepts).
- A constructor reference with no arguments, such as `String::new`, matches `Supplier<String>`.
- The `::` operator cannot be chained (`System::out::println` does not compile).
- A method reference stands alone with no parentheses or arguments. `System.out.println(s)` is a method call, not a reference, and cannot be assigned to a functional interface.

## Functional Interface Rules
- A functional interface is an interface with exactly one abstract method (the single abstract method, or SAM).
- Default, static, private, and private static methods do not count toward the abstract method count.
- Abstract methods that match public methods of `java.lang.Object` (such as `equals(Object)`, `hashCode()`, and `toString()`) do not count toward the abstract method count.
- An overloaded method with different parameters (such as `hashCode(String)`) does count as another abstract method. So `interface Boat { int hashCode(); int hashCode(String input); }` is functional (one counting method).
- An interface that extends another interface inherits its abstract methods, so adding a second abstract method makes it non-functional. An interface that extends a functional interface without adding anything (`Train extends Transport`) is still functional.
- A functional interface must be an interface, not a class or abstract class.
- The `@FunctionalInterface` annotation is optional, but if present and the interface is not functional, it does not compile.
- A functional interface can contain default, static, and private methods, in addition to its one abstract method.
- A `default` method must have a body, so `default int blastOff();` does not compile (and an interface with such an error is not a valid functional interface).

## Common Built-in Functional Interfaces
- `Supplier<T>` — `T get()`; no parameters, returns a value.
- `Consumer<T>` — `void accept(T t)`; takes one value, returns nothing (good for printing an existing value).
- `BiConsumer<T, U>` — `void accept(T t, U u)`.
- `Predicate<T>` — `boolean test(T t)`; returns a primitive boolean.
- `BiPredicate<T, U>` — `boolean test(T t, U u)`.
- `Function<T, R>` — `R apply(T t)`.
- `BiFunction<T, U, R>` — `R apply(T t, U u)`; takes three type arguments.
- `UnaryOperator<T>` — `T apply(T t)`; extends `Function<T, T>`, so it takes one type argument.
- `BinaryOperator<T>` — `T apply(T t1, T t2)`; extends `BiFunction<T, T, T>`, so it takes one type argument.
- `UnaryOperator<Integer>` is equivalent to `Function<Integer, Integer>`, and `BinaryOperator<Integer>` is equivalent to `BiFunction<Integer, Integer, Integer>`.
- Generic type argument counts matter: `Function` needs two, `BiFunction` needs three, `UnaryOperator` and `BinaryOperator` need one. Forms such as `Function<Integer>`, `BiFunction<Integer, Integer>`, and `BinaryOperator<Integer, Integer>` do not compile.
- Names such as `BinaryConsumer`, `BinaryFunction`, `IntegerSupplier`, `CharSupplier`, `FloatSupplier`, and `StringSupplier` do not exist.
- Primitive versions exist for `boolean`, `double`, `int`, and `long` (for example, `BooleanSupplier`, `DoubleSupplier`, `IntSupplier`, `LongSupplier`, `IntPredicate`, `IntFunction`); the `char` and `float` types have none.
- Supplier primitive versions return a primitive (`getAsInt()`, `getAsDouble()`, `getAsBoolean()`, `getAsLong()`).
- The method names to remember: `get()` for Supplier, `accept()` for Consumer, `test()` for Predicate, and `apply()` for Function and its operator variants.
- `Predicate` has `test()` and returns a `boolean`, not an `int`. `Function` has `apply()`, not `test()`. `Supplier` produces a value and takes none, so it is not for printing an existing value.

## Example: Determine Functional Interface from Description
- Returns a String without taking any parameters — `Supplier<String>`
- Returns a Boolean and takes a String — `Function<String, Boolean>` (a `Predicate<String>` returns the primitive `boolean`)
- Returns an Integer and takes two Integers — `BinaryOperator<Integer>` (or `BiFunction<Integer, Integer, Integer>`)
- A lambda that takes one value and returns a value of the same type, such as `a -> a + a`, fits `UnaryOperator<String>`.
- A constructor reference such as `String::new` with no arguments fits `Supplier<String>`.
- When a variable is assigned `m.andThen(n)` and `m` and `n` are the same type as that variable, the type must have an `andThen()` method that accepts its own type: `BiConsumer<String, String>` (and `Consumer`) qualify.

## Default Methods on Functional Interfaces
- `Function` has `andThen()` and `compose()`: `f.andThen(g)` runs `f` first and then `g`, while `f.compose(g)` runs `g` first and then `f`.
- Example: with `s = a -> a + 4` and `t = a -> a * 3`, `s.compose(t).apply(1)` computes `t` first (3) and then `s` (7).
- `Consumer` and `BiConsumer` have `andThen()`, which takes another `Consumer`/`BiConsumer` of the same kind and runs both in order.
- `BiFunction` has `andThen()` but it takes a `Function`, not another `BiFunction`.
- `Predicate` and `BiPredicate` have `and()`, `or()`, and `negate()`.
- `UnaryOperator` inherits `andThen()` and `compose()` from `Function`.

## Rules for Accessing Variables from a Lambda Body (Table 8.8 summary)
- Instance variable — Allowed
- Static variable — Allowed
- Local variable — Allowed if final or effectively final
- Method parameter — Allowed if final or effectively final
- Lambda parameter — Allowed
- A local variable that is reassigned anywhere in the method, before or after the lambda, is not effectively final and cannot be used in the lambda (assigning `age = 2;` after the lambda breaks it).
- Variables declared inside the lambda are local to it, so the same name can be declared again in the enclosing method after the lambda ends, but not before it.
- A lambda cannot declare a parameter or local variable with the same name as a local variable or parameter already in scope in the method.
- Private fields of a class can be read in a lambda written inside that same class (and read through an object of that class, such as `h.age`).
- A local variable that is not used by the lambda may be reassigned freely.
- Where a new line is placed matters. A line added **after** a lambda may reuse a name declared inside the lambda (`char start = 'a';` or `char c = 'x';` after `c -> { char start = 'a'; ... }` is fine). The same line placed **before** the lambda conflicts with the name used in the lambda and does not compile.
- Reassigning a variable that the lambda uses (`end = '1';`, before or after the lambda) does not compile. Reassigning one the lambda never uses (`chars = null;`) compiles.
- For a loop, a variable declared inside the loop body (such as `int j = i;`) is effectively final if it is never changed, so a lambda can use it, even though the loop counter `i` could not be used.
- The same variable name can appear in separate `if` and `else` blocks (each is its own scope), so declaring `Supplier<Integer> supplier` in both is fine.

## Exam Essentials
- **Write simple lambda expressions.** Look for the presence or absence of optional elements in lambda code. Parameter types are optional. Braces, a semicolon, and the return keyword are optional when the body is a single statement. Parentheses are optional when only one parameter is specified and the type is implicit.
- **Determine whether a variable can be used in a lambda body.** Local variables and method parameters must be final or effectively final to be referenced. This means the code must compile if you were to add the final keyword to these variables. Instance and class variables are always allowed. Also check that no lambda parameter or lambda local variable reuses the name of a variable already in scope.
- **Translate method references to the "long-form" lambda.** Be able to convert method references into regular lambda expressions, and vice versa. For example, `System.out::print` and `x -> System.out.print(x)` are equivalent. Remember that the order of method parameters is inferred for method references.
- **Determine whether an interface is a functional interface.** Use the single abstract method (SAM) rule to determine whether an interface is a functional interface. Other interface method types (default, private, static, and private static) do not count toward the single abstract method count, nor do any public methods with signatures found in Object.
- **Identify the correct functional interface given the number of parameters, return type, and method name — and vice versa.** The most common functional interfaces are Supplier, Consumer, Function, and Predicate. There are also binary versions and primitive versions of many of these methods. You can use the number of parameters and return type to tell them apart. Know how many generic type arguments each one takes.
- **Check the lambda syntax before tracing the code.** Look for missing semicolons, `return` without braces, the wrong arrow, mixed explicit and implicit parameter types, and a lambda assigned to `var`.

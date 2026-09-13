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

## Four Formats of Method References
- Static methods
- Instance methods on a particular object
- Instance methods on a parameter to be determined at runtime
- Constructors

## Example: Determine Functional Interface from Description
- Returns a String without taking any parameters
- Returns a Boolean and takes a String
- Returns an Integer and takes two Integers

## Rules for Accessing Variables from a Lambda Body (Table 8.8 summary)
- Instance variable — Allowed
- Static variable — Allowed
- Local variable — Allowed if final or effectively final
- Method parameter — Allowed if final or effectively final
- Lambda parameter — Allowed

## Exam Essentials
- **Write simple lambda expressions.** Look for the presence or absence of optional elements in lambda code. Parameter types are optional. Braces, a semicolon, and the return keyword are optional when the body is a single statement. Parentheses are optional when only one parameter is specified and the type is implicit.
- **Determine whether a variable can be used in a lambda body.** Local variables and method parameters must be final or effectively final to be referenced. This means the code must compile if you were to add the final keyword to these variables. Instance and class variables are always allowed.
- **Translate method references to the "long-form" lambda.** Be able to convert method references into regular lambda expressions, and vice versa. For example, `System.out::print` and `x -> System.out.print(x)` are equivalent. Remember that the order of method parameters is inferred for method references.
- **Determine whether an interface is a functional interface.** Use the single abstract method (SAM) rule to determine whether an interface is a functional interface. Other interface method types (default, private, static, and private static) do not count toward the single abstract method count, nor do any public methods with signatures found in Object.
- **Identify the correct functional interface given the number of parameters, return type, and method name — and vice versa.** The most common functional interfaces are Supplier, Consumer, Function, and Predicate. There are also binary versions and primitive versions of many of these methods. You can use the number of parameters and return type to tell them apart.

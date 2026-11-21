# Chapter 1: Building Blocks

## Major Components of Java (JDK key commands)
- **javac**: Converts .java source files into .class bytecode
- **java**: Executes the program
- **jar**: Packages files together
- **javadoc**: Generates documentation

## Simplified Rules for Java Files (main() section)
- Each file can contain only one public class.
- The filename must match the class name, including case, and have a .java extension.
- If the Java class is an entry point for the program, it must contain a valid main() method.
- A class, a reference, and an object are different things: in `Bunny bun = new Bunny();`, `Bunny` is a class, `bun` is a reference (a variable), and the object is what `new Bunny()` creates. `main` is a method, not a class or reference.

## Valid main() Method Rules
- The method must be named `main`, be `public` and `static`, and have a return type of `void`.
- The modifiers `public` and `static` can appear in either order, and `final` may be added (for example, `public static final void main(String[] args)`).
- Omitting `void`, omitting `public`, omitting `static`, or using `private` means the method is not a valid entry point.
- The parameter must be a String array, and its name can be anything (`args`, `x`, `time`), so a different parameter name never stops main() from running.

## Valid main() Parameter List Formats
- `String[] args`
- `String options[]`
- `String... friends`

## Order of Elements in a Java File
- The order must be: package declaration (optional), import statements (optional), then the class declaration.
- Imports cannot come before the package, and the class cannot come before either one.
- Valid combinations include: package, import, class; package, class; import, class; and class alone.

## Imports and Packages
- `java.lang` is imported automatically, so `import java.lang.*;` and `import java.lang.System;` are redundant but legal.
- Classes in the same package as the current class do not need to be imported, so importing them is redundant but legal.
- Redundant imports do not cause compiler errors.
- An explicit (single-class) import takes precedence over a wildcard import.
- If two wildcard imports contain the same class name and that name is used, the code does not compile because the reference is ambiguous.
- Two explicit imports of the same simple class name from different packages also do not compile.
- Importing one class with the same simple name as a class in another package is fine as long as only one of them is imported explicitly or by wildcard.

## Order of Initialization Rules
- Fields and instance initializer blocks are run in the order in which they appear in the file.
- The constructor runs after all fields and instance initializer blocks have run.
- Instance variables start with their default values, so an initializer block placed before a field is reassigned may print the default value (for example, 0) first.
- Example: with `count` defaulting to 0, an initializer block that prints `count` prints 0, a later block incrementing it makes it 1, and the constructor then overwrites it.

## Constructors
- A constructor has the same name as the class and has no return type.
- If a method has the class name but also a return type (for example, `public void PoliceBox()`), it is a regular method, not a constructor. It compiles, but it is never called automatically.
- If no constructor is declared, the compiler provides a default no-argument constructor, so instance variables keep their default values.

## Default Values for Variables
- Instance and class (static) variables are given default values automatically; local variables are not and must be initialized before use.
- boolean defaults to `false`.
- byte, short, int, and long default to `0`.
- float and double default to `0.0`.
- char defaults to `'\u0000'` (the null character), not the word null.
- Any object reference (including String) defaults to `null`, not the empty string.
- A local variable is never given a default, and reading it before assigning it is a compiler error.

## Key Points on Primitive Types (Table 1.5 discussion)
- The byte, short, int, and long types are used for integer values without decimal points.
- Each numeric type uses twice as many bits as the smaller similar type. For example, short uses twice as many bits as byte does.
- All of the numeric types are signed and reserve one of their bits to cover a negative range. For example, instead of byte covering 0 to 255 (or even 1 to 256), it actually covers -128 to 127.
- A float requires the letter f or F following the number so Java knows it is a float. Without an f or F, Java interprets a decimal value as a double, so `float temp = 50.0;` does not compile.
- A long requires the letter l or L following the number so Java knows it is a long. Without an l or L, Java interprets a number without a decimal point as an int in most scenarios.
- Assigning a larger type to a smaller type without a cast does not compile (for example, `short x = 5L;` and `int y = 2.0;` are errors).
- Primitives are not objects, so you cannot call methods on them (`numPets.length()` does not compile for a short).
- `length()` is a method on String, while `length` (no parentheses) is a field on arrays.

## Numeric Base Formats
- Octal (digits 0–7), which uses the number 0 as a prefix—for example, 017.
- Hexadecimal (digits 0–9 and letters A–F/a–f), which uses 0x or 0X as a prefix—for example, 0xFF, 0xff, 0XFf. Hexadecimal is case insensitive, so all of these examples mean the same value.
- Binary (digits 0–1), which uses the number 0 followed by b or B as a prefix—for example, 0b10, 0B10.

## Underscores in Numeric Literals
- Underscores may be placed only between two digits, and multiple consecutive underscores are allowed (`9___6` is legal).
- An underscore cannot be at the beginning or end of the literal (`_1_3_5_0` is not a number, and `3_13.0_` does not compile).
- An underscore cannot be placed right before or after the decimal point (`1_329_.0` and `5_291._2` do not compile).
- `3_1`, `2_234.0_0`, and `9___6` are examples of legal literals.

## Ways to Assign a Value to a Reference
- A reference can be assigned to another object of the same or compatible type.
- A reference can be assigned to a new object using the new keyword.
- When one reference is assigned to another (`p = q;`), both now point to the same object, and the object `p` previously pointed to may become eligible for garbage collection.

## Number Class Helper Methods (wrapper classes)
- byteValue()
- shortValue()
- intValue()
- longValue()
- floatValue()
- doubleValue()
- (Boolean and Character wrapper classes include booleanValue() and charValue(), respectively)

## Converting Strings to Numbers
- `Integer.parseInt(String)` returns a primitive `int`.
- `Integer.valueOf(String)` returns an `Integer` wrapper object.
- Both have an overload that takes a radix, for example `Integer.valueOf("B", 16)` reads hexadecimal B and returns 11.
- With `var`, the inferred type follows the return type, so `var n = Integer.parseInt("11");` is an int and `var m = Integer.valueOf("11");` is an Integer.
- A String that cannot be parsed in the given radix throws a NumberFormatException.

## Integer Helper Methods
- **max(int num1, int num2)**, which returns the largest of the two numbers
- **min(int num1, int num2)**, which returns the smallest of the two numbers
- **sum(int num1, int num2)**, which adds the two numbers

## Rules for Legal Identifiers
- Identifiers must begin with a letter, a currency symbol, or a _ symbol. Currency symbols include dollar ($), yuan (¥), euro (€), and so on.
- Identifiers can include numbers but not start with them (`1980_s` is illegal).
- A single underscore (_) is not allowed as an identifier.
- You cannot use the same name as a Java reserved word, including the literals `true`, `false`, and `null`.
- Identifiers cannot contain a dot, so `java.lang` is not a valid identifier.
- Identifiers are case sensitive, so `Public`, `Private`, and `Amount` are legal and different from `public`, `private`, and `amount`.
- Because of case sensitivity, a variable declared as `Depth` is not the same variable as `depth`.

## Rules for Using var
- `var` is only allowed for local variables (inside methods, constructors, or initializer blocks) that are initialized on the same line; it cannot be used for instance variables, class variables, method parameters, constructor parameters, or return types.
- The type of a `var` is determined at compile time and cannot change afterward, but its value can change as long as the new value is compatible with that type.
- `var x = null;` does not compile because the type cannot be inferred from null.
- Once a var is inferred as a primitive (for example, `var n = 2;` is an int), it cannot later be assigned null.
- A var may be assigned null later only if it was inferred as an object type (for example, `var s = "";` then `s = null;`).
- `var a = 1, b = 2;` and `var a = 1, b;` do not compile because var cannot be used in a multiple variable declaration.
- `var` is not a reserved word; it is a reserved type name, so it can still be used as a variable name.
- An expression such as `var day = 1/0;` compiles, since the type is int; it only fails at runtime with an ArithmeticException.

## Declaring Multiple Variables
- Multiple variables in one declaration must share one type: `int a = 0, b = 2;` is legal.
- You cannot mix types in a single declaration, so `int gills = 0, double weight = 2;` does not compile.
- Java does not support default parameter values, so a method like `void print(int length = 3)` does not compile.

## Text Blocks
- A text block starts with `"""` followed by a line break; content cannot begin on the same line as the opening delimiter.
- Everything between the delimiters is literal text, including double quotes and `+` signs, so no concatenation or variable evaluation takes place inside it.
- Incidental whitespace (the common leading indentation) is removed, and the position of the closing `"""` is included when computing it.
- Adding the same amount of indentation to every line does not change the output.
- Trailing whitespace at the end of each line is stripped.
- `\s` represents a single space and is preserved, which is how trailing spaces can be kept.
- A backslash `\` at the end of a line joins it with the next line, so no line break is added.
- A line break is included at the end of the content only when the closing `"""` is on its own line.
- If the closing delimiter is on the same line as the last text, no trailing newline is added.

## Rules on Variable Scope (Reviewing Scope)
- **Local variables**: In scope from declaration to the end of the block
- **Method parameters**: In scope for the duration of the method
- **Instance variables**: In scope from declaration until the object is eligible for garbage collection
- **Class variables**: In scope from declaration until the program ends
- A variable declared inside a loop or a plain `{ }` block is not in scope after the closing brace, so it cannot be used after the block ends.
- Variables declared in an instance initializer block are local to that block.
- When counting variables in scope at a line, include instance variables, method parameters, and local variables whose blocks are still open at that line.

## Ways an Object Becomes No Longer Reachable (Garbage Collection)
- The object no longer has any references pointing to it.
- All references to the object have gone out of scope.
- An object that is referenced only by other unreachable objects is also eligible (an island of isolation), so setting the outer reference to null can make both objects eligible.
- An object remains reachable while another reachable object still holds a reference to it, even if its own variable was set to null.

## Garbage Collection Facts
- `System.gc()` is only a suggestion; the JVM may or may not run garbage collection, and it is never guaranteed.
- Garbage collection does not run on a fixed schedule or at a set memory threshold.
- Garbage collection lets the JVM reclaim memory from unreachable objects so it can be reused for other objects.
- An object can be eligible for garbage collection but never actually removed from the heap, such as when the program ends first.
- Marking a variable `final` does not prevent its object from being garbage collected.

## Common Cases Where You Don't Need to Check Imports (Exam Formatting)
- Code that begins with a class name
- Code that begins with a method declaration
- Code that begins with a code snippet that would normally be inside a class or method
- Code that has line numbers that don't begin with 1

## Exam Essentials — Key Ability Points
- Be able to write code using a main() method.
- Understand the effect of using packages and imports.
- Be able to recognize a constructor.
- Be able to identify legal and illegal declarations and initialization.
- Understand how to create text blocks.
- Be able to use var correctly.
- Be able to determine where variables go into and out of scope.
- Know how to identify when an object is eligible for garbage collection.

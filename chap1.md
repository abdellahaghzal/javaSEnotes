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

## Valid main() Parameter List Formats
- `String[] args`
- `String options[]`
- `String... friends`

## Order of Initialization Rules
- Fields and instance initializer blocks are run in the order in which they appear in the file.
- The constructor runs after all fields and instance initializer blocks have run.

## Key Points on Primitive Types (Table 1.5 discussion)
- The byte, short, int, and long types are used for integer values without decimal points.
- Each numeric type uses twice as many bits as the smaller similar type. For example, short uses twice as many bits as byte does.
- All of the numeric types are signed and reserve one of their bits to cover a negative range. For example, instead of byte covering 0 to 255 (or even 1 to 256), it actually covers -128 to 127.
- A float requires the letter f or F following the number so Java knows it is a float. Without an f or F, Java interprets a decimal value as a double.
- A long requires the letter l or L following the number so Java knows it is a long. Without an l or L, Java interprets a number without a decimal point as an int in most scenarios.

## Numeric Base Formats
- Octal (digits 0–7), which uses the number 0 as a prefix—for example, 017.
- Hexadecimal (digits 0–9 and letters A–F/a–f), which uses 0x or 0X as a prefix—for example, 0xFF, 0xff, 0XFf. Hexadecimal is case insensitive, so all of these examples mean the same value.
- Binary (digits 0–1), which uses the number 0 followed by b or B as a prefix—for example, 0b10, 0B10.

## Ways to Assign a Value to a Reference
- A reference can be assigned to another object of the same or compatible type.
- A reference can be assigned to a new object using the new keyword.

## Number Class Helper Methods (wrapper classes)
- byteValue()
- shortValue()
- intValue()
- longValue()
- floatValue()
- doubleValue()
- (Boolean and Character wrapper classes include booleanValue() and charValue(), respectively)

## Integer Helper Methods
- **max(int num1, int num2)**, which returns the largest of the two numbers
- **min(int num1, int num2)**, which returns the smallest of the two numbers
- **sum(int num1, int num2)**, which adds the two numbers

## Rules for Legal Identifiers
- Identifiers must begin with a letter, a currency symbol, or a _ symbol. Currency symbols include dollar ($), yuan (¥), euro (€), and so on.
- Identifiers can include numbers but not start with them.
- A single underscore (_) is not allowed as an identifier.
- You cannot use the same name as a Java reserved word.

## Rules on Variable Scope (Reviewing Scope)
- **Local variables**: In scope from declaration to the end of the block
- **Method parameters**: In scope for the duration of the method
- **Instance variables**: In scope from declaration until the object is eligible for garbage collection
- **Class variables**: In scope from declaration until the program ends

## Ways an Object Becomes No Longer Reachable (Garbage Collection)
- The object no longer has any references pointing to it.
- All references to the object have gone out of scope.

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

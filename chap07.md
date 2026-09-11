# Chapter 7: Beyond Classes

## Annotations to Know for the Exam
- `@Deprecated` — marks a feature as no longer supported and possibly slated for removal; using it can trigger a compiler warning.
- `@SuppressWarnings` — tells the compiler to ignore warnings for a section of code.
- `@SafeVarargs` — indicates a method performs no unsafe operations on its varargs parameter.

## Implicit Modifiers for Interfaces
- Interfaces are implicitly abstract.
- Interface variables are implicitly public, static, and final.
- Interface methods without a body are implicitly abstract.
- Interface methods without the private modifier are implicitly public.

## Interface Member Types (Table 7.1 concepts)
- **Constant variable** — class membership; implicitly public/static/final; has a value.
- **Abstract method** — instance membership; implicitly public/abstract; no body.
- **Default method** — instance membership; requires `default`; implicitly public; has a body.
- **Static method** — class membership; requires `static`; implicitly public; has a body.
- **Private method** — instance membership; requires `private`; has a body.
- **Private static method** — class membership; requires `private static`; has a body.

## Default Interface Method Definition Rules
1. A default method may be declared only within an interface.
2. A default method must be marked with the `default` keyword and include a method body.
3. A default method is implicitly public.
4. A default method cannot be marked abstract, final, or static.
5. A default method may be overridden by a class that implements the interface.
6. If a class inherits two or more default methods with the same method signature, the class must override the method.

## Static Interface Method Definition Rules
1. A static method must be marked with the `static` keyword and include a method body.
2. A static method without an access modifier is implicitly public.
3. A static method cannot be marked abstract or final.
4. A static method is not inherited and cannot be accessed in an implementing class without referencing the interface name.

## Private Interface Method Definition Rules
1. A private interface method must be marked `private` and include a method body.
2. A private static interface method may be called by any method within the interface definition.
3. A private (non-static) interface method may only be called by default and other private non-static methods within the interface definition.

## Quick Tips for Interface Members (exam)
- Treat abstract, default, and non-static private methods as belonging to an instance of the interface.
- Treat static methods and variables as belonging to the interface class object.
- All private interface method types are only accessible within the interface declaration.

## Sealed Class Keywords
- **sealed** — indicates a class/interface may only be extended/implemented by named classes or interfaces.
- **permits** — used with `sealed` to list the allowed classes/interfaces.
- **non-sealed** — applied to a class/interface extending a sealed type, allowing extension by unspecified classes.

## Sealed Class Rules (Reviewing Sealed Class Rules)
- Sealed classes are declared with the `sealed` and `permits` modifiers.
- Sealed classes must be declared in the same package or named module as their direct subclasses.
- Direct subclasses of sealed classes must be marked `final`, `sealed`, or `non-sealed` (interfaces extending a sealed interface may only use `sealed` or `non-sealed`).
- The `permits` clause is optional if the sealed class and its direct subclasses are declared within the same file, or the subclasses are nested within the sealed class.
- Interfaces can be sealed to limit the classes that implement them or the interfaces that extend them.

## permits Clause Usage (Table 7.3 concepts)
- Direct subclasses in a different file from the sealed class → `permits` clause **required**.
- Direct subclasses in the same file as the sealed class → `permits` clause **permitted but not required**.
- Direct subclasses nested inside the sealed class → `permits` clause **permitted but not required**.

## Members Automatically Added to Records
- **Constructor** — parameters in the same order as the record declaration.
- **Accessor method** — one accessor per field.
- **equals()** — compares records field by field.
- **hashCode()** — consistent hash using all fields.
- **toString()** — prints each field in a readable format.

## Rules for Pattern Matching with Records
- If any field declared in the record is included, then all fields must be included.
- The order of fields must be the same as in the record.
- The names of the fields do not have to match.
- At compile time, the type of the field must be compatible with the type declared in the record.
- The pattern may not match at runtime if the record supports elements of various types.

## Nested Class Flavors
- **Inner class** — a non-static type defined at the member level of a class.
- **Static nested class** — a static type defined at the member level of a class.
- **Local class** — a class defined within a method body.
- **Anonymous class** — a special case of a local class that does not have a name.

## Inner Class Properties
- Can be declared public, protected, package, or private.
- Can extend a class and implement interfaces.
- Can be marked abstract or final.
- Can access members of the outer class, including private members.

## Static Nested Class Notes
- The nesting creates a namespace, since the enclosing class name must be used to refer to it.
- Can additionally be marked private or protected.
- The enclosing class can refer to the fields and methods of the static nested class.
- Nested records are implicitly static.

## Local Class Properties
- Do not have an access modifier.
- Can be declared final or abstract.
- Can include instance and static members.
- Have access to all fields and methods of the enclosing class (when defined in an instance method).
- Can access final and effectively final local variables.

## Nested Class Modifiers (Table 7.4 concepts)
- Access modifiers: Inner class — all; Static nested class — all; Local class — none; Anonymous class — none.
- `abstract`: Inner class — yes; Static nested class — yes; Local class — yes; Anonymous class — no.
- `final`: Inner class — yes; Static nested class — yes; Local class — yes; Anonymous class — no.

## Nested Class Access Rules (Table 7.5 concepts)
- Can include instance and static members? — Yes for all four (inner, static nested, local, anonymous).
- Can extend a class or implement any number of interfaces? — Yes for inner, static nested, and local; anonymous classes must have exactly one superclass or one interface.
- Can access instance members of enclosing class? — Yes for inner class; no for static nested class; yes for local/anonymous if declared in an instance method.
- Can access local variables of enclosing method? — N/A for inner/static nested; yes for local/anonymous if final or effectively final.

## Polymorphism — Ways an Object May Be Accessed
- A reference with the same type as the object.
- A reference that is a superclass of the object.
- A reference of an interface the object implements or inherits.

## Object vs. Reference — Summary Rules
1. The type of the object determines which properties exist within the object in memory.
2. The type of the reference to the object determines which methods and variables are accessible to the Java program.

## Casting Objects — Summary Rules
1. Casting a reference from a subtype to a supertype doesn't require an explicit cast.
2. Casting a reference from a supertype to a subtype requires an explicit cast.
3. At runtime, an invalid cast of a reference to an incompatible type results in a `ClassCastException`.
4. The compiler disallows casts to unrelated types.

## Exam Essentials
- **Be able to write code that creates, extends, and implements interfaces.** An interface may extend any number of interfaces and inherits their abstract methods; an interface cannot extend a class, nor can a class extend an interface; a class may implement any number of interfaces.
- **Know which interface methods an interface method can reference.** Non-static private, default, and abstract methods are associated with an instance; non-static private and default methods may reference any method within the interface; static methods can only reference other static members; private methods can only be referenced within the interface declaration.
- **Be able to create and use enum types.** The semicolon after values is optional for simple enums but required otherwise; enum constructors are implicitly private; if an enum declares an abstract method, each enum value must implement it.
- **Be able to recognize when sealed classes are being correctly used.** Use the correct modifier (`final`, `sealed`, or `non-sealed`); understand when the `permits` clause may be omitted.
- **Identify properly encapsulated classes.** Instance variables are private; access/update happens via methods; accessor/mutator methods are optional.
- **Understand records and know which members the compiler adds automatically.** Long constructor, accessor methods, `equals()`, `hashCode()`, `toString()`; recognize compact constructors (validation/transformation only, not field access); instance members outside the declaration don't compile; records work with pattern matching.
- **Be able to declare and use nested classes.** Inner classes require an outer class instance; static nested classes do not; local and anonymous classes cannot have an access modifier; anonymous classes are limited to extending one class or implementing one interface.
- **Understand polymorphism.** An object exists in memory in one concrete form but is accessible in many forms through reference variables; changing the reference type may grant access to new members, but those members always existed in memory.

# Chapter 7: Beyond Classes

## Annotations to Know for the Exam
- `@Deprecated` — marks a feature as no longer supported and possibly slated for removal; using it can trigger a compiler warning.
- `@SuppressWarnings` — tells the compiler to ignore warnings for a section of code.
- `@SafeVarargs` — indicates a method performs no unsafe operations on its varargs parameter.
- `@Override` — tells the compiler the method must override or implement a method (or, in a record, an accessor); a compiler error occurs if it does not.

## Implicit Modifiers for Interfaces
- Interfaces are implicitly abstract (writing `public abstract interface` is redundant but legal).
- Interface variables are implicitly public, static, and final, so they must be given a value.
- Interface methods without a body are implicitly abstract.
- Interface methods without the private modifier are implicitly public.
- An interface method with a body must be marked `default`, `static`, or `private`; a plain `int findMore() { return 2; }` does not compile.
- Interface members cannot be `protected` or package-private.
- A class that implements an interface method must declare it `public`, because the interface method is implicitly public and a class cannot reduce access.
- An interface cannot extend a class, and a class cannot extend an interface (a class uses `implements`); an interface can extend any number of interfaces.

## Interface Member Types (Table 7.1 concepts)
- **Constant variable** — class membership; implicitly public/static/final; has a value.
- **Abstract method** — instance membership; implicitly public/abstract; no body.
- **Default method** — instance membership; requires `default`; implicitly public; has a body.
- **Static method** — class membership; requires `static`; implicitly public; has a body.
- **Private method** — instance membership; requires `private`; has a body.
- **Private static method** — class membership; requires `private static`; has a body.
- An abstract method cannot be `private` or `final`, and an abstract method cannot have a body.

## Default Interface Method Definition Rules
1. A default method may be declared only within an interface.
2. A default method must be marked with the `default` keyword and include a method body.
3. A default method is implicitly public.
4. A default method cannot be marked abstract, final, or static.
5. A default method may be overridden by a class that implements the interface.
6. If a class inherits two or more default methods with the same method signature, the class must override the method.
- Inside the overriding method, a specific interface's default can be called with `InterfaceName.super.methodName()` (for example, `Swim.super.perform()`); `Swim.perform()`, `super.perform()`, and `super.Swim.perform()` do not work.

## Static Interface Method Definition Rules
1. A static method must be marked with the `static` keyword and include a method body.
2. A static method without an access modifier is implicitly public.
3. A static method cannot be marked abstract or final.
4. A static method is not inherited and cannot be accessed in an implementing class without referencing the interface name.

## Private Interface Method Definition Rules
1. A private interface method must be marked `private` and include a method body.
2. A private static interface method may be called by any method within the interface definition.
3. A private (non-static) interface method may only be called by default and other private non-static methods within the interface definition.
- A private interface method is not inherited, so an implementing class may declare a method with the same name and any return type without conflict.
- A static interface method cannot call an instance method (abstract, default, or private non-static) of the interface, and a private static method cannot call a non-static one.
- A stray extra semicolon after a method body is legal.

## Quick Tips for Interface Members (exam)
- Treat abstract, default, and non-static private methods as belonging to an instance of the interface.
- Treat static methods and variables as belonging to the interface class object.
- All private interface method types are only accessible within the interface declaration.
- A class that is not abstract must implement every abstract method it inherits from an interface or abstract class; defining a method with the same name but different parameters is an overload and does not satisfy the requirement.
- When an interface redeclares an abstract method inherited from a parent interface, the return type must be the same or covariant.

## Enums
- Enum values are listed first, separated by commas; a semicolon after the last value is optional for simple enums but required if the enum has fields, constructors, or methods after the values (`STRAWBERRY` followed by `public Flavors() {}` with no semicolon does not compile).
- Enum constructors are implicitly private; marking one `public` or `protected` does not compile.
- `values()` returns an array of all the constants in declaration order, and `ordinal()` returns the zero-based index of a constant.
- Enums can have fields, constructors, and methods, and each value can pass arguments to the constructor (for example, `MAMMAL(List.of(2, 4))`).
- If an enum declares an abstract method, every value must implement it, either with a constant-specific class body or the enum does not compile.
- A constant-specific class body can override a method, and it can use the enum's non-private fields.
- Enum values are printed by name exactly as declared (`INSECTS`, not `Insects`).
- Enums can be used in switch statements and expressions; in Java 21, case labels may also use qualified names such as `case Seasons.SPRING`.
- Switching on a `null` enum reference throws a `NullPointerException` unless the switch has a `case null` label.
- Enums cannot be extended and cannot extend another class.

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
- Every class listed in `permits` must actually extend (or implement) the sealed type; a permitted class that does not do so is a compile error.
- A direct subclass that is not marked `final`, `sealed`, or `non-sealed` does not compile.
- A sealed class can be extended by an abstract class, as long as that subclass is marked `sealed` or `non-sealed`.
- A `non-sealed` subclass lets any class extend it, so a sealed class can still be indirectly extended by classes that are not in its permits list.
- A sealed class may contain nested subclasses.
- A sealed class needs at least one permitted subclass (listed, in the same file, or nested).

## permits Clause Usage (Table 7.3 concepts)
- Direct subclasses in a different file from the sealed class → `permits` clause **required**.
- Direct subclasses in the same file as the sealed class → `permits` clause **permitted but not required**.
- Direct subclasses nested inside the sealed class → `permits` clause **permitted but not required**.

## Records — Declaration Rules
- A record declares its components in the header, such as `record Gecko(int age) {}`, and the compiler generates a private final field for each component.
- Records are implicitly final, so `final` is allowed but redundant, and they cannot be marked `abstract`.
- Records cannot extend any class (they implicitly extend `java.lang.Record`) but can implement interfaces.
- Records cannot declare instance variables or instance initializers; static fields, static methods, and static initializers are allowed, but a static field cannot reuse a component name.
- An accessor method can be overridden using the component name and no parameters (`public boolean fun() { ... }`) with a return type that matches the component, and `@Override` is allowed on it.
- A compact constructor has no parentheses (`public Reptile { ... }`), is used for validation or transformation, and can reassign its parameters (for example, `stripes = stripes + 1;`) but cannot assign `this.field` directly.
- A compact constructor may throw an exception, and the field values are assigned automatically at its end.
- A non-canonical (overloaded) record constructor must start by calling another constructor with `this(...)`, so assigning `this.size = 10` or throwing alone in the first line is not valid.
- In a record constructor with the full parameter list written out, every field must be assigned.
- Nested records are implicitly static, so they can access the enclosing class's static members but not its instance members.

## Members Automatically Added to Records
- **Constructor** — parameters in the same order as the record declaration.
- **Accessor method** — one accessor per field.
- **equals()** — compares records field by field.
- **hashCode()** — consistent hash using all fields.
- **toString()** — prints each field in a readable format.
- A no-argument constructor, mutator (setter) methods, and sort methods are not added.
- All of the generated members may be overridden or redeclared by the programmer.

## Rules for Pattern Matching with Records
- If any field declared in the record is included, then all fields must be included.
- The order of fields must be the same as in the record.
- The names of the fields do not have to match.
- At compile time, the type of the field must be compatible with the type declared in the record.
- The pattern may not match at runtime if the record supports elements of various types.
- `var` can be used for any component and always matches it, and a `var` or type pattern that covers the component is unconditional.
- Each pattern variable needs a unique name within the pattern (`Exhibit(var i, var i)` does not compile).
- A raw type such as `List` is allowed in a pattern, but `List<Integer>` for a component declared as `List<String>` is not.
- `null` cannot be used as a nested record pattern (`Walrus(null)` does not compile).
- A component of a primitive type must be matched using the same primitive type (`double`), not a wrapper (`Double`), and the reverse for wrapper components.
- In a switch, an unconditional pattern such as `case Family(var a, var b)` dominates every pattern after it, so later patterns and `default` do not compile.
- A pattern after `Object w &&` cannot call members that exist only on a subtype unless the type is declared as that subtype.

## Encapsulation
- Properly encapsulated classes have private instance variables, accessed and updated only through methods, while accessor and mutator methods themselves are optional.

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
- An instance requires an enclosing instance: `Outer.Inner in = new Outer().new Inner();` (not `Outer.new Inner()` or `new Outer().Inner()`).
- Since Java 16, inner classes may declare static members, and a static member of the inner class hides an outer member of the same name inside the inner class.
- Creating an inner class from a static method (such as `main()`) with only `new Inner()` does not compile, because no outer instance exists.
- Inside an inner class, `Outer.this.member` refers to the outer instance's member.
- A private constructor of an inner class can be called from the outer class, and the reverse.

## Static Nested Class Notes
- The nesting creates a namespace, since the enclosing class name must be used to refer to it.
- Can additionally be marked private or protected.
- The enclosing class can refer to the fields and methods of the static nested class.
- Nested records are implicitly static.
- A static nested class cannot access the instance variables or methods of the enclosing class directly; doing so is a compile error.
- It is created with `new Outer.Nested()` and does not need an outer instance.
- Each nested class compiles to its own bytecode file named `Outer$Nested.class`.

## Local Class Properties
- Do not have an access modifier.
- Can be declared final or abstract.
- Can include instance and static members.
- Have access to all fields and methods of the enclosing class (when defined in an instance method).
- Can access final and effectively final local variables.
- `Outer.this.field` accesses the outer class's field when the local class has its own field with the same name.
- A local abstract class can be extended by an anonymous class declared in the same method.

## Anonymous Classes
- An anonymous class is declared and instantiated in one expression, such as `new Spirit() {}`, and extends one class or implements one interface.
- It has no name, no access modifier, and cannot be `abstract` or `final`.
- A call to a static method through an object reference (`new Ghost().boo()`) uses the static method of the reference's type.

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
- A reference can hold `null`, and a method can be passed any subtype of its parameter type, but not an abstract class instance or an unrelated type such as `Object` or `String`.
- An abstract class cannot be instantiated with `new`, but a reference of the abstract type can hold a concrete subclass object.
- A `var` takes the type of the right side, so `var frog = new TurtleFrog();` is a `TurtleFrog`.

## Object vs. Reference — Summary Rules
1. The type of the object determines which properties exist within the object in memory.
2. The type of the reference to the object determines which methods and variables are accessible to the Java program.

## Casting Objects — Summary Rules
1. Casting a reference from a subtype to a supertype doesn't require an explicit cast.
2. Casting a reference from a supertype to a subtype requires an explicit cast.
3. At runtime, an invalid cast of a reference to an incompatible type results in a `ClassCastException`.
4. The compiler disallows casts to unrelated types (such as casting a class that does not extend the other to that class).

## Constructor Call Reminders
- `this()` and `super()` are statements at the start of a constructor, not expressions, so `this().age = 3;` does not compile.

## Exam Essentials
- **Be able to write code that creates, extends, and implements interfaces.** An interface may extend any number of interfaces and inherits their abstract methods; an interface cannot extend a class, nor can a class extend an interface; a class may implement any number of interfaces.
- **Know which interface methods an interface method can reference.** Non-static private, default, and abstract methods are associated with an instance; non-static private and default methods may reference any method within the interface; static methods can only reference other static members; private methods can only be referenced within the interface declaration.
- **Be able to create and use enum types.** The semicolon after values is optional for simple enums but required otherwise; enum constructors are implicitly private; if an enum declares an abstract method, each enum value must implement it.
- **Be able to recognize when sealed classes are being correctly used.** Use the correct modifier (`final`, `sealed`, or `non-sealed`); understand when the `permits` clause may be omitted.
- **Identify properly encapsulated classes.** Instance variables are private; access/update happens via methods; accessor/mutator methods are optional.
- **Understand records and know which members the compiler adds automatically.** Long constructor, accessor methods, `equals()`, `hashCode()`, `toString()`; recognize compact constructors (validation/transformation only, not field access); instance members outside the declaration don't compile; records work with pattern matching.
- **Be able to declare and use nested classes.** Inner classes require an outer class instance; static nested classes do not; local and anonymous classes cannot have an access modifier; anonymous classes are limited to extending one class or implementing one interface.
- **Understand polymorphism.** An object exists in memory in one concrete form but is accessible in many forms through reference variables; changing the reference type may grant access to new members, but those members always existed in memory.

# Chapter 5: Methods

## Method Declaration Rules

- The order of a method declaration is: access modifier, optional specifiers (such as `static` and `final`), return type, method name, parameter list, and an optional body.
- Optional specifiers such as `static` and `final` can appear in any order relative to each other and to the access modifier, but they must come before the return type (`final static void rain()` and `static final void sleet()` are both legal).
- The return type is required and must come right before the method name; `void` is a return type, so `public final int void snow()` and `void final ice()` do not compile.
- A method with a `void` return type may use `return;` or no return at all, and it cannot return a value.
- A method with a non-void return type must return a value compatible with that type (`return null;` does not compile for `int`, `return 9.0;` does not compile for `int`, and a bare `return;` does not compile).
- Method names follow the same rules as identifiers, and `default` is not an access modifier for a class method; omit the modifier for package access.
- Modifiers are case sensitive, so `Public` is not valid.
- Labels, such as `zzz:`, and types, such as `String`, cannot be used in place of a modifier.

## Access Modifiers (Four Choices)

- **private** – The method can be called only from within the same class.
- **Package Access** – The method can be called only from a class in the same package. There is no keyword for this; you simply omit the access modifier. Also called package-private or default access.
- **protected** – The method can be called only from a class in the same package or a subclass.
- **public** – The method can be called from anywhere.

## Rules for Creating a Method with a Varargs Parameter

- A method can have at most one varargs parameter.
- If a method contains a varargs parameter, it must be the last parameter in the list.
- The syntax is `Type... name`; forms such as `int nums...` or `...int nums` do not compile.
- Inside the method, a varargs parameter is an array, so `nums.length` gives the number of arguments passed.
- A varargs parameter may receive zero or more arguments, or an array that is passed directly (`new boolean[2]`), but an array initializer such as `{true, true}` cannot be passed as an argument.
- In `juggle(boolean b, boolean... b2)`, the first argument is required, so `juggle()` does not compile; `juggle(true, true, true)` and `juggle(true, new boolean[2])` both give `b2.length` of 2.

## Purposes of Static Methods

- For utility or helper methods that don't require any object state. Since there is no need to access instance variables, having static methods eliminates the need for the caller to instantiate an object just to call the method.
- For state that is shared by all instances of a class, like a counter. All instances must share the same state. Methods that merely use that state should be static as well.

## Static and Instance Members

- Static members belong to the class and are shared by all instances, while instance members belong to a specific object.
- A static method can be called using the class name or an object reference; calling it through a reference that is `null` still works and does not throw a `NullPointerException`.
- A static method cannot call an instance method or use an instance variable directly; it needs an object reference to do so.
- An instance method can call static methods and use static variables directly.
- Changing a static variable through any reference (`rope1.length = 2;`) changes it for every reference to the class, so the last assignment wins.
- Instance initializer blocks only run when an object is created; if no object is ever instantiated, they never run.
- Static initializer blocks (`static { }`) run once when the class is first loaded, in the order they appear in the file.
- Instance initializer blocks and instance variables are not accessible from static blocks, and static members are accessible from instance blocks.

## Applying Access Modifiers (Most Restrictive to Least Restrictive)

- **private**: Only accessible within the same class.
- **Package access**: private plus other members of the same package. Sometimes referred to as package-private or default access.
- **protected**: Package access plus access within subclasses.
- **public**: protected plus classes in other packages.
- Package access is more restrictive than protected, not more lenient.
- A class that is `public` can be seen from other packages, even if some of its members are private or package-private.
- A class in another package that is not a subclass can only access `public` members; it cannot use package-private (default) members, protected members, or private members.
- Constructors have access modifiers too: a constructor without a modifier is package-private, so `new Classroom(...)` from another package does not compile.
- Access modifiers can be used to expose only methods while hiding instance variables (public methods with private fields), but they cannot restrict access by class name pattern or to only some classes in the same package.

## Protected Access — Two Scenarios Where Rules Apply

- A member is used without referring to a variable. In this case, we are taking advantage of inheritance, and protected access is allowed.
- A member is used through a variable. In this case, the rules for the reference type of the variable are what matter. If it is a subclass, protected access is allowed. This works for references to the same class or a subclass.
- A class in a different package that is not a subclass cannot access a protected member, even a static one (`Classroom.teacherName` fails when called from an unrelated class in another package).

## Final Variables and Effectively Final

- Local variables, instance variables, static variables, and method parameters can all be marked `final`.
- `final` and `var` can be combined for a local variable (`final var x = 10;`).
- A final primitive cannot be reassigned; a final reference cannot point to another object, but the object it refers to can still be modified.
- A final instance variable must be assigned exactly once: at the declaration, in an instance initializer, or in every constructor.
- A final static variable must be assigned exactly once: at the declaration or in a static initializer block.
- Assigning a final variable a second time (for example, in two static blocks or after it was given a value at the declaration) does not compile.
- A final variable that is never assigned does not compile, and a static final variable cannot be assigned in `main()` or any method.
- A static block cannot assign a final instance variable, and an instance block cannot reassign a final variable that already has a value.
- A local variable is effectively final if it is assigned a value only once and never changed afterward, even if the `final` keyword is not written.
- A variable declared without a value and assigned exactly once later is still effectively final.
- Using `++`, `--`, or `+=` on a variable, or assigning it a second value, means it is not effectively final.
- Two different variables with the same name can exist in different non-overlapping scopes, and each is judged independently for effectively final.

## Static Imports

- The syntax is `import static`, not `static import`.
- A static import imports a static member (method or variable) or all static members with `*`: `import static java.util.Collections.*;` or `import static java.util.Collections.sort;`.
- You cannot static import a class name (`import static java.util.Collections;` does not compile), and you cannot include a method signature or parameter types.
- A regular import such as `import java.util.*;` imports classes, not static methods, so static methods like `sort()` need a static import to be called without the class name.
- Static members of a class imported with `import static` can be used by their simple name, such as `LENGTH`.

## Passing Data Among Methods

- Java is pass-by-value: a copy of the primitive value or of the reference is passed.
- Assigning a new value to a parameter (`x = -1;` or `a = new StringBuilder("a");`) does not change the caller's variable.
- Calling methods that change an object through a reference parameter (`b.append("b");`) changes the object the caller can also see.
- Strings are immutable, so a method cannot change the caller's String; a StringBuilder can be changed.
- Return values must be captured or they are lost.

## Autoboxing and Unboxing

- Autoboxing converts a primitive to its wrapper class, and unboxing converts a wrapper back to its primitive.
- A primitive can be boxed only into its own wrapper type (or `Object`): `int` boxes to `Integer`, so `final Long x = 6;` does not compile, but `final long x = 6;` does.
- Widening happens before boxing, so an `int` literal can be assigned to `long` or `double` but not to `Long` or `Double`.
- Unboxing a `null` wrapper throws a `NullPointerException`.

## Overloading Methods

- Overloaded methods have the same name but different parameter lists (the number, types, or order of parameters).
- Parameter names, return types, and access modifiers do not distinguish overloads; two methods with the same name and parameter types are duplicates, even if the return type differs.
- A method with a different name is not an overload.
- A varargs method `moo(int m, int... n)` can be overloaded with `moo(char ch)` or `moo(int... x)`, because the parameter lists differ.
- Java picks the most specific match in this order: exact match, then wider primitive, then autoboxing, then varargs.
- Example: with overloads for `byte`, `int`, `float`, and `Object`, a `short` argument picks `int` (widening), a `boolean` argument picks `Object` (boxing), and a `double` argument picks `Object` (a double cannot narrow to float).
- Example: calling `execute(100)` with `int`, `Integer`, `Object`, and varargs versions picks the `int` version; if the `int` version is removed, it picks `Integer`.
- A `long` argument has no `int` match (narrowing is not done) and falls to boxing as `Long`, which matches `Object` but not `Integer`.
- Java never does more than one conversion step (such as widening then boxing) except to match `Object`.

## Exam Essentials

- **Be able to identify correct and incorrect method declarations.** Be able to view a method signature and know if it is correct, contains invalid or conflicting elements, or contains elements in the wrong order.
- **Identify when a method or field is accessible.** Recognize when a method or field is accessible when the access modifier is: private, package (omitted), protected, or public.
- **Understand how to declare and use final variables.** Local, instance, and static variables may be declared final. Be able to understand how to declare them and how they can (or cannot) be used.
- **Be able to spot effectively final variables.** Effectively final variables are local variables that are not modified after being assigned. Given a local variable, be able to determine if it is effectively final.
- **Recognize valid and invalid uses of static imports.** Static imports import static members. They are written as import static, not static import. Make sure they are importing static methods or variables rather than class names.
- **Apply autoboxing and unboxing.** The process of automatically converting from a primitive value to a wrapper class is called autoboxing, while the reciprocal process is called unboxing. Watch for a NullPointerException when performing unboxing.
- **State the output of code involving methods.** Identify when to call static rather than instance methods based on whether the class name or object comes before the method. Recognize that instance methods can call static methods and that static methods need an instance of the object in order to call an instance method.
- **Recognize the correct overloaded method.** Exact matches are used first, followed by wider primitives, followed by autoboxing, followed by varargs. Assigning new values to method parameters does not change the caller, but calling methods on them can.

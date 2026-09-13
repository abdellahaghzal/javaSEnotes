# Chapter 5: Methods

## Access Modifiers (Four Choices)

- **private** – The method can be called only from within the same class.
- **Package Access** – The method can be called only from a class in the same package. There is no keyword for this; you simply omit the access modifier. Also called package-private or default access.
- **protected** – The method can be called only from a class in the same package or a subclass.
- **public** – The method can be called from anywhere.

## Rules for Creating a Method with a Varargs Parameter

- A method can have at most one varargs parameter.
- If a method contains a varargs parameter, it must be the last parameter in the list.

## Purposes of Static Methods

- For utility or helper methods that don't require any object state. Since there is no need to access instance variables, having static methods eliminates the need for the caller to instantiate an object just to call the method.
- For state that is shared by all instances of a class, like a counter. All instances must share the same state. Methods that merely use that state should be static as well.

## Applying Access Modifiers (Most Restrictive to Least Restrictive)

- **private**: Only accessible within the same class.
- **Package access**: private plus other members of the same package. Sometimes referred to as package-private or default access.
- **protected**: Package access plus access within subclasses.
- **public**: protected plus classes in other packages.

## Protected Access — Two Scenarios Where Rules Apply

- A member is used without referring to a variable. In this case, we are taking advantage of inheritance, and protected access is allowed.
- A member is used through a variable. In this case, the rules for the reference type of the variable are what matter. If it is a subclass, protected access is allowed. This works for references to the same class or a subclass.

## Exam Essentials

- **Be able to identify correct and incorrect method declarations.** Be able to view a method signature and know if it is correct, contains invalid or conflicting elements, or contains elements in the wrong order.
- **Identify when a method or field is accessible.** Recognize when a method or field is accessible when the access modifier is: private, package (omitted), protected, or public.
- **Understand how to declare and use final variables.** Local, instance, and static variables may be declared final. Be able to understand how to declare them and how they can (or cannot) be used.
- **Be able to spot effectively final variables.** Effectively final variables are local variables that are not modified after being assigned. Given a local variable, be able to determine if it is effectively final.
- **Recognize valid and invalid uses of static imports.** Static imports import static members. They are written as import static, not static import. Make sure they are importing static methods or variables rather than class names.
- **Apply autoboxing and unboxing.** The process of automatically converting from a primitive value to a wrapper class is called autoboxing, while the reciprocal process is called unboxing. Watch for a NullPointerException when performing unboxing.
- **State the output of code involving methods.** Identify when to call static rather than instance methods based on whether the class name or object comes before the method. Recognize that instance methods can call static methods and that static methods need an instance of the object in order to call an instance method.
- **Recognize the correct overloaded method.** Exact matches are used first, followed by wider primitives, followed by autoboxing, followed by varargs. Assigning new values to method parameters does not change the caller, but calling methods on them can.

# Chapter 6: Class Design

## Class Modifiers (Table 6.1)
- **final** — The class may not be extended.
- **abstract** — The class is abstract, may contain abstract methods, and requires a concrete subclass to instantiate.
- **sealed** — The class may only be extended by a specific list of classes.
- **nonsealed** — A subclass of a sealed class permits potentially unnamed subclasses.
- **static** — Used for static nested classes defined within a class.

## Constructors — Summary Rules
- A class can contain many overloaded constructors, provided the signature for each is distinct.
- The compiler inserts a default no-argument constructor if no constructors are declared.
- If a constructor calls `this()`, then it must be the first line of the constructor.
- Java does not allow cyclic constructor calls.

## Additional Constructor Rules (super/this)
- If a constructor calls `super()` or `this()`, then it must be the first line of the constructor.
- If the constructor does not contain a `this()` or `super()` reference, then the compiler automatically inserts `super()` with no arguments as the first line of the constructor.

## Initializing Classes — Order of Initialization
1. Initialize the superclass of X.
2. Process all static variable declarations in the order in which they appear in the class.
3. Process all static initializers in the order in which they appear in the class.

## Initializing Instances — Order of Initialization
1. Initialize Class X if it has not been previously initialized.
2. Initialize the superclass instance of X.
3. Process all instance variable declarations in the order in which they appear in the class.
4. Process all instance initializers in the order in which they appear in the class.
5. Initialize the constructor, including any overloaded constructors referenced with `this()`.

## Order of Initialization / final Fields — Summary Rules
- A class is initialized at most once by the JVM before it is referenced or used.
- All static final variables must be assigned a value exactly once, either when they are declared or in a static initializer.
- All final fields must be assigned a value exactly once, either when they are declared, in an instance initializer, or in a constructor.
- Non-final static and instance variables defined without a value are assigned a default value based on their type.
- The order of initialization is as follows: variable declarations, then initializers, and finally constructors.

## Rules for Overriding a Method (compiler checks)
1. The method in the child class must have the same signature as the method in the parent class.
2. The method in the child class must be at least as accessible as the method in the parent class.
3. The method in the child class may not declare a checked exception that is new or broader than the class of any exception declared in the parent class method.
4. If the method returns a value, it must be the same or a subtype of the method in the parent class, known as covariant return types.

## Hiding Static Methods — Additional (5th) Rule
5. The method defined in the child class must be marked as static if it is marked as static in a parent class.

## Rules for Abstract Classes/Methods
- Only instance methods can be marked abstract within a class, not variables, constructors, or static methods.
- An abstract class can include zero or more abstract methods, while a non-abstract class cannot contain any.
- A non-abstract class that extends an abstract class must implement all inherited abstract methods.
- Overriding an abstract method follows the existing rules for overriding methods that you learned about earlier in the chapter.

## Declaring an Immutable Class — Steps
1. Mark the class as final or make all of the constructors private.
2. Mark all the instance variables private and final.
3. Don't define any setter methods.
4. Don't allow referenced mutable objects to be modified.
5. Use a constructor to set all properties of the object, making a copy if needed.

## Exam Essentials
- Be able to write code that extends other classes. A Java class that extends another class inherits all of its public and protected methods and variables. If the class is in the same package, it also inherits all package members of the class. Classes that are marked final cannot be extended. Finally, all classes in Java extend java.lang.Object either directly or from a superclass.
- Be able to distinguish and use this, this(), super, and super(). To access a current or inherited member of a class, the this reference can be used. To access an inherited member, the super reference can be used. The super reference is often used to reduce ambiguity, such as when a class reuses the name of an inherited method or variable. The calls to this() and super() are used to access constructors in the same class and parent class, respectively.
- Evaluate code involving constructors. The first line of every constructor is a call to another constructor within the class using this() or a call to a constructor of the parent class using the super() call. The compiler will insert a call to super() if no constructor call is declared. If the parent class doesn't contain a no-argument constructor, an explicit call to the parent constructor must be provided. Be able to recognize when the default constructor is provided. Remember that the order of initialization is to initialize all classes in the class hierarchy, starting with the superclass. Then the instances are initialized, again starting with the superclass. All final variables must be assigned a value exactly once by the time the constructor is finished.
- Understand the rules for method overriding. Java allows methods to be overridden, or replaced, by a subclass if certain rules are followed: a method must have the same signature, be at least as accessible as the parent method, must not declare any new or broader exceptions and must use covariant return types. Methods marked final may not be overridden or hidden.
- Recognize the difference between method overriding and method overloading. Both method overloading and overriding involve creating a new method with the same name as an existing method. When the method signature is the same, it is referred to as method overriding and must follow a specific set of override rules to compile. When the method signature is different, with the method taking different inputs, it is referred to as method overloading, and none of the override rules is required. Method overriding is important to polymorphism because it replaces all calls to the method, even those made in a superclass.
- Understand the rules for hiding methods and variables. When a static method is overridden in a subclass, it is referred to as method hiding. Likewise, variable hiding is when an inherited variable name is reused in a subclass. In both situations, the original method or variable still exists and is accessible depending on where it is accessed and the reference type used. For method hiding, the use of static in the method declaration must be the same between the parent and child class. Finally, variable and method hiding should generally be avoided since it leads to confusing and difficult-to-follow code.
- Be able to write code that creates and extends abstract classes. In Java, classes and methods can be declared as abstract. An abstract class cannot be instantiated. An instance of an abstract class can be obtained only through a concrete subclass. Abstract classes can include any number of abstract and non-abstract methods, including zero. Abstract methods follow all the method override rules and may be defined only within abstract classes. The first concrete subclass of an abstract class must implement all the inherited methods. Abstract classes and methods may not be marked as final.
- Create immutable objects. An immutable object is one that cannot be modified after it is declared. An immutable class is commonly implemented with a private constructor, no setter methods, and no ability to modify mutable objects contained within the class.

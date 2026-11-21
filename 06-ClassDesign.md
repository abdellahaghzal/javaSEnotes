# Chapter 6: Class Design

## Class Modifiers (Table 6.1)
- **final** — The class may not be extended.
- **abstract** — The class is abstract, may contain abstract methods, and requires a concrete subclass to instantiate.
- **sealed** — The class may only be extended by a specific list of classes.
- **nonsealed** — A subclass of a sealed class permits potentially unnamed subclasses.
- **static** — Used for static nested classes defined within a class.
- A concrete class is any non-abstract class; it can be marked `final`, it does not have to be immutable, and it must implement every inherited abstract method.
- A class cannot be both `abstract` and `final`, because abstract classes must be extended.

## Inheritance Basics
- Java supports single inheritance for classes: a class can directly extend only one class, but it can implement any number of interfaces.
- Multiple inheritance, which Java does not allow for classes, is the ability of a class to have multiple direct superclasses.
- Every class inherits from `java.lang.Object`, either directly or through a superclass (this applies to classes, not to variables).
- If class B extends class A, then A is the superclass (parent) of B and B is the subclass (child) of A.
- If a class implements an interface, the class is a subtype of that interface.
- A subclass inherits public and protected members, and package members if it is in the same package; private members are not inherited.
- Which version of an overloaded method is called is decided at compile time using the reference type, while an overridden method is chosen at runtime using the actual object type.
- Casting an object to its parent type does not change which overridden method runs, because the actual object type decides.

## Modifier Combinations for Methods
- `static` and `final` can be combined, as can `private` and `static`, and `private` and `final`.
- `abstract` cannot be combined with `final`, `private`, or `static`, because an abstract method must be inherited and overridden by a subclass.
- An abstract method has no body and must be declared `abstract` (`boolean isBlind();` without a body in a class does not compile).

## Constructors — Summary Rules
- A class can contain many overloaded constructors, provided the signature for each is distinct.
- The compiler inserts a default no-argument constructor if no constructors are declared.
- If any constructor is declared (even a private one with parameters), the compiler does not add a default constructor.
- A constructor has no return type and must have the same name as the class (including case); a declaration like `public bird() {}` in class `Bird` does not compile, while `public Bird bird()` is an ordinary method.
- If a constructor calls `this()`, then it must be the first line of the constructor.
- Java does not allow cyclic constructor calls.
- `this()` and `super()` can only be called as the first statement of a constructor, never from methods, and not after other statements.
- A constructor cannot contain both `this()` and `super()`, since each must be the first line.
- `this(...)` selects the overloaded constructor that best matches its arguments using the normal overload rules (for example, a `short` argument prefers an `int` constructor over a `long` one, and `null` matches a `String` or other reference parameter).

## Additional Constructor Rules (super/this)
- If a constructor calls `super()` or `this()`, then it must be the first line of the constructor.
- If the constructor does not contain a `this()` or `super()` reference, then the compiler automatically inserts `super()` with no arguments as the first line of the constructor.
- If the parent class has no no-argument constructor (for example, it only has `Mammal(int age)`), then each child constructor, including the default one the compiler would insert, must call `super(...)` explicitly with matching arguments or it does not compile.
- A subclass that declares no constructor gets a default constructor that calls `super()`, which also fails to compile if the parent has no accessible no-argument constructor.
- A private constructor can be accessed only from within the same class (for example, from `main()` in that class or from another constructor with `this()`).
- A parent constructor always finishes before the child's constructor body runs, so its output (or side effects) comes first.
- Instance initializers and instance variable initializers run before the body of a constructor, in the order they appear, and run again for every constructor that does not call `this()`.
- When a constructor calls `this()`, the initializers run only once, in the constructor that eventually calls `super()` (implicitly or explicitly).

## Initializing Classes — Order of Initialization
1. Initialize the superclass of X.
2. Process all static variable declarations in the order in which they appear in the class.
3. Process all static initializers in the order in which they appear in the class.
- Static initialization happens only once, the first time the class is referenced or an instance is created, with the superclass before the subclass.
- Accessing a static member through a subclass name (`Scorpion.sb`) still triggers initialization of the subclass and its parent.

## Initializing Instances — Order of Initialization
1. Initialize Class X if it has not been previously initialized.
2. Initialize the superclass instance of X.
3. Process all instance variable declarations in the order in which they appear in the class.
4. Process all instance initializers in the order in which they appear in the class.
5. Initialize the constructor, including any overloaded constructors referenced with `this()`.
- For a hierarchy, the full order is: parent static, child static, parent instance initializers, parent constructor, child instance initializers, child constructor.
- Static initializers run only once, but instance initializers run again for each new object created.

## Order of Initialization / final Fields — Summary Rules
- A class is initialized at most once by the JVM before it is referenced or used.
- All static final variables must be assigned a value exactly once, either when they are declared or in a static initializer.
- All final fields must be assigned a value exactly once, either when they are declared, in an instance initializer, or in a constructor.
- A `final` field that is never given a value (such as `private final int antlers;` with no constructor setting it) causes a compiler error.
- Non-final static and instance variables defined without a value are assigned a default value based on their type.
- The order of initialization is as follows: variable declarations, then initializers, and finally constructors.

## Rules for Overriding a Method (compiler checks)
1. The method in the child class must have the same signature as the method in the parent class.
2. The method in the child class must be at least as accessible as the method in the parent class.
3. The method in the child class may not declare a checked exception that is new or broader than the class of any exception declared in the parent class method.
4. If the method returns a value, it must be the same or a subtype of the method in the parent class, known as covariant return types.
- The parameters of an overriding method must match exactly; "covariant" applies only to return types, not to parameters.
- An overriding method may declare a new unchecked exception (such as `RuntimeException`), but not a new or broader checked exception.
- If the parent method returns `void`, the overriding method must also return `void`.
- A method that is `final` cannot be overridden or hidden.
- Private methods are not inherited, so a child method with the same name is a new method, not an override, and none of the override rules apply (for example, the return type can differ).
- A child method with the same name but a different parameter list is an overload, not an override, and can have any return type and access.
- If a child method has the same signature but breaks an override rule (such as a different return type that is not covariant), it does not compile.

## Hiding Static Methods — Additional (5th) Rule
5. The method defined in the child class must be marked as static if it is marked as static in a parent class.
- Hiding follows the same rules as overriding for access, exceptions, and covariant return types, and a static method cannot be hidden by an instance method (or the reverse).
- A hidden method is chosen by the reference type, not the object type.
- Static methods can be called through a reference variable, and a `null` reference still works because the reference type is used.

## Overloading, Overriding, and Hiding Compared
- Overloaded methods must have different parameter lists and may have different return types and access.
- Overridden methods must have the same signature, but the return type may be covariant (a subtype), not necessarily identical.
- Hidden methods must also have the same signature.
- Parameter names, access modifiers, and return types alone do not make methods different overloads.

## Hiding Variables
- Variables are not overridden, only hidden: a field in the child with the same name as one in the parent creates a second variable.
- The variable that is accessed depends on the reference type, not the object type, so `Speedster s = new Cheetah(50); s.numSpots` reads the `Speedster` field.
- Inside the child, `this.numSpots` refers to the child's variable and `super.numSpots` to the parent's.
- Within a constructor, `numSpots = numSpots;` assigns the parameter to itself and changes no field.
- To set the parent's hidden field, use `super.numSpots = numSpots;`.
- Static variables and static methods behave the same way: `ref.name` uses the reference type, even if the object is a subclass, but an overridden instance method such as `setName()` runs the subclass version.
- `this.variableName` can be used from any instance method, but not from a static method.

## Rules for Abstract Classes/Methods
- Only instance methods can be marked abstract within a class, not variables, constructors, or static methods.
- An abstract class can include zero or more abstract methods, while a non-abstract class cannot contain any.
- A non-abstract class that extends an abstract class must implement all inherited abstract methods.
- Overriding an abstract method follows the existing rules for overriding methods that you learned about earlier in the chapter.
- An abstract class cannot be instantiated directly, but a reference of the abstract type can point to a concrete subclass object.
- A method call is checked at compile time against the reference type, so calling a method that exists only in the subclass through an abstract (parent) reference does not compile.
- Abstract classes and methods cannot be marked `final`.

## Declaring an Immutable Class — Steps
1. Mark the class as final or make all of the constructors private.
2. Mark all the instance variables private and final.
3. Don't define any setter methods.
4. Don't allow referenced mutable objects to be modified.
5. Use a constructor to set all properties of the object, making a copy if needed.
- A class cannot be immutable if it can be extended, so it must be `final` or have only private constructors (but private constructors are not required).
- An immutable class may have static variables, instance variables, and any number of constructors.
- A class with a non-final or non-private field is not immutable, and a class whose final field is never assigned does not compile.
- Mutable data may be read through getters only if the caller cannot modify it, which usually means returning a copy.
- An empty `final` class and a `final` class with only private final fields of unmodifiable objects are immutable.

## Exam Essentials
- Be able to write code that extends other classes. A Java class that extends another class inherits all of its public and protected methods and variables. If the class is in the same package, it also inherits all package members of the class. Classes that are marked final cannot be extended. Finally, all classes in Java extend java.lang.Object either directly or from a superclass.
- Be able to distinguish and use this, this(), super, and super(). To access a current or inherited member of a class, the this reference can be used. To access an inherited member, the super reference can be used. The super reference is often used to reduce ambiguity, such as when a class reuses the name of an inherited method or variable. The calls to this() and super() are used to access constructors in the same class and parent class, respectively.
- Evaluate code involving constructors. The first line of every constructor is a call to another constructor within the class using this() or a call to a constructor of the parent class using the super() call. The compiler will insert a call to super() if no constructor call is declared. If the parent class doesn't contain a no-argument constructor, an explicit call to the parent constructor must be provided. Be able to recognize when the default constructor is provided. Remember that the order of initialization is to initialize all classes in the class hierarchy, starting with the superclass. Then the instances are initialized, again starting with the superclass. All final variables must be assigned a value exactly once by the time the constructor is finished.
- Understand the rules for method overriding. Java allows methods to be overridden, or replaced, by a subclass if certain rules are followed: a method must have the same signature, be at least as accessible as the parent method, must not declare any new or broader exceptions and must use covariant return types. Methods marked final may not be overridden or hidden.
- Recognize the difference between method overriding and method overloading. Both method overloading and overriding involve creating a new method with the same name as an existing method. When the method signature is the same, it is referred to as method overriding and must follow a specific set of override rules to compile. When the method signature is different, with the method taking different inputs, it is referred to as method overloading, and none of the override rules is required. Method overriding is important to polymorphism because it replaces all calls to the method, even those made in a superclass.
- Understand the rules for hiding methods and variables. When a static method is overridden in a subclass, it is referred to as method hiding. Likewise, variable hiding is when an inherited variable name is reused in a subclass. In both situations, the original method or variable still exists and is accessible depending on where it is accessed and the reference type used. For method hiding, the use of static in the method declaration must be the same between the parent and child class. Finally, variable and method hiding should generally be avoided since it leads to confusing and difficult-to-follow code.
- Be able to write code that creates and extends abstract classes. In Java, classes and methods can be declared as abstract. An abstract class cannot be instantiated. An instance of an abstract class can be obtained only through a concrete subclass. Abstract classes can include any number of abstract and non-abstract methods, including zero. Abstract methods follow all the method override rules and may be defined only within abstract classes. The first concrete subclass of an abstract class must implement all the inherited methods. Abstract classes and methods may not be marked as final.
- Create immutable objects. An immutable object is one that cannot be modified after it is declared. An immutable class is commonly implemented with a private constructor, no setter methods, and no ability to modify mutable objects contained within the class.

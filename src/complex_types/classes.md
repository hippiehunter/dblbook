# Classes
#### Classes: The Building Blocks of Object-Oriented Programming

In object-oriented programming (OOP), classes serve as the foundational building blocks. They encapsulate data and methods that operate on that data into a single unit. When you hear the word "encapsulation," think of it as a protective shield that prevents external code from accidentally modifying the internal state of an object. This ensures data integrity and allows for modular and maintainable code.

#### Defining classes
```
[access] [modifiers] CLASS name [EXTENDS class] [IMPLEMENTS interface, ...]
member_def
  .
  .
  .
ENDCLASS
```

The syntax for declaring a class is pretty close to that of a structure so this should already look pretty familiar. `EXTENDS`, `IMPLEMENTS` and the catch-all `INHERITS` all have to do with how you can specify a base type or interface to be implemented by a class. `EXTENDS` is specifically for a base class. `IMPLEMENTS` allows a list of interfaces to be implemented. `INHERITS` can take any combination of a base class or list of interfaces and is a newer bit of syntax because it turns out that separating classes and interfaces syntactically is a real pain.

#### Members

A class typically comprises three main components: field, properties and methods. Properties are variables that hold the state of the object. For example, in a `Person` class, `Name` and `Age` could be properties. Methods, on the other hand, are functions defined within the class that act on or use these properties. Continuing with our `Person` analogy, `CelebrateBirthday()` might be a method that increases the `Age` property by one.

### Common class member modifiers
Methods and properties both share many of the same concepts because under the hood, properties are syntactic sugar that just makes a certain type of method more friendly to call and understand. 
**Modifiers**: A method or property can have several modifiers. Some common ones include:

-   **Static**: Can be accessed without referencing an object.
-   **Abstract**: Must be implemented in a derived class.
-   **Override**: Overrides a parent class's method.
-   **Virtual**: Can be overridden in a subclass.
-   **Const (field)**: A static field whose value is not changed once it is initialized. `CONST` implies `STATIC`.
-   **Readonly (field)**: A field’s value can only be set during declaration or in the constructor of the class
-   And more such as async (method), byref, extension (method), new, sealed, etc.

Visibility or access modifiers determine the accessibility of a class, method, property, or field. They define the scope from which these members can be accessed. The main purpose of visibility modifiers is to encapsulate the data, ensuring that it remains consistent and safe from unintentional modifications.

Here's a brief overview of common visibility modifiers:

1.  **Public**:
    -   When a class or its member is declared as public, it means it can be accessed from any other class or method, regardless of where it is in the program or the package.
    -   It offers the most unrestricted level of accessibility.
2.  **Private**:
    -   A private class member can only be accessed from within the same class in which it is declared.
    -   It's a way to hide the internal details of a class, exposing only what's necessary and keeping the rest hidden and safe from external interference.
3.  **Protected**:
    -   Protected members are accessible within the same class, as well as in subclasses.
    -   They cannot be accessed outside of these contexts unless they're in the same package (this behavior can differ in languages like Java).
4.  **Internal (.NET)**:
    -   An internal member can be accessed by any code in the same assembly, but not outside of it.
    -   This is particularly useful when you want to make something accessible to all the classes within a specific assembly, but hidden from outside consumers.
5.  **Protected Internal (.NET)** (specific to some languages like C#):
    -   This is a combination of both protected and internal. Members with this modifier are accessible from the current assembly and from derived classes.
    -   It provides a more nuanced level of accessibility.

**Importance of Visibility Modifiers:**

1.  **Encapsulation**: One of the core tenets of OOP is encapsulation, which means bundling data and methods that operate on that data into a single unit and restricting the direct access to some of the object's components. Visibility modifiers help achieve this by controlling the accessibility of class members.

2.  **Maintainability**: By controlling the access to class members, you can prevent unintended interactions or modifications. This makes the system easier to maintain and reduces the risk of bugs.

3.  **Flexibility**: Over time, the internal implementation of a class may need to change. If you've restricted access to internal details using private or protected visibility, you can change them without affecting classes that depend on the one you're modifying.

4.  **Data Integrity**: Ensuring that data is accessed and modified in controlled ways can prevent the system from entering into an inconsistent or invalid state.

### Defining Methods

```
[access] [modifiers ...] METHOD name, return_type[, options]
  parameter_def
  PROC
  . 
  .
ENDMETHOD|END
```

#### Key Components:

1.  **Name**: This is the method's identifier.

2.  **Return Type**: Specifies what kind of data the method will send back when called.

3.  **Parameters (parameter_def)**: These are values you can pass into a method to be used within its execution.
    
4.  **ROUND or TRUNCATE Options**: By default, a program rounds all expression results. However, specifying the ROUND or TRUNCATE option on a METHOD can override this.


### Defining Properties

```
[access] [modifiers ...] PROPERTY name, property_type
  method_def
  ...
ENDPROPERTY
```

Properties are unique members of a class that provide a flexible mechanism to read, write, or compute the value of a private field. Properties can be used as if they are data members, but they are actually special methods called accessors. The method def part of the above syntax description is mostly like a defining any other method with the main differences being you dont have to declare parameters or a return type.

**Auto Properties**
```
[access] [modifier ...] simple_mod PROPERTY name, property_type [,initial_value]
```

Auto properties or simple properties save you the tedium of having to write the accessor code if you just want to define a field and expose it with some control like only allowing code inside the class to set a value but allowing anyone to get a value (`SETPRIVATE`).

* `simple_mod`: This determines the behavior of the generated accessors. Options include `INITONLY`, `READONLY`, `READWRITE`, `SETPROTECTED`, `SETPRIVATE`, `SETINTERNAL`, and `SETPROTECTEDINTERNAL`.

**Why Use Properties?**

Properties offer a way to control access to the data in your objects. You can define what actions can be taken with that data, which can be essential for maintaining the integrity of your objects. For instance, properties can prevent inconsistent modifications, validate new data before storing it, or perform specific actions when data is accessed.

**Properties vs. Fields**

Fields are variables that hold the state of an object. When you want to provide controlled access or take certain actions when a field is read or written, you can use properties. A property will typically have a backing field which is a private member variable of the property's type. When the property gets accessed or modified, the corresponding getter or setter will be invoked, allowing the developer to add custom logic.

### Defining Fields

```
[access] [modifier] [name], [dimension|[dim, …]]type[size][, init_value, …] 
```

Most of the complexity seen in field definitions is about dealing with the fixed size types like `a`, `d`, `i` and fixed size arrays of those types. Here's a slightly less comprehensive look at defining a class field.

```
[access] [modifier] [name], type[, init_value]
```

Other than access modifiers, class field declarations are largely the same as what you've seen inside records, groups or structures and infact you can declare records, groups and structures within a class as you would in the data division of a routine.

#### Inheritance and Polymorphism

One of the powerful features of classes is the ability to create hierarchies through inheritance. A class can inherit properties and methods from another class, referred to as its "base" or "parent" class. This enables the creation of more specific subclasses from general parent classes. For instance, from a general `Vehicle` class, one could derive more specific classes like `Car` or `Bike`.

Polymorphism, closely tied to inheritance, allows objects of different classes to be treated as objects of a common superclass. This flexibility means that different classes can define their own unique implementations of methods, yet they can be invoked through a reference of the parent class, enhancing code reusability and flexibility.

Both of these topics will be further covered in the later sections about inheritance and objects.

#### Constructors and Destructors

Classes often come with special methods called constructors and destructors. A constructor is automatically invoked when an object of the class is instantiated, and it typically initializes the object's properties. Destructors, on the other hand, are called when the object is about to be destroyed, providing an opportunity to release resources or perform cleanup operations.

#### Overloaded Methods
Method overloading is a feature that allows a class to have multiple methods with the same name, but with different parameters. Consider the `Console.WriteLine` method in .NET, a staple for output in this book. In .NET this method is overloaded to accept a variety of data types, from strings to integers to custom objects. Whether you're passing a string, an integer, or even a decimal, you use the same method name, `Console.WriteLine`. The specific version of `Console.WriteLine` that gets executed depends on the type and number of arguments you provide. For example, `Console.WriteLine("Hello, World!")` will use the string overload, while `Console.WriteLine(123)` will use the integer overload. This allows for a consistent method name, promoting readability, while catering to various data needs. By leveraging method overloading, developers can maintain a streamlined method naming convention, making code both more intuitive and readable.

Overloaded methods don't require any specific syntax, they are just multiple method declarations with the same name but a different number of parameters or different parameter types.

#### Example
```dbl
namespace ComplexTypesExample
    
    public class Rectangle
        ;;  Fields
        
        private length_fld, d18.10
        private width_fld, d18.10
        ;;  Properties
        
        public property Length, id
            method get
            proc
                mreturn length_fld
            endmethod
            method set
            proc
                length_fld = value > 0 ? value : 0
            endmethod
        endproperty
        
        public property Width, id
            method get
            proc
                mreturn width_fld
            endmethod
            method set
            proc
                width_fld = value > 0 ? value : 0
            endmethod
        endproperty
        
        public property Area, id
            method get
            proc
                mreturn length_fld * width_fld
            endmethod
        endproperty

        ;;  Constructor overloading
        public method Rectangle
            endparams
            this(1.0, 1.0)
        proc
        
        endmethod
        
        public method Rectangle
            side, id 
            endparams
            this(side, side)
        proc
        
        endmethod
        
        public method Rectangle
            aLength, id 
            aWidth, id 
            endparams
        proc
            Length = aLength
            Width = aWidth
        endmethod

        ;;  Method overloading
        public method SetDimensions, void
            side, id 
            endparams
        proc
            SetDimensions(side, side)
        endmethod
        
        public method SetDimensions, void
            aLength, id 
            aWidth, id 
            endparams
        proc
            Length = aLength
            Width = aWidth
        endmethod
        
        public method Display, void
            endparams
        proc
            Console.WriteLine("Rectangle Dimensions: " +%string(Length) + " x " + %string(Width))
            Console.WriteLine("Area: " + %string(Area))
        endmethod
    endclass
endnamespace

main
proc
    begin
        ;;  Using default constructor
        data square = new Rectangle()
        ;;  Using constructor with one parameter
        data anotherSquare = new Rectangle(5)
        ;;  Using constructor with two parameters
        data rect = new Rectangle(4, 6)

        square.Display()
        anotherSquare.Display()
        rect.Display()
        ;;  Using method overloading
        rect.SetDimensions(7)
        rect.Display()
        rect.SetDimensions(7, 8)
        rect.Display()
    end
endmain
```
> #### Output
> ```
> Rectangle Dimensions: 1.0000000000000000000000000000 x 1.0000000000000000000000000000
> Area: 1.0000000000000000000000000000
> Rectangle Dimensions: 5.0000000000000000000000000000 x 5.0000000000000000000000000000
> Area: 25.0000000000000000000000000000
> Rectangle Dimensions: 4.0000000000000000000000000000 x 6.0000000000000000000000000000
> Area: 24.0000000000000000000000000000
> Rectangle Dimensions: 7.0000000000000000000000000000 x 7.0000000000000000000000000000
> Area: 49.0000000000000000000000000000
> Rectangle Dimensions: 7.0000000000000000000000000000 x 8.0000000000000000000000000000
> Area: 56.0000000000000000000000000000
```
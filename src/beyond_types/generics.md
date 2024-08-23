# Generics
.NET generics in DBL introduce a powerful way to create flexible, reusable, and type-safe code structures. At their core, generics allow developers to define classes, structures, methods, delegates, and interfaces that can operate on a variety of data types without specifying the exact data type during the time of their creation. This capability is not just a convenience but a significant enhancement in programming with DBL.

One of the key advantages of generics is the promotion of type safety. By using generics, developers can create data structures that are inherently safe at compile-time, reducing runtime errors related to type mismatches. This aspect is particularly beneficial when dealing with collections, where enforcing a consistent data type across all elements is crucial for reliability and maintainability of code.

Moreover, generics contribute to code optimization and reduction of redundancy. Prior to generics, developers often had to create multiple versions of classes or methods to handle different data types, leading to code duplication or expensive runtime checking and casting.<!--Should this say "extensive" instead of "expensive"?--> With generics, a single class or method can cover a range of data types, making the codebase more concise and easier to manage. This not only streamlines development but also makes the code more readable and maintainable.

## Syntax

Declaring generic types involves a straightforward syntax that closely resembles the conventional class declaration process, with the addition of type parameters. These type parameters, typically denoted as `<T>`, allow the class to handle various data types without specifying the exact type during the class definition. Here's how you can declare generic types in Synergy .NET:

To declare a generic class, use the standard class declaration syntax but include a type parameter within angle brackets (`<>`). This type parameter acts as a placeholder for the actual data type that will be used when an instance of the generic class is created.

The basic syntax is as follows:

```dbl
namespace Example
    class ClassName<T>
        ; Class members go here, using T as a type
    endclass
endnamespace
```

In this syntax, `ClassName` is the name of the class, and `T` is the generic type parameter. The letter `T` is conventionally used, but you can use any valid identifier.

### Examples of generic class declarations

#### A simple generic class

```dbl
namespace Example
    class MyGenericClass<T>
        public field, T
    endclass
endnamespace
```

In this example, `MyGenericClass` is a generic class with a single type parameter `T`. The class contains a public field of type `T`.

#### Generic class with multiple type parameters

```dbl
namespace Example
    class Pair<T, U>
        public first, T
        public second, U
    endclass
endnamespace
```

Here, `Pair` is a generic class with two type parameters, `T` and `U`. It can be used to store a pair of values of potentially different types.

## Constraints

Constraints in generics are a pivotal feature in Synergy .NET, allowing developers to specify limitations on the types that can be used as arguments for type parameters in generic classes, methods, interfaces, or structures. These constraints provide a way to enforce type safety and ensure that generic types behave as expected. There are primarily three types of constraints in Synergy .NET generics: base class constraints, interface constraints, and the `new` keyword constraint.

### Base class constraints

A base class constraint restricts a type parameter to a specified base class or any of its derived classes. By default, if no base class constraint is specified, the type parameter can be any class since it implicitly inherits from `System.Object`.

**Syntax:**

```dbl
namespace Example
    class MyClass<T(BaseClass)>
        ; Class members using T
    endclass
endnamespace
```

In this example, `T` is constrained to `BaseClass` or any class derived from `BaseClass`.

### Interface constraints

Interface constraints restrict the type parameter to classes that implement one or more specified interfaces. This ensures that the type argument provides the functionality defined in the interface, allowing the generic class to utilize these capabilities.

**Syntax:**

```dbl
namespace Example
    class MyClass<T(Interface1, Interface2)>
        ; Class members using T
    endclass
endnamespace
```

Here, `T` must be a type that implements both `Interface1` and `Interface2`.

### The "new" keyword constraint

The `new` constraint specifies that a type argument must have a public parameterless constructor. This is particularly useful when you need to create instances of the type parameter within your generic class.

**Syntax:**

```dbl
namespace Example
    class MyClass<T(new)>
        public method CreateInstance, T
        proc
            return new T()
        endmethod
    endclass
endnamespace
```

In this class, `T` is constrained to types with a public parameterless constructor, allowing the `CreateInstance` method to create a new instance of `T`.

### Combining constraints

Synergy .NET also allows you to combine these constraints to create more specific and controlled generic definitions.

**Syntax:**

```dbl
namespace Example
    class MyClass<T(BaseClass, Interface, new)>
        ; Class members using T
    endclass
endnamespace
```

In this case, `T` must be a type that inherits from `BaseClass`, implements `Interface`, and has a public parameterless constructor.

### Practical usage of constraints
Using base class or interface constraints in generics is a strategic choice for developers aiming to enhance the robustness, safety, and clarity of their code. These constraints serve several important purposes:

### Enforcing type safety

By specifying a base class or interface constraint, developers ensure that the generic type parameter adheres to a certain "shape" or set of behaviors. This guarantees that the generic class or method can safely invoke the methods and properties defined in the base class or interface, without the risk of runtime errors due to type mismatches. The alternative would be to use the `System.Object` type, which allows any type to be used as an argument for the type parameter. However, this would require the developer to perform runtime checks and casts to ensure that the type parameter is compatible with the methods and properties used within the generic class or method.

### Leveraging polymorphism

Base class and interface constraints enable polymorphism in generics. A generic class constrained to a specific base class can work with any subclass of that base class, thus benefiting from polymorphism. This allows the generic class to handle a variety of related types in a uniform way.

### Utilizing interface-defined capabilities

When a generic type is constrained to an interface, it can utilize the capabilities that are guaranteed by that interface. This is particularly useful when the generic class needs to perform operations that are specific to that interface, such as iterating over a collection (if the interface defines enumeration behavior), without needing to know the exact type of the objects it's dealing with.

### Designing flexible and reusable code

Constraints allow for the creation of flexible and reusable generic classes and methods. They enable developers to write code that is abstract and general enough to handle different types but also specific enough to enforce certain characteristics of these types. This balance between abstraction and specificity leads to more versatile and maintainable code.

### Enhancing code readability and clarity

Using constraints clarifies the developer's intent and the design of the generic type. It makes it clear to other developers what kinds of types are expected or allowed, improving the readability and maintainability of the code.

### Preventing inappropriate usage

Constraints prevent the misuse of a generic type by restricting the type parameters to suitable types. This reduces the likelihood of runtime errors and ensures that the generic type is used as intended.

### Facilitating code validation and debugging

With constraints in place, many potential errors can be caught at compile-time rather than at runtime. This early detection makes debugging and validation of the code easier, as the compiler can provide clear guidance on the proper use of the generic types.

### Examples of generic class constraints

#### Generic class with a type parameter constraint

```dbl
namespace Example
    class MyClass<T(SomeBaseClass)>
        public myField, T
    endclass
endnamespace
```

This declaration shows a generic class `MyClass` with a type parameter `T` that is constrained to be a subclass of `SomeBaseClass`. This ensures that `T` inherits from `SomeBaseClass`, allowing you to use methods and properties of `SomeBaseClass` within `MyClass`.

#### Complex constraints

```dbl
namespace Example
    class ComplexClass<T(class1, iface1, iface2, new), S(class2), K(iface3), X(new)>
        ; Class members using T, S, K, X
    endclass
endnamespace
```

In this more complex example, `ComplexClass` has multiple type parameters (`T`, `S`, `K`, `X`), each with different constraints. `T` is constrained to types that inherit from `class1` and implement both `iface1` and `iface2` interfaces, and it must have a public parameterless constructor (`new`).

### Constructing generic classes
Using generic classes involves constructing these classes with specific type arguments that conform to the defined constraints and rules of the generic class. This process allows developers to leverage the power and flexibility of generics while adhering to type safety. 

To use a generic class, you construct or instantiate it by providing actual type arguments in place of the generic type parameters. This creates a constructed type, tailored to the specified type arguments.

#### Instantiating a simple generic class

Suppose you have a generic class `MyGenericClass<T>`:

```dbl
namespace Example
    class MyGenericClass<T>
        public field, T
    endclass
endnamespace
```

You can instantiate this class with a specific type, such as `int`:

```dbl
record
    myInstance, @MyGenericClass<int>
proc
    myInstance = new MyGenericClass<int>()
```

Here, `myInstance` is an instance of `MyGenericClass` specifically constructed for the `int` type.

#### Multiple type parameters

For a class with multiple type parameters like `Pair<T, U>`:

```dbl
namespace Example
    class Pair<T, U>
        public first, T
        public second, U
    endclass
endnamespace
```

You can instantiate it with two types:

```dbl
record
    myPair, @Pair<int, string>
proc
    myPair = new Pair<int, string>()
```

In this case, `myPair` is an instance of `Pair` with `int` for `T` and `string` for `U`.

### Restrictions on type arguments

When instantiating generic classes in Synergy .NET, there are certain restrictions on the types that can be used as type arguments. These restrictions ensure compatibility and prevent runtime errors.

1. **Synergy descriptor types:**
   Certain Synergy descriptor types are not allowed as type arguments. These include `a`, `d`, `d.`, `I`, `i1-i8`, `n`, `p`, or `p.`, because these descriptor types represent specific data structures or behaviors that are not compatible with the generic system.

2. **No local structures:**
    Local structures only exist within the scope of the method or subroutine they are declared in. This means they cannot be used outside of that scope, including as type arguments for generic classes. If you want to use a structure as a type argument, it must be declared at the global level.

3. **Drop the '@' in type arguments:**
   The '@' symbol is not permitted in a type argument. For example, a declaration like `@class1<@string>` is not allowed and will result in a compilation error.



### Generic methods
Generics are not limited to classes and structures; they also extend to methods, delegates, and interfaces. This further allows for flexible and reusable code designs. Here's an overview of how to define and use these generic types.

#### Defining a generic method

To define a generic method, you include type parameters in the method declaration. These type parameters can then be used in the method's return type, its parameters, or within the method body.

**Syntax:**

```dbl, ignore, does_not_compile
public static method MyGenericMethod<T>, T
    p1, T
    ; Method body using T
endmethod
```

In this example, `MyGenericMethod` is a generic method with a type parameter `T`. It returns an object of type `T` and accepts a parameter of the same type.

#### Using a generic method

To use a generic method, specify the type argument when calling the method:

```dbl, ignore, does_not_compile
record
    result, int
proc
    result = MyGenericMethod<int>(5)
end
```

Here, `MyGenericMethod` is called with `int` as its type argument.

### Generic delegates

Generic delegates are similar to generic methods but are used in scenarios where methods are passed as parameters or assigned to variables.

#### Defining a generic delegate

Define a generic delegate by specifying type parameters in its declaration:

**Syntax:**

```dbl
delegate MyDelegate<T>, void
    param1, T
enddelegate
```

`MyDelegate` is a delegate that can take a method accepting a parameter of type `T`.

#### Using a generic delegate

Assign a method that matches the delegate's signature to an instance of the delegate:

```dbl
namespace Example
    class Example
        public static method ExampleMethod, void
            param, int
        proc
            ; Implementation
        endmethod
    endclass
endnamespace

main
record
    myDelegate, @MyDelegate<int>
proc
    myDelegate = Example.ExampleMethod
end
```

In this example, `ExampleMethod` is assigned to `myDelegate`.

### Generic interfaces

Generic interfaces allow you to define contracts with type parameters, making them versatile for various implementations.

#### Defining a generic interface

To define a generic interface, include type parameters in the interface declaration:

**Syntax:**

```dbl
namespace Example
    interface IMyInterface<T>
        method DoSomething, void
            param1, T
        endmethod
    endinterface
endnamespace
```

`IMyInterface` is a generic interface with a method that operates on type `T`.

#### Implementing a generic interface

When implementing a generic interface, specify the type argument in the implementing class:

```dbl
namespace Example
    class MyClass implements IMyInterface<int>
        method DoSomething, void
            param1, int
        proc
            ; Implementation
        endmethod
    endclass
endnamespace
```

In this implementation, `MyClass` implements `IMyInterface` for the `int` type.

### Uniqueness based on type parameters

The uniqueness of generic methods, delegates, or interfaces is determined by their signatures, which include the number of type parameters. This means that two generic items in the same scope are considered distinct if they have a different number of type parameters. However, if they have the same number and type of parameters, they are considered duplicates, and the compiler will throw an error.

For example, two methods named `MyMethod` with one type parameter each are considered duplicates, even if the names of the type parameters are different. However, `MyMethod<T>` and `MyMethod<T, U>` are considered unique due to the differing number of type parameters.

This rule ensures that each generic method, delegate, or interface is distinctly identifiable by its parameter structure.

### Constructed types in generics

A constructed type is created by specifying actual types in place of the generic type parameters when you instantiate a generic class, method, or delegate. For example, if you have a generic class `MyGenericClass<T>`, you can create a constructed type by replacing `T` with a specific type like `int` or `string`.

**Example:**

```dbl
namespace Example
    class MyGenericClass<T>
        public field, T
    endclass
endnamespace

main
record
    intInstance, @MyGenericClass<int>
    stringInstance, @MyGenericClass<string>
proc
    intInstance = new MyGenericClass<int>()
    stringInstance = new MyGenericClass<string>()
end
```

In this example, `MyGenericClass<int>` and `MyGenericClass<string>` are two different constructed types of the same generic class.

### Static fields in generic classes

Static fields are not shared globally across all instances of a generic class but are instead specific to each constructed type.

#### Sharing static fields among instances of the same constructed type

Static fields are shared among all instances of the same constructed type. This means that if you create multiple instances of a generic class with the same type arguments, they will share the same static field values.

**Example:**

```dbl
namespace Example
    class MyGenericClass<T>
        public static field, int
    endclass
endnamespace

main
record
    instance1, @MyGenericClass<int>
    instance2, @MyGenericClass<int>
proc
    instance1 = new MyGenericClass<int>()
    instance2 = new MyGenericClass<int>()
    MyGenericClass<int>.field = 10
    ; Both instance1 and instance2 see MyGenericClass<int>.field as 10
end
```

In this scenario, `instance1` and `instance2` share the same static field `MyGenericClass<int>.field`.

#### Different constructed types have separate static fields

When you instantiate a generic class with different type arguments, each constructed type has its own set of static fields. This separation ensures that static fields are relevant and specific to the type they are associated with.

**Example:**

```dbl
record
    intInstance, @MyGenericClass<int>
    stringInstance, @MyGenericClass<string>
proc
    intInstance = new MyGenericClass<int>()
    stringInstance = new MyGenericClass<string>()
    MyGenericClass<int>.field = 10
    MyGenericClass<string>.field = 20
    ; intInstance sees MyGenericClass<int>.field as 10
    ; stringInstance sees MyGenericClass<string>.field as 20
```

In this example, `intInstance` and `stringInstance` do not share the same static field; they each have their own version of `field`, one for `MyGenericClass<int>` and one for `MyGenericClass<string>`.

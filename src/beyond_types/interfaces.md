# Interfaces
Interfaces play the role of defining a blueprint for methods, properties, and events. An interface is akin to a contract; it specifies a set of operations that a class or a structure must implement, without providing the implementation details itself. It's a way to enforce certain behaviors and functionalities across different classes, ensuring they adhere to a specific protocol. Interfaces are only supported when targeting .NET.

### What Are Interfaces?

An interface is a type definition, similar to a class, but it purely represents a contract or a template. It defines what is to be done but not how it is to be done. Interfaces contain method signatures, properties, and events, but these members lack any implementation. When a class or a structure implements an interface, it agrees to fulfill this contract by providing concrete implementations for the interface's members.

### Characteristics of Interfaces

- **Abstract Members:** Interfaces historically only declare members but do not implement them. This is changing with the introduction of default interface implementations in .NET.
- **Multiple Inheritance:** A class or structure can implement multiple interfaces, thus supporting multiple inheritance, which isn't directly possible with base classes.
- **Polymorphism:** Interfaces enable polymorphic behavior. A class instance can be treated as an instance of an interface if it implements that interface.

#### Key Uses of Interfaces in .NET:

1. **Decoupling Code:** Interfaces help in decoupling the implementation from the interface, allowing changes in implementation without affecting the interface consumers.

2. **Testing and Mocking:** Interfaces facilitate easier unit testing and mocking. By programming to an interface, you can replace implementation with mock objects in testing scenarios.

3. **Design Flexibility:** Interfaces allow developers to define functionalities that can be adopted by various unrelated classes, providing flexibility in design.

4. **Contract-Based Development:** Interfaces ensure that a class adheres to a specific contract, making it easier to understand and use.

5. **API Design:** In API development, interfaces are used to define contracts that external systems can implement and interact with.

6. **Extensibility:** They provide a way to extend a system with new features without breaking existing functionality.



### Syntax for Declaring Interfaces in DBL

The syntax for declaring an interface in DBL is very similar to declaring a class. Just like a class, interfaces must be declared within a namespace. Here's the basic structure:

```dbl
namespace Example
    interface InterfaceName
        ; Interface members go here
    endinterface
endnamespace
```

In this structure:
- `interface` is the keyword used to begin the declaration.
- `InterfaceName` is the name of the interface, which should follow the naming conventions.
- The interface body contains declarations of methods, properties, and events without implementations.
- `endinterface` marks the end of the interface declaration.

### Guidelines for Naming Interfaces

When naming interfaces in .NET, certain conventions are generally followed to ensure clarity and consistency:

1. **PascalCase Naming:** Like classes and methods, interface names should use PascalCase (e.g., `IReadable`, `IMyInterface`).

2. **'I' Prefix:** It is a common practice in .NET to start interface names with an uppercase 'I' to distinguish them from classes and other types. This 'I' denotes 'Interface'.

3. **Descriptive Names:** The name should clearly describe the behavior or capability that the interface represents. For example, `IDisposable` for disposability, `IEnumerable` for enumeration.

4. **Keep it Short and Intuitive:** While being descriptive, the name should not be excessively long. It should be easily memorable and intuitive for other developers.

### Simple Interface Declaration

Here's an example of a simple interface declaration:

```dbl
namespace Example
    interface IShape
        method Draw, void
            someparameter, string
        endmethod
        method GetArea, double
        endmethod
        readonly property Color, string
    endinterface
endnamespace
```

In this example, `IShape` is an interface in the `Example` namespace with two methods - `Draw` and `GetArea`, and one property - `Color`. the `Draw` method takes a single parameter of type `string`. This interface can be implemented by any class that represents a shape, and it enforces the implementation of the `Draw` and `GetArea` methods and the `Color` property in those classes.

### Implementing Interfaces

To implement an interface, a class must provide concrete definitions for all the members declared in the interface. A class uses the `implements` keyword to specify the interface it is implementing. A class can implement multiple interfaces, separating each with a comma.

#### Explicit vs. Implicit Implementation

**Implicit Implementation**: 
This is when the method in the class has the same signature as the method in the interface. It is the most common way and is straightforward. The class methods naturally match the interface contract without any special syntax.

**Explicit Implementation**: 
In explicit implementation, the interface name is prefixed to the method name. This is particularly useful when a class implements multiple interfaces that may have methods with the same signature but require different implementations. In DBL, explicit interface members are accessible only through a variable of the interface type. 

#### Examples

**Simple Interface Implementation**:

```dbl
namespace Example
    public interface IVehicle
        method Drive, void
        endmethod
    endinterface

    public class Car implements IVehicle
        public method Drive, void
        proc
            Console.WriteLine("Car is driving")
        endmethod
    endclass
endnamespace
```

Here, `Car` implicitly implements the `IVehicle` interface.

**Explicit interface implementation**:
```dbl
namespace Example
    public class MultiFunctionDevice implements IPrinter, IScanner
        method IPrinter.Print, void
        proc
            ;; Printer-specific implementation
        endmethod

        method IScanner.Scan, void
        proc
            ;; Scanner-specific implementation
        endmethod
    endclass
endnamespace
```

Here, `MultiFunctionDevice` implements both `IPrinter` and `IScanner` interfaces. Since both interfaces have a `Print` method, we need to explicitly specify which interface's `Print` method we're implementing. Calling code will need to use the interface type to access the explicit implementation.

**Implementing Interfaces with Default Implementations** (for .NET 6+):

```dbl
namespace Example
    public interface IVehicle
        method Drive, void
        proc
            Console.WriteLine("Vehicle is driving")
        endmethod
        method Refuel, void
        endmethod
    endinterface

    public class ElectricCar implements IVehicle
        ;; Implements Refuel only; uses default Drive implementation
        public method Refuel, void
        proc
            Console.WriteLine("Charging battery")
        endmethod
    endclass
endnamespace
```

`ElectricCar` implements `Refuel` and inherits the default `Drive` method from `IVehicle`.

### Interfaces vs. Abstract Classes

1. **Definition and Capabilities**:
   - **Interfaces**: When targeting .NET 6+, interfaces can include default implementations for methods. This allows interfaces to define not just the contract (method signatures) but also provide a base implementation. However, they still cannot hold state (fields).
   - **Abstract Classes**: Abstract classes can provide both complete (implemented) and incomplete (abstract) methods. They can also contain fields, constructors, and other implementation details, offering a more comprehensive template for derived classes.

2. **Inheritance and Flexibility**:
   - **Interfaces**: A key advantage of interfaces is the ability to implement multiple interfaces in a single class, enabling a form of multiple inheritance and greater flexibility in combining different behaviors.
   - **Abstract Classes**: Classes can only inherit from one abstract class, enforcing a more traditional, linear inheritance hierarchy. This is useful for a clear and structured base but is less flexible than interfaces.

3. **Member Types and State Management**:
   - **Interfaces**: Even with default implementations, interfaces cannot maintain state through fields. All members are inherently abstract or virtual.
   - **Abstract Classes**: Can have a mix of abstract and non-abstract members, including fields, allowing them to maintain state and provide more comprehensive functionality.

4. **Access Modifiers and Member Visibility**:
   - **Interfaces**: Prior to .NET 6 interface members were always public. Interfaces now support more complex access patterns like private and protected members.
   - **Abstract Classes**: Offer full flexibility with access modifiers, allowing public, protected, and private member visibility.

#### When to Use an Interface Over an Abstract Class

1. **Role or Capability Modeling**:
   - Choose interfaces when defining a set of capabilities or roles that classes can adopt, especially when these capabilities can be combined or are independent of the class hierarchy.

2. **Enhanced Flexibility with Default Implementations**:
   - With default implementations, interfaces now offer a mix of defined behaviors and abstract declarations. Use interfaces when you want to provide a default behavior that classes can override or extend.

3. **Multiple Inheritance**:
   - If a class needs to incorporate functionality from multiple sources, interfaces remain the best choice due to their ability to support a form of multiple inheritance.

4. **Decoupling and Future Evolution**:
   - Interfaces are better for decoupling where you want to separate operation definitions from the class hierarchy. They are also preferable when expecting future expansion or changes in the contract, as adding new methods with default implementations doesn't break existing implementations.

#### When to Prefer Abstract Classes

1. **Shared Base Functionality with State**:
   - Use abstract classes when there’s a need for a shared base that includes not just behavior (methods) but also state (fields and properties).

2. **Controlled Inheritance**:
   - When a strict and controlled inheritance structure is required, with a clear common base for all subclasses, an abstract class is more appropriate.

3. **Comprehensive**:
   - Abstract classes are ideal when you need to provide a more comprehensive base, including constructors, fields, and a mix of implemented and abstract methods.

### Practical Use Cases for Interfaces in DBL

Interfaces in DBL are powerful tools for creating flexible, maintainable, and scalable software architectures. They are especially beneficial in scenarios where abstraction, decoupling, and multiple inheritance of behaviors are required. Here are some common scenarios and design patterns where interfaces are particularly useful:

#### 1. Decoupling Modules and Layers

Interfaces are instrumental in decoupling different parts of an application. By programming to an interface rather than a concrete implementation, you can change the underlying implementation without affecting the clients of the interface. This is particularly useful in:

- **Service Layers**: For example, in a multi-layered architecture, the service layer can expose interfaces, allowing the presentation layer to interact with the service layer without knowing the implementation details.
- **Repository Patterns**: In database operations, interfaces can abstract the data access layer, allowing for easier swapping of database technologies or mocking of data access for testing.

#### 2. Dependency Injection and Inversion of Control

In modern software design, especially in test-driven development (TDD), interfaces are crucial for dependency injection (DI) and inversion of control (IoC). Interfaces define contracts for services or components, and concrete implementations are injected at runtime. This approach simplifies unit testing and reduces coupling.

#### 3. Strategy Pattern

The Strategy pattern is a behavioral design pattern that enables selecting an algorithm's runtime implementation. By defining a family of algorithms as an interface and making each algorithm a separate class that implements this interface, you can switch between different algorithms dynamically.

#### Implementing the Strategy Pattern Using Interfaces

Let's consider a practical example: a sorting application where the sorting algorithm can vary.

**Define the Strategy Interface**
Create an interface for the sorting strategy.
```dbl
namespace Example
    public interface ISortStrategy
        method Sort, void
            arg, @List<int>
        endmethod
    endinterface
endnamespace
```

**Implement Concrete Strategies**
Create different sorting algorithms implementing the interface.
```dbl
namespace Example
    public class QuickSort implements ISortStrategy
        public method Sort, void
            arg, @List<int>
        proc
            ;; QuickSort implementation
        endmethod
    endclass

    public class MergeSort implements ISortStrategy
        public method Sort, void
            arg, @List<int>
        proc
            // MergeSort implementation
        endmethod
    endclass
endnamespace
```

**Context Class**
A context class uses the sorting strategy.
```dbl
namespace Example
    public class SortContext
        private strategy, @ISortStrategy

        public constructor SortContext, void
            strategy, @ISortStrategy
        proc
            this.strategy = strategy
        end

        public method SetStrategy, void
            strategy, @ISortStrategy
        proc
            this.strategy = strategy
        endmethod

        public method SortData, void
            data, @List of i4
        proc
            strategy.Sort(data)
        endmethod
    endclass
endnamespace
```

**Using the Strategy**
The client can now use the `SortContext` with different strategies.
```dbl
main
proc
    data myList = new List<int>() {5, 6, 7, 1, 2, 10, 88}
    
    sortContext = new SortContext(new QuickSort())
    sortContext.SortData(data)

    ;; Switch to a different strategy
    sortContext.SetStrategy(new MergeSort())
    sortContext.SortData(data)
end
```

In this example, the use of interfaces allows for flexibility and extensibility in the sorting algorithms. New sorting strategies can be added without modifying the context or client code, adhering to the Open/Closed principle, one of the SOLID principles of object-oriented design. This is a classic example of how interfaces promote better software design practices.


### Best practices in designing and using interfaces

#### Define Clear Contracts
Interfaces should represent a clear contract. Define methods and properties that are cohesive and relevant to the interface's purpose. Avoid adding unrelated methods which can lead to a violation of the Interface Segregation Principle (one of the SOLID principles).

#### Use Descriptive Names
Choose names that clearly convey the interface's purpose. For example, `IPrintable` or `IDataRepository` are more descriptive and intuitive than vague names like `IData` or `IProcess`.

#### Favor Small, Focused Interfaces
Create small and focused interfaces rather than large, do-it-all interfaces. This increases the flexibility and reusability of your interfaces and adheres to the Interface Segregation Principle.

#### Consider Default Implementations Carefully
With the capability of default implementations in interfaces, use them judiciously. Default methods can simplify interface evolution, but overusing them can lead to confusion and difficulties in understanding the inheritance hierarchy.

#### Document Interface Expectations
Document what each method in the interface is expected to do, its parameters, return type, and any special behavior or requirements. This is crucial for others who implement the interface to understand its purpose and usage.

#### Use Interfaces for Decoupling
Leverage interfaces to decouple components of your application. This facilitates testing (especially unit testing), maintenance, and future enhancements.

#### Interface Inheritance
Use interface inheritance to extend or modify the functionality of an interface. However, ensure that the new interface logically extends the old one and doesn't just add unrelated methods.

#### Consider Future Changes
Design interfaces with future evolution in mind. Changing an interface after it's widely used can be problematic. With default implementations, it's easier to add new methods, but changing existing ones can still break compatibility.

### Common Pitfalls to Avoid When Working with Interfaces

#### Over-Engineering
Avoid creating interfaces for classes that don’t need them, especially if there's only one implementation. This can lead to unnecessary complexity.

#### Ignoring Interface Segregation
Avoid creating large interfaces that force implementers to write methods they don’t need. This not only bloats the code but can also lead to errors and inefficient implementations.

#### Misusing Default Implementations
Be cautious when adding default implementations in interfaces. They should not be used as a workaround for multiple inheritance or to inject common functionality better suited for an abstract class.

#### Confusing Interface with Implementation
Avoid designing interfaces based on how they will be implemented. Interfaces should be defined based on what they represent or need to accomplish, not how they will achieve it.

#### Breaking Changes
Avoid making changes to interfaces that will break existing implementations. Consider the impact of changes on all classes that implement the interface.

#### Lack of Documentation
Avoid poorly documented interfaces. Clear documentation is vital for understanding the purpose and usage of an interface, especially in large and complex projects.

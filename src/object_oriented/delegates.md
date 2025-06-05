# Delegates
Delegates in DBL provide a mechanism for type-safe function pointers when compiling for the .NET framework. They enable developers to treat methods as first-class objects that can be passed as parameters, stored in variables, and invoked dynamically at runtime.

In DBL, delegates are declared using the `delegate` keyword followed by what looks like a regular method signature. The syntax follows this pattern:

```
delegate delegateName, returnType
    param1, @string
    param2, a
enddelegate
```

Once declared, a delegate type can be instantiated by assigning it a compatible method reference. Method compatibility is determined by matching return types and parameter signatures. For example, a delegate expecting a method that takes an integer parameter and returns a string will only accept methods with that exact signature.

DBL delegates support both instance methods and static methods. When using instance methods, the delegate maintains a reference to both the method and the object instance on which the method should be called. For static methods, only the method reference is stored.

Multicast delegates represent another powerful feature in DBL's .NET implementation. These allow multiple methods to be combined into a single delegate instance using the plus equals (`+=`) operator. When invoked, a multicast delegate calls all contained methods sequentially. This feature forms the foundation for event handling in DBL .NET applications.

Implementing event-driven patterns in DBL leverages delegates to create subscription mechanisms. Components can expose events (represented by delegate instances) that other components can subscribe to by adding handler methods to the delegate chain. This approach enables loosely coupled systems where components interact without direct dependencies, a core principle in modern application design.

## Relationship between lambdas and delegates in DBL

Lambdas and delegates share an intrinsic relationship where lambdas serve as a concise syntax for creating delegate instances. While delegates define the type signature for a method pointer, lambdas provide an inline implementation that conforms to that signature without requiring a separately defined named method. When a lambda expression is assigned to a delegate variable, the compiler automatically generates an anonymous method with the appropriate signature and creates a delegate instance pointing to this method. This relationship enables more compact and readable code, particularly when implementing event handlers or callback mechanisms that would otherwise require dedicated method definitions. Lambdas in DBL support both expression syntax for single-statement operations and statement block syntax for more complex logic, all while maintaining type safety. The parameter types in a lambda can often be inferred from the delegate type it's being assigned to, further reducing syntactic overhead. This delegate-lambda relationship forms a cornerstone of functional programming techniques within DBL's predominantly object-oriented environment, allowing developers to construct more dynamic and flexible code structures without sacrificing type safety or readability.
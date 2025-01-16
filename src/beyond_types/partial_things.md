# Partial Things

Partial classes and partial methods are features in some programming languages that allow a class or method to be split across multiple files or sections within a single file. This splitting enables developers to organize code more effectively, particularly in large projects. A partial class is a class definition that is broken into multiple parts, typically across different files. Each part can contain a subset of the class’s members (fields, properties, methods), and when the code is compiled, the parts are combined into a single class definition. Similarly, partial methods are method declarations within a partial class that allow for optional implementation. If a partial method is not implemented, the compiler removes the declaration, preventing any runtime overhead.

## Brief history and evolution of partial constructs in programming languages

The concept of partial constructs, particularly partial classes, emerged to address the growing complexity of software development, especially in environments where code generation tools play a significant role. Languages like C# introduced partial classes and methods as part of their feature set to allow better code organization, especially in situations where a single class would otherwise become too large or unwieldy. This was particularly valuable in the context of auto-generated code, where developers needed to extend or modify generated classes without directly altering the generated code. Shortly after adding initial support for OOP, the DBL language added support for partial classes and partial methods, providing developers with powerful tools to manage large codebases and promote clean, modular design.

## Partial methods

Partial methods allow developers to declare methods within a partial class without being required to implement them immediately. These methods are defined in one part of a partial class and can be optionally implemented in another part. If a partial method is not implemented, the compiler automatically removes the declaration and any calls to it, ensuring there is no runtime overhead. This feature is highly beneficial in scenarios where generated code needs to include method declarations that allow for custom extensions or hooks. Developers can provide additional logic or behaviors by implementing the partial method in a separate file, keeping the generated and custom code cleanly separated. Partial methods are often used in code generation scenarios where the framework or tool provides default behaviors that developers can optionally override or extend, making them a powerful tool for maintaining flexibility and extensibility in large, complex codebases.

## Use cases in code generation

Partial constructs are particularly useful when code is generated automatically—such as in Harmony Core or SQL replication. Defining classes or methods as partial allows developers to extend or customize the generated code without modifying the generated files. This separation ensures that custom code is preserved even when the generated code is updated. Maintaining the ability to keep generated code up to date is crucial to preventing bitrot.

## Syntax
In both methods and classes, the only syntax requirement is the keyword `partial`. 

```dbl
namespace ExampleNamespace
    partial class MyPartialClass
        public myField, int

        private partial method MaybeDoSomething, void
            param1, @StringBuilder
        endmethod

        public method GeneratedMethod, @String
        proc
            ;;do something interesting
            data myStringBuilder, @StringBuilder, new StringBuilder()
            myStringBuilder.Append("The first part")
            MaybeDoSomething(myStringBuilder)
            myStringBuilder.Append(" The last part)
        endmethod
    endclass
endnamespace
```

If this were the only part of `MyPartialClass`, the compiler would comply and effectively skip over the undefined call to `MaybeDoSomething`. Important elements here include the requirement that a partial method is both private and can only return `void`. This is necessary because when there is no corresponding definition, the compiler effectively skips calling the method. If there were a return value, this silent transformation would not be possible. Let's take a look at part 2 of a partial class definition.

```dbl
namespace ExampleNamespace
    partial class MyPartialClass
        public myOtherField, int
        private method MaybeDoSomething, void
            param1, @StringBuilder
        proc
            param1.Append("Hello from a partial method")
        endclass
    endclass
endclass
```

## Avoiding anti-patterns

While partial classes and methods offer significant advantages in managing large codebases and improving code modularity, they can also introduce challenges if misused. One of the most common misuses of partial classes is scattering related code across multiple files without a clear organizational strategy. This can lead to difficulty in understanding the full behavior of a class because its logic is fragmented. Instead of enhancing clarity, partial classes can contribute to code that is harder to navigate and maintain. To avoid this situation, developers should use partial classes with a clear intent and a consistent structure. Each part of a partial class should encapsulate logically grouped functionality, such as separating auto-generated code from custom logic, rather than splitting code arbitrarily.

Similarly, partial methods, while useful for providing extension points, can lead to confusion if overused or poorly documented. For example, declaring too many partial methods without clear documentation on their intended use can make it difficult for other developers to understand which methods are critical and which are optional. This misuse can lead to a fragmented implementation where the flow of control is obscured, making the codebase harder to maintain and debug. Developers should use partial methods sparingly and ensure their purpose is well-documented, indicating when and why a method might be implemented or left unimplemented.

## Common mistakes and how to avoid them

A frequent pitfall is relying too heavily on partial methods as a means of customization, especially in scenarios where other design patterns might be more appropriate. For example, if a partial method is used to override behavior that could be better handled through polymorphism or design patterns like the strategy pattern, it can lead to code that is less flexible and harder to extend in the future. Developers should instead carefully consider whether a partial method is the best tool for the job or if other, more robust patterns would offer better long-term maintainability.

 A lack of documentation is a significant issue when working with partial constructs. Because partial classes and methods inherently spread code across multiple files or sections, thorough documentation is critical to ensure that future maintainers or collaborators understand the design and intent behind the code structure. Clear comments and documentation can help mitigate the risk of misuse and ensure that partial constructs remain a tool for clarity and organization rather than a source of confusion. By adhering to best practices in documentation, developers can ensure that partial classes and methods are used effectively, enhancing rather than hindering the quality and maintainability of the codebase.

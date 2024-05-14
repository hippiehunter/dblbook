# Extension Methods

Extension methods provide a way to add new methods to existing classes or data types without modifying their source code or inheriting from them. These methods are static methods, but they're called as if they were instance methods on the extended type. The purpose of extension methods is to extend the capabilities of existing types in a clean and maintainable way.

#### Difference from traditional methods

- **Static vs. instance**: Traditional methods are either instance methods (belonging to an instance of a class) or static methods (belonging to the class itself). Extension methods, while declared as static, are used like instance methods. They are a special kind of static method that can be called on an instance of a class.

- **Class modification**: Traditional methods require modifying the class definition to add new methods. Extension methods, on the other hand, allow adding new methods to existing classes without altering their source code.

- **Scope of accessibility**: Traditional methods can access private and protected members of the class they belong to. Extension methods can only access the public and internal members of the types they extend, as they are not part of the type's internal implementation.

#### Benefits of using extension methods

1. **Enhancing functionality**: Extension methods enable developers to add functionality to classes for which the source code is not available, such as .NET Framework classes or third-party library classes.

2. **Improved readability**: They can make your code more readable and expressive. For example, instead of having a utility class with static methods, you can use extension methods to make it look like the functionality is a part of the existing type.

3. **Maintainability**: Since extension methods don't modify the original class, they help in maintaining a clean separation of the original code and the extended functionalities. This separation is particularly useful when the original classes are subject to updates or are part of a library.

4. **No need for inheritance**: Extension methods provide an alternative to inheritance for adding functionality to a class. This is especially useful when dealing with sealed classes or when inheritance would lead to an unnecessary or overly complex class hierarchy.

5. **LINQ and fluent interfaces**: In .NET, extension methods are a key part of LINQ (Language Integrated Query), enabling a fluent and intuitive way of processing data.

6. **Reduced boilerplate**: Extension methods can reduce boilerplate code by eliminating the need for numerous utility classes. Functionalities that would typically be put into a utility class can be made into extension methods, making them more discoverable and contextually relevant.

### Introduction to basic syntax

Extension methods are a special kind of static method. They are defined in a static class and have a syntax that is similar to regular static methods with some key differences. The basic structure of an extension method includes the `EXTENSION` and `STATIC` modifiers, followed by the method definition. Here’s a general syntax template:

```dbl,ignore,does_not_compile
public static extension method MethodName, ReturnType
    parm1, ExtendedType 
    OtherParameters...
proc
    ; Method implementation
endmethod
```

#### Significance of the EXTENSION and STATIC Modifiers

1. **STATIC modifier**: 
   - Indicates that the method is static and belongs to the class rather than any individual instance.
   - As with any static method, it can be called without creating an instance of the class in which it is defined.

2. **EXTENSION modifier**:
   - This modifier is specific to extension methods.
   - It signals that the method is intended to extend the functionality of the `ExtendedType` (the type of the first parameter).
   - This modifier enables the method to be called as if it were a method of the `ExtendedType` itself.

#### Structure of an extension method

- **First parameter (ExtendedType)**:
  - The first parameter of an extension method specifies the type that the method extends.
  - This parameter is crucial because it denotes the type on which the extension method can be called.
  - When calling the extension method, this first parameter is not passed explicitly; instead, the method is called on an instance of the `ExtendedType`.

- **Other parameters**:
  - After the first parameter, additional parameters can be defined as in any standard method.
  - These parameters are used as usual, and their values must be provided when the extension method is called.

- **Method body**:
  - Within the method body, the first parameter (`ExtendedType`) is used as if it were an instance of that type, enabling the extension functionality.

#### Example

Consider an example where we extend the `string` type with a `WordCount` method:

```dbl
namespace MyExtensions
    public static class StringExtensions
        public extension static method WordCount, int
            Parm1, string
        proc
            ; Count the number of words in Parm1
            mreturn NumberOfWords
        endmethod
    endclass
endnamespace
```

In this example:
- `WordCount` is an extension method that can be called on instances of `string`.
- It counts the number of words in the string and returns this count.
- The `Parm1` parameter represents the string instance on which the `WordCount` method is called.

#### Role of namespaces in extension methods

1. **Grouping by functionality**: It's a good practice to group extension methods into namespaces based on their functionality or the types they extend. For instance, all string-related extension methods can be in one namespace, while numeric type extensions can be in another.

2. **Avoiding naming conflicts**: Namespaces prevent naming conflicts, especially when you extend types that are not defined in your codebase (like standard library types). This is important because different libraries might introduce extension methods with the same name.

3. **Ease of maintenance**: Organizing extension methods in namespaces makes your codebase easier to navigate and maintain. It's clear where to find and how to use these extensions.

#### Using the namespace in your program

To use the `WordCount` method we defined earlier, include the `StringExtensions` namespace at the beginning of your DBL source file:

```dbl
import StringExtensions

main
    record
        myString, string
        wordCount, int
proc
    myString = "Hello, world!"
    wordCount = myString.WordCount()
    Console.WriteLine("Word count: " + %string(wordCount))
end
```

Here, the `import StringExtensions` statement makes the `WordCount` extension method accessible. You can then call `WordCount()` on any string instance as demonstrated.

### Limitations of extension methods
Extension methods offer a convenient way to add functionality to existing types, but they come with certain limitations and considerations that developers should be aware of.

1. **No access to private members**: 
   - Extension methods cannot access private or protected members of the type they are extending. They can only work with public and internal members, as they are essentially external to the type's implementation.
   
2. **Not part of the type's definition**:
   - Extension methods are not actually part of the extended type's definition. They are just static methods that syntactically appear to be part of the type. This means they don't participate in polymorphism in the same way that true instance methods do.

3. **No override capability**:
   - You cannot use extension methods to override existing methods of a type. If there's a method with the same signature as an extension method within the type, the type's method will take precedence.

#### When to use extension methods vs inheritance or composition

1. **Use extension methods when...**:
   - **Adding methods to sealed classes**: If you need to add functionality to a class that is sealed (cannot be inherited), extension methods provide a way to "extend" these types.
   - **Enhancing types you don't own**: For types that are part of a library or framework where you don't have the source code, extension methods allow you to add functionalities without modifying the original type.
   - **Creating fluent interfaces**: Extension methods are useful for building fluent interfaces, where method chaining can lead to more readable code, such as in LINQ.

2. **Prefer inheritance or composition when...**:
   - **Designing a family of types**: If you're creating a family of related types, inheritance provides a better structure, allowing shared functionality and polymorphic behaviors.
   - **Needing access to internal state**: If you need access to the private or protected members of a class, inheritance is a more suitable approach.
   - **Building complex functionalities**: For more complex functionalities that require maintaining state or extensive interaction with various aspects of a class, composition or inheritance offers a more integrated solution.

3. **Considerations for making a decision**:
   - **Purpose and scope**: Evaluate whether the functionality is a core aspect of the type (favoring inheritance/composition) or a supplementary utility (suitable for extension methods).
   - **Reusability and maintenance**: Consider how the addition of functionalities will impact the maintainability and reusability of your classes. Extension methods can enhance reusability without affecting existing class hierarchies.
   - **Coupling and cohesion**: Assess the level of coupling your feature requires with the existing type. Lower coupling and higher cohesion favor extension methods.

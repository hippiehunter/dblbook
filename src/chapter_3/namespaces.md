# Namespaces
A namespace is essentially a container that allows developers to bundle up a set of related functionalities, classes, or structures under a unique identifier. This concept ensures that similar named entities do not collide, which is particularly beneficial as software projects grow in size and complexity.

**Rationale**: The primary reason for using namespaces is to prevent naming conflicts. As software projects expand, there's an increased probability of having multiple developers or teams, perhaps working on different libraries or modules, inadvertently using the same name for a function, class, or variable. Such conflicts can lead to ambiguous references, making it unclear which entity is being referred to, thereby leading to potential errors and unpredictable behavior. Namespaces mitigate this problem by providing a clear and defined scope, ensuring that even if two entities share the same name, as long as they reside in different namespaces, they remain distinct and won't conflict.

Moreover, namespaces assist in code organization. By segregating functionalities into different namespaces, it becomes much easier to understand the modular structure of a project, track dependencies, and maintain the code. It gives developers a clear perspective on where to locate specific functionalities and how different components of a system relate to each other.

**Mechanics**: Defining a namespace is straightforward. Once a namespace is declared, all subsequent types, classes, and methods reside under that namespace until it's explicitly closed or a new namespace is declared. For instance:

```dbl
namespace MyNamespace
    class MyClass
    endclass
endnamespace
```

The class `MyClass` is now under the `MyNamespace` namespace and can be referred to specifically as `MyNamespace.MyClass`.

**Import Syntax**: When you want to utilize elements (like classes or methods) from a namespace in another part of your code, you can do so using the `import` statement. This means you won't need to provide the full namespace path every time you reference an entity.

For example, to use the previously mentioned `MyClass` without specifying its namespace every time, you'd write:


```dbl,ignore,will_not_compile
import MyNamespace
```

After this, `MyClass` can be used directly in the code without the `MyNamespace.` prefix.

Import statements should appear at the top of the file, this isn't enforced by the compiler but because the behavior is the same regardless of where the import statement is placed it would have surprising behavior if it was placed in the middle of a file.

**Nested Namespaces**: Namespaces can be nested within each other. For instance, you can have a namespace `MyNamespace` that contains another namespace `MyNamespace.MySubNamespace`. This is useful for organizing related functionalities into a hierarchy. This is done can be done in a single statement or multiple nested statements. For example:

```dbl,ignore,will_not_compile
namespace MyNamespace.MySubNamespace
    class MyClass
    endclass
endnamespace

;;this is equivalent to the above
namespace MyNamespace
    namespace MySubNamespace
        class MyClass
        endclass
    endnamespace
endnamespace
```

**Caveats**: In Traditional DBL namespaces cannot effectively contain functions and subroutines if you defined a function or subroutine inside a namespace it would be global and not contained within the namespace. This is not the case in .NET. In .NET you can define functions and subroutines inside a namespace and they will be contained within the namespace. This is an unfortunate internal limitation caused by the way functions and subroutines are found at runtime inside loaded ELBs.



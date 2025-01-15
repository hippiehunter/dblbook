# Structures
A STRUCTURE statement provides a clear and organized way to manage data in your programs. Unlike the RECORD or GROUP declarations, which allocate space for a specific, non-reusable grouping of variables, a STRUCTURE statement serves to define a blueprint for a data type. This blueprint can be used multiple times across your program, showcasing the advantages of using types in programming.

In contrast, a RECORD or GROUP creates a collection of variables in a fixed arrangement. While useful in certain contexts, this is a one-off assembly of data. Once defined, a RECORD can't be replicated, passed around, or instantiated, making it less flexible in larger programs.

On the other hand, a STRUCTURE is more akin to a type definition, acting as a template for creating instances of structured data. These instances can then be used, passed around, or returned from routines, offering a level of flexibility beyond what records provide.

Similar to a RECORD or GROUP, you can treat any STRUCTURE that contains only fixed-size types as an alpha. This allows you to write them directly to disk or send them directly over the network without any serialization effort. This doesn't work if the structure contains an object or another structure that can't be converted to an alpha.

Structures can be declared globally, within a namespace, class, or routine. If you pass a structure to a routine, the compiler knows precisely what type of data it is and can ensure that you're using the structure correctly, such as accessing the right fields or using appropriate operations. Therefore, using structures leads to safer, more reliable, and easier-to-maintain code, which is why they're recommended over groups and named records.

### Defining Structures
```
> [structure_mod] STRUCTURE name
>   member_def
>   .
>   .
>   .
> [ENDSTRUCTURE]
```

The above syntax for definition structures is a slightly abbreviated version. There are other features, but since they have their own chapters, we'll just cover the common parts here. 

The `structure_mod` preceding STRUCTURE is an optional modifier that provides additional information about the structure. Some of the available `structure_mod` options are

-   PARTIAL: Partial allows the definition of a structure to be split into multiple files. It is often used in code generation scenarios. Many code generators produce partial structures, allowing developers to extend the generated code with custom logic in a separate file, without the risk of overwriting it when the generated code is updated.
  
-   BYREF: This modifier is for .NET only. It tells the compiler that instances of the structure can only be allocated on the stack, not on the heap. This has implications for memory management and the structure's lifecycle, as stack-allocated structures have a deterministic, scoped lifetime that ends when the execution leaves the scope in which they were created. This contrasts with heap-allocated objects, which have their lifetimes managed by the garbage collector. BYREF structures also come with usage restrictions and cannot be used in certain contexts like generics, boxed objects, lambdas, YIELD iterators, and ASYNC methods. Despite these restrictions, BYREF structures can be particularly efficient in performance-critical parts of your code, where avoiding garbage collection overhead is important. 

-   CLS: This modifier is for .NET only. Applying the CLS modifier to a structure signals to the compiler that only CLS-compliant types will be contained in that structure. Although this restricts the type of fields that can be included in the structure, it notably broadens the ways you can manipulate instances of that structure. The implications are substantial, especially when dealing with generics, which we'll explore in more depth in an upcoming chapter on that topic.

-   READONLY: This modifier is for .NET only. It tells the compiler that instances of this structure are immutable, meaning their state cannot be changed after they are created. Immutability is a powerful concept in programming as it simplifies code by eliminating side effects and making it easier to reason about the behavior of the code. READONLY structures also have performance benefits, especially when combined with BYREF. The .NET JIT can make certain optimizations, knowing that a method cannot modify the state of a READONLY structure. This makes READONLY structures an attractive choice for high-performance .NET code, where minimizing copies of value types is essential.


A `member_def` inside a structure is either a single field definition or a group declaration. A field definition follows the syntax specified in "Defining a field," while a group declaration adheres to the GROUP-ENDGROUP syntax. There is no limit to the number of member definitions you can specify within a structure.
# Comments

All programmers strive to make their code easy to understand, but sometimes
extra explanation is warranted. In these cases, programmers leave *comments* in
their source code that the compiler will ignore but people reading the source
code may find useful.

Here’s a simple comment:

```dbl
; hello, world
```

In DBL, the idiomatic comment style starts a comment with one or two semicolons, and the
comment continues until the end of the line. For comments that extend beyond a
single line, you’ll need to include `;` on each line, like this:

```dbl
; I'm going to need a lot of space to explain this concept fully,
; multiple lines in fact Whew! I hope, this comment will
; explain what’s going on.
```

Comments can also be placed at the end of lines containing code:
```dbl
Console.WriteLine("Hello World") ;this is a comment
```

## Doc Comments

## Doc Comments in DBL

When compiling for .NET, similar to XML documentation in C#, triple semicolons `;;;` are used to create documentation comments. These are special kind of comments that can be processed by a documentation generator to produce software documentation. They are typically placed immediately before the code statement they are annotating.

Documentation comments can be extracted to generate external documentation or can be used by Integrated Development Environments (IDEs) and code editors to provide contextual hints and auto-completion suggestions.

The XML tags used in DBL doc comments are identical to those used in C# XML documentation. Some of the commonly used XML tags include:

- `<summary>`: Provides a short description of the associated code.
- `<param name="paramName">`: Describes a parameter for a method or function.
- `<returns>`: Describes the return value of a method or function.
- `<remarks>`: Provides additional information about the code, such as any special considerations or usage information.
- `<exception cref="exceptionName">`: Indicates the exceptions a method or function might throw.
- `<seealso cref="memberName">`: Creates a link to the documentation for another code element.

```dbl
namespace doc_comment
    class ADocumentedClass
        ;;; <summary>
        ;;; This function performs a calculation.
        ;;; </summary>
        ;;; <param name="input1">The first input value.</param>
        ;;; <param name="input2">The second input value.</param>
        ;;; <returns>The result of the calculation.</returns>
        ;;; <remarks>This function uses a specific algorithm to perform the calculation.</remarks>
        method Calculate, int
            input1, int
            input2, int
        proc
            mreturn input1 * input2
        end
    endclass
endnamespace
```
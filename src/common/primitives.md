# Primitive  Types

In DBL, weakly typed descriptor types like alpha, decimal, implied decimal, and integer are not bound by strict data type constraints. This wasn't really done on purpose—it's the result of being developed in the days of single-pass compilers and very limited memory, where it was not possible to enforce strong typing. DBL's continuation with this weak typing means that variables declared with these types can be assigned a variety of values or even manipulated in ways that are typically prevented in strongly typed systems. While this enables very old applications to move forward without significant costly refactoring, it increases the risk of type-related errors, necessitating a more cautious and thorough approach to debugging and data handling. It is possible to tell the modern DBL compiler to enforce strong typing, but it requires well-organized projects and setting a few compiler switches. 

Consider the following example, where a routine expects an alpha parameter, but a caller instead passes a decimal. In a strongly typed language, this would result in a compile-time error. However, with the right set of compiler switches, the compiler for Traditional DBL will allow a decimal to be passed in. At runtime there may be no ill effects, depending on the value. A negative number would result in an unexpected alpha, while the result of a positive number would look as if the routine caller had passed the correct type. 

```dbl
proc
    xcall test_parameters(5, "5", 5)
    xcall test_parameters(-5, " 5", -5)
    xcall test_parameters(0, "p5zdfsdf", 0)
endmain


subroutine test_parameters
    param1, a
    param2, d
    param3, n
proc
    Console.WriteLine(param1)
    Console.WriteLine(%string(param2 + 5))
    Console.WriteLine(%string(param3 + 5))
    xreturn
endsubroutine
```

> #### Output
> ```
> 5
> 10
> 10
> u
> 10
> 0
> 0
> -5:46341
> 5
> %DBR-S-STPMSG, STOP
> ```

You can see from the `u` and `-5:46341` outputs that this scenario wouldn't be a good idea in a real program. You are likely to encounter code like this in legacy DBL programs, but it's very unlikely that it will manifest itself in this obvious way. Because of the age and relative stability of most DBL code, it's more likely with production code that some rarely taken code path is not being tested and is almost never seen in production.

DBL can also enforce a more rigid type system, as seen with the string type. Here, a variable declared as a string can only hold string data, and any operation that attempts to change its type will result in a compile-time error. This strict type enforcement promotes data consistency and type safety, reducing runtime errors related to unexpected data conversion. Developers must perform explicit type conversions and cannot rely on the language to coerce types implicitly, leading to more predictable though verbose code.

### Alpha

An alpha value (`a`, `a*`, `a{size}`) is a sequence of printable ASCII characters treated as a single information unit. 

- `a`: An alpha parameter, return type, or method property.
- `a*`: An alpha data field with size determined by its initial value.
- `a{size}`: An alpha data field of specified size, default filled with spaces. For example, `a10` is a 10-character alpha.

> #### Platform limits
> Alphas have the following maximum-length restrictions on certain platforms:
> - 65,535 single-byte characters on 32-bit Windows and 32-bit Linux when running Traditional DBL
> - 32,767 single-byte characters on OpenVMS
> - 2,147,483,647 single-byte characters on all other platforms

<!--Mention that alpha variables are padded with spaces if you assign a value that doesn't take up all bytes?-->

### Decimal and implied decimal

Decimal (`d`, `d*`, `d{size}`) and implied-decimal (`d.`, `d{size}.{precision}`, `decimal`) types in DBL handle numbers as sequences of ASCII characters, ensuring an exact representation. Both decimal and implied-decimal types are signed, meaning they can represent both positive and negative numbers.

In a typical DBL program, the avoidance of floating-point numbers like `float` and `double` (which are discussed below) is deliberate. Floating-point representations can introduce rounding errors due to their binary format, which cannot precisely depict most decimal fractions. This imprecision, although minuscule in a single operation, can compound in financial contexts, leading to significant discrepancies. Therefore, DBL programmers rely on decimal and implied-decimal types for monetary computations to preserve data integrity.
<!--Floating point API.-->

### Integer

An integer (`i`, `i*`, `i1`, `i2`, `i4`, and `i8`) is a byte-oriented, binary representation of a signed whole number. The integer value depends on its usage and can be a value type or descriptor type. <!--Add info on when it's a value type or descriptor type? ***Put something in here about ^val-->

### Numeric

Numeric types (`n` and `n.`) define numeric parameters that can pass any of the numeric data types: decimal, packed (discussed below), or integer for `n` and implied decimal or implied packed for `n.`. It is not possible to declare a numeric field, only a parameter or return type.

## Ownership
### Sized declarations
When declaring an `a`, `d`, `i`, or `id` variable somewhere that defines a memory layout or owns its own memory, you must include a size. Let's consider an example where we define several fields in a record:

```dbl,ignore,does_not_compile
record
    myAField, a100
    myDField, d10
    myIdField, d10.3
    myIField, i4
```

The field `myAField` allocates a memory space of 100 bytes and tells the compiler to interpret that space as an alpha (or string) type. On the other hand, `myIdField` allocates 10 bytes of memory but specifies that the last three of those bytes should be interpreted as decimal places. This is an implied-decimal declaration. The decimal places are implied within the byte storage of the field, effectively allowing you to store a decimal number within an ASCII numeric space. If, for example, the memory allocated to `myIdField` contained the value `1333333333`, it would be interpreted and displayed as `1333333.333` when printed, due to the implied-decimal places.

### Unsized declarations

The size of parameters and return types is determined at runtime, rather than being defined during compile time. This means that the size of these variables is based on the actual data that is passed or returned during the execution of your program. 

When declaring a field like an alpha (`a`) type within a record, structure, group, or class, you would usually specify the size, for example, `myFld, a10`. However, for parameter or return type declarations, the size is omitted, and you would declare it simply as `myFld, a`.

The size of this unsized parameter will then be determined by the length of the argument passed at the time of calling. For example, if you call your routine with `xcall myRoutine("a short alpha")`, the size of the `myFld` parameter would be 13 at this particular call site, reflecting the length of the string `a short alpha`.

```dbl
main
proc
    xcall myRoutine("a short alpha")
endmain

; Routine definition
subroutine myRoutine
    in req myFld, a
proc
    Console.WriteLine(^size(myFld))
endsubroutine

```
> #### Output
> ```
> 13
> ```

## Less common types

### Packed and implied packed

Data in packed or implied-packed form (`p`, `p{size}`, `p.`, or `p{size}.{precision}`) is stored as two digits per byte, plus an extra byte for the sign. This data type is uncommon and can usually be migrated to decimal and implied decimal without significant trouble.

### Boolean

A boolean data type represents a true/false value, defaulting to false.

### Byte

In Traditional DBL, byte represents an eight-bit signed integer (`i1`), whereas in DBL on .NET, it's an eight-bit unsigned integer mapping to the .NET System.Byte structure. The default byte value is 0.

### Char

In Traditional DBL, char is a 16-bit numeric value (C# char), allowing a DBL program to write records read by C# programs.

### Double

In DBL on .NET, double is a value type mapping to the .NET System.Double structure, defaulting to 0.0. It's not recommended for Traditional DBL.

### Float

In DBL on .NET, float maps to the .NET System.Single structure, defaulting to 0.0. It's not recommended for Traditional DBL.

### IntPtr and UIntPtr (DBL on .NET only)

The .NET System.IntPtr and System.UIntPtr are unsigned integers of native size, depending on the platform.

### Long

In Traditional DBL, long maps to `i8`. In DBL on .NET, it's a value type mapping to System.Int64, defaulting to 0.

### Sbyte

In Traditional DBL, sbyte maps to `i1`. In DBL on .NET, it represents an eight-bit signed integer and  maps to the .NET System.Sbyte structure. The default value is 0.

### Short

In Traditional DBL, short maps to `i2`. In DBL on .NET, it's a value type that maps to System.Int16 and defaults to 0.


> #### Quiz
> 1. What are the three forms of alpha types in DBL?
>    - [ ] a, alpha, a10
>    - [ ] a, a*, a{size}
>    - [ ] a, a10, a*
>    - [ ] a, a*, a{size}+
>    
> 2. What's the maximum length of alpha types in 32-bit Windows and Linux when running Traditional DBL?
>    - [ ] 65,535 characters
>    - [ ] 32,767 characters
>    - [ ] 2,147,483,647 characters
>    - [ ] 2,147,483,648 characters
>    
> 3. In Traditional DBL, how is a byte represented?
>    - [ ] Eight-bit unsigned integer
>    - [ ] Eight-bit signed integer
>    - [ ] Sixteen-bit signed integer
>    - [ ] Sixteen-bit unsigned integer
>    
> 4. What is the main difference between the decimal and implied-decimal types in DBL?
>    - [ ] Decimal types are unsigned; implied-decimal types are signed
>    - [ ] Decimal types are whole numbers; implied-decimal types have a sized precision
>    - [ ] Decimal types have a fractional precision; implied-decimal types are whole numbers
>    - [ ] Decimal types are signed; implied-decimal types are unsigned
>    
> 5. What is the default value for the .NET System.Double structure in DBL?
>    - [ ] 1.0
>    - [ ] 0.0
>    - [ ] NULL
>    - [ ] Undefined
>    
> 6. Which types does short map to in Traditional DBL and DBL on .NET, respectively?
>    - [ ] i2 and System.Int32
>    - [ ] i2 and System.Int64
>    - [ ] i2 and System.Int16
>    - [ ] i4 and System.Int16
>    
> 7. When defining a record, when must you include a size for these DBL types: a, d, i, id?
>    - [ ] Only when the fields need to be stored in an array
>    - [ ] Only when the fields will be processed in a loop
>    - [ ] When the fields define a memory layout (this is most of the time)
>    - [ ] When the fields are initialized with a certain value
>    
> 8. In DBL, when is the size of parameters and non-^VAL return types determined?
>    - [ ] At compile time
>    - [ ] At runtime
>    - [ ] When they are initialized
>    - [ ] When they are declared
>    
> 9. What happens when you pass the string "a slightly longer alpha" to a routine with an unsized alpha parameter `myFld, a`?
>    - [ ] An error occurs as the parameter is unsized
>    - [ ] The parameter myFld will have a size of 23
>    - [ ] The parameter myFld will have a size of 24, including the end character
>    - [ ] The program will crash

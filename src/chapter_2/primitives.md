# Primitive  Types

> ### notes
> rework this section

Static typing + weak typing = descriptors
Signed vs unsigned
Why synergy applications dont use floating point
Overflow, truncation, misuse of data (math on an alpha)

### Alpha

An alpha value (`a`, `a*`, `asize`) is a sequence of printable ASCII characters treated as a single information unit. 

- `a`: An alpha parameter, return type, or method property.
- `a*`: An alpha data field with size determined by its initial value.
- `asize`: An alpha data field of specified size, default filled with spaces.

> #### Platform Limits
> Alphas have the following max length restrictions on certain platforms. 
> - 65,535 characters in 32-bit Windows and Linux when running Traditional DBL.
> - 32,767 characters in OpenVMS
> - 2,147,483,647 characters in all other platforms

### Boolean

A boolean data type represents a true/false value, defaulting to false.

### Byte

In Traditional DBL, byte represents an eight-bit signed integer (`i1`), whereas in DBL on .NET, it's an eight-bit unsigned integer mapping to the .NET System.Byte structure. The default byte value is 0.

### Char

In Traditional DBL, char is a 16-bit numeric value (C# char), allowing a DBL program to write records read by C# programs.

### Decimal and Implied-decimal

The `d`, `d*`, and `dsize` types represent decimal data, signed whole numbers consisting of ASCII numeric characters. The `d.`, `dsize.precision`, and `decimal` types represent implied decimal data, signed numbers with a whole number part and fractional precision.

### Double

In DBL on .NET, double is a value type mapping to the .NET System.Double structure, defaulting to 0.0. It's not recommended for Traditional DBL.

### Float

In DBL on .NET, float maps to the .NET System.Single structure, defaulting to 0.0. It's not recommended for Traditional DBL.

### Integer

An integer (`i`, `i*`, `i1`, `i2`, `i4`, and `i8`) is a byte-oriented, binary representation of a signed whole number. The integer value depends on its usage and can be a value type or descriptor type. 

### IntPtr and UIntPtr (DBL on .NET only)

The .NET System.IntPtr and System.UIntPtr are unsigned integers of native size, depending on the platform.

### Long

In Traditional DBL, long maps to `i8`. In DBL on .NET, it's a value type mapping to System.Int64, defaulting to 0.

### Numeric

Numeric types (`n` and `n.`) define numeric parameters that can pass any of the numeric data types: decimal, packed, or integer (for `n`), and implied decimal or implied packed (for `n.`).

### Packed and Implied-packed

Data in packed or implied-packed form (`p`, `psize`, `p.`, or `psize.precision`) is stored as two digits per byte, plus an extra byte for the sign. This data type is uncommon and can usually be migrated to decimal and implied decimal without significant trouble.

### Sbyte

In Traditional DBL, sbyte maps to an `i1`. In DBL on .NET, it represents an eight-bit signed integer, mapping to the .NET System.Sbyte structure, defaulting to 0.

### Short

In Traditional DBL, short maps to `i2`. In DBL on .NET, it's a value type mapping to System.Int16, defaulting to 0.

## Ownership
### Sized declarations
When declaring one of the following DBL types `a,d,i,id` somewhere that defines a memory layout or owns its own memory you must include a size. Let's consider an example where we define several fields in a `record`.

```dbl,ignore,does_not_compile
record
    myAField, a100
    myDField, d10
    myIdField, d10.3
    myIField, i4
```

The field `myAField` allocates a memory space of 100 bytes and informs the compiler to interpret that space as an alpha (or string) type. On the other hand, `myIdField` allocates 10 bytes of memory but specifies that the last three of those bytes should be interpreted as decimal places.

This is an implied decimal declaration. The decimal places are implied within the byte storage of the field, effectively allowing you to store a decimal number within an ascii numeric space. If, for example, the memory allocated to `myIdField` contained the value `1333333333`, it would be interpreted and displayed as `1333333.333` when printed, due to the implied decimal places.

### Unsized Declarations

The size of parameters and return types is determined at runtime, rather than being defined during compile time. This means that the size of these variables is based on the actual data that is passed or returned during the execution of your program. 

When declaring a field like an alpha (`a`) type within a `record`, `structure`, `group`, or `class`, you would usually specify the size, for example `myFld, a10`. However, for parameter or return type declarations, the size is omitted, and you would declare it simply as `myFld, a`.

The size of this unsized parameter will then be determined by the length of the argument passed at the time of calling. For example, if you call your routine with `xcall myRoutine("a short alpha")`, the size of the `myFld` parameter would be 13 at this particular call site, reflecting the length of the string "a short alpha".

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


> #### Quiz
> 1. What are the three forms of alpha types in DBL?
>    - [ ] a, alpha, a10
>    - [ ] a, a*, asize
>    - [ ] a, a10, a*
>    - [ ] a, a*, asize+
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
> 4. What's the main difference between the decimal and implied-decimal types in DBL?
>    - [ ] Decimal types are unsigned, implied-decimal types are signed.
>    - [ ] Decimal types are whole numbers, implied-decimal types have a sized precision.
>    - [ ] Decimal types have a fractional precision, implied-decimal types are whole numbers.
>    - [ ] Decimal types are signed, implied-decimal types are unsigned.
>    
> 5. What is the default value for the .NET System.Double structure in DBL?
>    - [ ] 1.0
>    - [ ] 0.0
>    - [ ] NULL
>    - [ ] Undefined
>    
> 6. What type does short map to in Traditional DBL and DBL on .NET respectively?
>    - [ ] i2 and System.Int32
>    - [ ] i2 and System.Int64
>    - [ ] i2 and System.Int16
>    - [ ] i4 and System.Int16
>    
> 7. When defining a record, when must you include a size for certain DBL types (a,d,i,id)?
>    - [ ] Only when the fields need to be stored in an array.
>    - [ ] Only when the fields will be processed in a loop.
>    - [ ] When the fields define a memory layout ((this is most of the time).
>    - [ ] When the fields are initialized with a certain value.
>    
> 8. In DBL, when is the size of parameters and non ^VAL return types determined?
>    - [ ] At compile time
>    - [ ] At runtime
>    - [ ] When they are initialized
>    - [ ] When they are declared
>    
> 9. What happens when you pass the string "a slightly longer alpha" to a routine with an unsized alpha parameter myFld, a?
>    - [ ] An error occurs as the parameter is unsized.
>    - [ ] The parameter myFld will have a size of 23.
>    - [ ] The parameter myFld will have a size of 24, including the end character.
>    - [ ] The program will crash.

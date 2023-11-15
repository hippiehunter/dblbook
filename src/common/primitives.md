# Primitive  Types
`Matt's actively working on this section (11/14-11/15), so any comments here are provisional.`

In DBL, weakly typed descriptor types like alpha, decimal, implied decimal and integer are not bound by strict data type constraints`[**tautology (?) Rework]`. This wasn't really done on purpose. It would be more accurate to say that in the days `...when DBL was being defined, the days` of single-pass compilers and very limited memory`,` it was not possible to enforce strong typing. This weak typing means that variables declared with these types can be assigned a range `[**"variety" ?]` of values or even manipulated in ways strongly typed systems would typically prevent. However, while `...the continuation of` this `...design` allows very old applications to move forward without significant and costly refactoring, it increases the risk of type-related errors, necessitating a more cautious and thorough approach to debugging and data handling. It is possible to tell the modern DBL compiler to enforce strong typing, but it requires well-organized projects and a few compiler switches to be set`[**will this be explained? Compiler stuff is probably out of scope, so maybe something like"...and compiler switches set to enforce it"]`. 

Consider a situation where a routine is expecting an alpha parameter, but a caller instead passes a decimal`, as in the following example`. In a strongly typed language, this would result in a compile-time error. However, depending on the compiler switches used, in traditional`[**I've lowercase the initial t here and elsewhere because that's how it is in the docs]` DBL the compiler may allow that decimal to be passed in. At runtime there may be no ill effects, depending on what the value was. A negative number would result in a strange alpha, while a positive number would look the same as if the caller of the routine had passed the correct type. 

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

You can see from the 'u' and '-5:46341' outputs that this wouldn't be a good idea in a real program. You may encounter code like this in legacy DBL programs, but it's very unlikely that it will manifest in this obvious way`[**"manifest" doesn't work well as an intransitive verb here. Rework or "manifest itself in this..."]`. Because of the age and relative stability of most DBL code, it's more likely that when this exists, it's in some rarely-taken code path that is not being tested and is almost never seen in production.

DBL can also enforce a more rigid type system`[**"...more rigid type checking"?]`, as seen with the "string" type. Here, a variable declared as a string can hold only string data, and any operation that attempts to change its type will result in a compile-time error. This strict type enforcement promotes data consistency and type safety, reducing runtime errors related to unexpected data conversion. To change a string variable's type, a developer must perform explicit type conversion and cannot rely on the language to coerce types implicitly. This leads to more predictable yet verbose code.

### Alpha

An alpha value (`a`, `a*`, `a<size>`) is a sequence of printable ASCII characters treated as a single information unit. `[**I think the angle brackets around "size" and the like make things more readable, so I've added them to show my preference and prompt discussion]`

- `a`: An alpha parameter, return type, or method property
- `a*`: An alpha data field with size determined by its initial value `[i.e., so apparently you must give it an initial value when you define it. ]`
- `a<size>`: An alpha data field of specified size, filled with spaces by default

> #### Platform Limits
> Alpha values have the following size restrictions: 
> - 65,535 characters on 32-bit Windows and `[**32-bit]`Linux when running traditional DBL
> - 32,767 characters on OpenVMS
> - 2,147,483,647 characters on all other platforms

`[**mention that alpha variables are padded with spaces if you assign a value that doesn't take up all bytes?]`

`[**mention that an ASCII character takes a single byte and that <size> tells you how many bytes the a will have, so it also tells you how many characters it will have?]`

### Decimal and Implied-Decimal

Decimal (`d`, `d*`, `d<size>`) and implied-decimal (`d.`, `d<size>.<precision>`, `decimal`) types in DBL handle numbers as sequences of ASCII characters`[**"numerals"]`, ensuring an exact representation. Both decimal and implied decimal types are signed, meaning they can represent both positive and negative numbers.

In a typical DBL program, the avoidance of floating-point numbers like `float` and `double` `[**(discussed below)]` is deliberate. Floating-point representations can introduce rounding errors due to their binary format, which cannot precisely depict most decimal fractions. This imprecision, although minuscule per operation, can compound in financial contexts, leading to significant discrepancies. Therefore, DBL programmers rely on decimal and implied-decimal types for monetary computations to preserve data integrity.
`[**The first sentence's passiveness seems too striking. Maybe something like "Floating point numbers, such as float and double, are generally avoided in DBL programs.". Actually, though, the first sentence is talking about avoiding floating point numbers in DBL programs specifically, so "like" or "such as" probably isn't correct, since float and double are (I'm thinking) the only floating point types in DBL, and then they're apparently only floating point numbers in Syn.NET.]`

### Integer

An integer (`i`, `i*`, `i1`, `i2`, `i4`, or `i8`) is a byte-oriented`[**what does that mean here?]`, binary representation of a signed whole number. The integer value depends on its usage`[**What does "depends on it's usage" mean here?]` and can be a value type or descriptor type`[**more info on when it's value or descriptor?]`. 

### Numeric

Numeric types (`n` and `n.`) define numeric parameters that can pass any of the numeric data types: decimal, packed`[**haven't mentioned packed yet]`, or integer for `n`; and implied decimal or implied packed for `n.`. It is not possible to declare a numeric field, only a numeric parameter or numeric return type.

## Ownership
### Sized declarations
When declaring one of the following DBL types `a`, `d`, `i`, `id` somewhere that defines a memory layout or owns its own memory`[**e.g., record or DATA, but not structure]`, you must include a size. Let's consider an example where we define several fields in a 

record.

```dbl,ignore,does_not_compile
record
    myAField, a100
    myDField, d10
    myIdField, d10.3
    myIField, i4
```

The field `myAField` allocates a memory space of 100 bytes and informs the compiler to interpret that space as an alpha (or string) type. On the other hand, `myIdField` allocates 10 bytes of memory but specifies that the last three of those bytes should be interpreted as decimal places. This is an implied decimal declaration. The decimal places are implied within the byte storage of the field, effectively allowing you to store a decimal number within an ASCII numeric space. If, for example, the memory allocated to `myIdField` contained the value `1333333333`, it would be interpreted and displayed as `1333333.333` when printed, due to the implied decimal places.

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

## Less common types

### Packed and Implied-packed

Data in packed or implied-packed form (`p`, `p<size>`, `p.`, or `p<size>.<precision>`) is stored as two digits per byte, plus an extra byte for the sign. This data type is uncommon and can usually be migrated to decimal and implied decimal without significant trouble.

### Boolean

The boolean represents a true/false value. Booleans default to false.

### Byte

In traditional DBL, byte represents an eight-bit signed integer (`i1`), whereas in DBL on .NET, it's an eight-bit unsigned integer, mapping to the .NET System.Byte structure. The default value for byte is 0.

### Char

In traditional DBL, char is a 16-bit numeric value (C# char), allowing a DBL program to write records read by C# programs.

### Double

In DBL on .NET, double is a value type mapping to the .NET System.Double structure, defaulting to 0.0. It's not recommended for Traditional DBL.

### Float

In DBL on .NET, float maps to the .NET System.Single structure, defaulting to 0.0. It's not recommended for Traditional DBL.

### IntPtr and UIntPtr (DBL on .NET only)

The .NET System.IntPtr and System.UIntPtr are unsigned integers of native size, depending on the platform.

### Long

In Traditional DBL, long maps to `i8`. In DBL on .NET, it's a value type mapping to System.Int64, defaulting to 0.

### Sbyte

In Traditional DBL, sbyte maps to an `i1`. In DBL on .NET, it represents an eight-bit signed integer, mapping to the .NET System.Sbyte structure, defaulting to 0.

### Short

In Traditional DBL, short maps to `i2`. In DBL on .NET, it's a value type mapping to System.Int16, defaulting to 0.


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
>    - [ ] When the fields define a memory layout (this is most of the time).
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
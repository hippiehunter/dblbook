# Prototyping
Prototyping is one of the phases of compiling a Traditional DBL project. It uses a separate Synergy Prototype utility (known as `dblproto`) to process your source code and produce type information to be used by later compilation phases. By default, DBL performs type checking for system-supplied routines and classes at compile time. This means the compiler assesses each system-supplied routine used in a program against its prototype, ensuring the proper number and type of arguments, the correct return type, and other specifics. 

When working with a single compilation unit, you will always see the compiler performing type checking. If you're not using MSBuild and building your project requires executing multiple DBL commands, you can use the `dblproto` utility to facilitate validation.

When writing for .NET, compile-time type checking is always in effect and is slightly more strict about what sorts of things can be implicitly converted. 

**Compile-time type checking:**

Compile-time type checking is the process by which a compiler verifies type constraints and ensures that operations performed on variables are semantically correct and type-safe. In programming languages with strong static typing, like Java, C++, and Swift, the compiler will scrutinize the code for type mismatches or incompatible operations during the compilation process, well before the code runs.

In early versions of DBL, if a routine was not previously declared and its name was encountered in a function call, it was implicitly considered to be a routine that returned a ^VAL type, with no assumptions about its arguments. If it was preceded by XCALL, there would be no return type, but the arguments were treated the same as with a function. In this <!--second?-->scenario, the compiler wouldn't conduct compile-time validity checks on the quantity or types of arguments passed to it. 

**How it works:**

When the compiler encounters a piece of code, it evaluates each variable, function, and operation based on its declared or inferred type. For example, in the statement `data x, int, 5`, the compiler understands that `x` is of type `int` and should only hold integer values. If later in the code there's an attempt to assign a string to `x` (e.g., `x = "hello"`), the compiler will flag it as a type error. Similarly, the compiler checks function calls to ensure that the right number and types of arguments are passed and checks if the return types of functions are used correctly.

**Advantages:**

1.  **Early error detection**: One of the most significant benefits is that many errors, particularly those involving data type mismatches or misused operations, are caught during the compilation process. This early detection can save developers a lot of time, as these errors can be harder to identify and diagnose if they only manifest during runtime.

2.  **Optimization**: Knowing the data types in advance allows the compiler to make various optimizations that can make the final code run faster and be more efficient.

3.  **Code readability and maintenance**: Strongly-typed code can be more explicit, making it easier for developers to understand the intended use and function of different variables and functions.

4.  **Security**: Type-related errors, if undetected, can lead to various vulnerabilities in software. Catching type mismatches during compilation can prevent some of these potential security risks.

**How to use it:**
If you're building using MSBuild, this<!--prototyping?--> is mostly handled for you automatically. Projects produce prototype files (with a .dbp file extension), project references consume those prototype files, and the compiler ensures that any symbols or routines it knows about are correctly used. In .NET, this coverage is complete; there isn't a way to accidentally use a function that the compiler doesn't have a definition for. In Traditional DBL, though, if a subroutine is undefined, by default the compiler will not complain. This behavior was chosen because most codebases contain some circular references or other things that prevent complete prototyping. If you want to enforce complete prototyping in Traditional DBL, you can use the `-qreqproto` compiler option. This can be passed to DBL on the command line, set inside your MSBuild project directly as `<DBL_qReqProto>True</DBL_qReqProto>` inside a `<PropertyGroup>`, or set in the Compile tab of your Visual Studio project properties.

If you're building using `dbl` from a script or directly on the command line, you will need to run `dblproto` prior to `dbl`. At its most basic, the flow looks like this:
```
dblproto -out=my_prototype_file.dbp my_source_file.dbl my_other_source_file.dbl
dbl -qimpdbp=my_prototype_file.dbp my_source_file.dbl
dbl -qimpdbp=my_prototype_file.dbp my_other_source_file.dbl
dblink -o my_program.dbr my_source_file my_other_source_file
```
The two invocations of dbl have been split to illustrate the prototype file, but with implicit prototyping, if you instead call it like this, it would be functionally equivalent:

```
dbl -o my_program.dbo my_source_file.dbl my_other_source_file.dbl
dblink -o my_program.dbr my_program.dbo
``` 

Keep in mind that if you're integrating `dblproto` into an existing build script, you may need to supply many of the same compiler options to `dblproto` that you normally supply to `dbl`.

**Unprototyped code:**
Before `dblproto` was created in version 9 of DBL, the closest thing to compile-time type checking was the external function declaration. This was only for functions and only defined the return type of a function so it would not result in any substantial compile-time type checking. Now that DBL has real compile-time type checking, these external function declarations are ignored by the compiler, but you may still see them in your code base.

> TODO More setup. The reader needs to understand that compile-time type checking is good and they need strategies that will allow them to incrementally move towards this goal. Might need to cover some of the qrelaxed options here as well as provide examples of code that compiles without dblproto but fails with it. Effort here will pay off in one of the major areas of issue in leg mod.

**Rules for unprototyped code:**
What happens when the Traditional DBL compiler doesn't know the type of an argument? For starters, it's going to assume that the argument is a descriptor type of some sort, meaning `a`, `d`, `id`, or `i`. Because descriptors carry some type information with them at runtime, all is not lost if you've passed the wrong type into a routine. It now depends on what you try to do with that argument. Operations involving a transition from `d` to `a` are relatively safe for positive numbers, but you will likely get an unexpected result if you store a wrongly typed negative `d` into an `a`. Going the other direction, `a` into `d`, will have pretty terrible results if that alpha contains anything other than ASCII decimal characters. The store may work, but performing arithmetic operations on it will cause an exception. Improperly typed `i` usage will result in binary data in whatever destination you're storing into. Type `id` has the same restrictions about negative numbers but with the added problem that the decimal point will be missing depending on how you use it.

**What to do about parameter type errors in older code:**
The `MISMATCH` modifier provides some flexibility in argument type matching during the compile-time checks. When applied to `n`, `n.`, `a`, or `d` parameter types, it permits an alpha type argument to be passed to a numeric type without triggering a prototype mismatch error. For both subroutines and functions, when `MISMATCH` is used with an `a` parameter type, it allows the passage of a decimal or implied-decimal argument to an alpha parameter.

However, there are nuances to be aware of. When using `MISMATCH` with an `n` parameter, you must exercise caution due to differences in behavior between Traditional DBL and DBL .NET. Specifically, in Traditional DBL, an alpha argument passed to a `MISMATCH n` is treated as decimal. In contrast, DBL running on .NET retains the alpha type for the passed argument. This distinction can lead to varied outcomes. If your intention is to pass an alpha to a routine with an `n` argument, and you don't make use of ^DATATYPE and ^A, you should opt for ^D rather than setting the routine to `MISMATCH n`.

For situations where you're using a `MISMATCH a` argument but expecting to access a `d` parameter as an alpha, ensure you use ^DATATYPE and cast with ^A.

If your goal is to pass a decimal variable to a routine with an alpha-typed parameter without employing ^DATATYPE for casting, using `MISMATCH a` is not a good idea. A better approach is to change the routine to accept a numeric parameter, label it as `MISMATCH n`, and judiciously use ^DATATYPE and ^A when necessary.

**Reinterpreting:**

The following operators do *not* convert the expression into an alpha, integer, decimal, or implied-decimal value. Instead, they only modify how the data is referenced in that particular instance. The underlying data remains intact. 

- `^A(expression)` Accesses the underlying data as though it were an alpha. This is especially handy when dealing with decimal data in file I/O operations.
- `^D(expression[, precision])` Accesses the underlying data as though it were a decimal or implied decimal, depending on whether you've passed the precision argument.
- `^I(expression)` Accesses the underlying data as though it were an integer. This is not usually very useful and should not really be done unless you know someone else put integer data there.

#### Example
```
record
    decimalData, a*, "1234"
    aFld, a*, "abcd"
    dFld, d*, 1234
    ifld, i4, 1234

proc
    Console.WriteLine(^a(dFld))
    Console.WriteLine(^a(-dFld))
    Console.WriteLine(^a(ifld))

    ;this example uses %string to make output easier
    Console.WriteLine(%string(^d(aFld)))
    Console.WriteLine(%string(^d(decimalData)))
    Console.WriteLine(%string(^d(decimalData, 2)))
    Console.WriteLine(%string(^d("-" + decimalData)))

    Console.WriteLine(%string(^i(aFld)))
```
> #### Output
> ```
> 1234
> 123t
> ╥
> -1234
> 1234
> 12.34
> =1234
> 1684234849
> ```

**Converting:**
The following functions *will* convert the expression into the target type, applying their respective rules while they do it.

- `IMPLIED(expression)` Converts an expression to an implied-decimal value.
  - If the expression is of integer or decimal type, the resulting value will lack fractional precision.
  - If the expression is alpha, the fractional precision will match the number of digits to the right of the decimal point in the expression.
  - Rounding and truncation are applied for alpha variables containing implied-decimal values with more than 28 digits to the right of the decimal point.
- `STRING(expression)` Converts a numeric value to its string representation.
  - Converts the given value to a nonblank string based on rules for moving data to an alpha destination. The optional format can alter the representation.
  - Particularly useful for displaying integer values, as it genuinely converts the data, unlike ^A.
- `INTEGER(expression)` Converts an expression to an integer value.
  - The expression is changed following the rules for moving data to an integer destination.
  - If the transformed expression doesn't fit the requested integer size, the high-order bits are discarded.
  - By default, the output is four bytes long, unless the expression magnitude specifies otherwise.

#### Example
Because the implementation of `Console.WriteLine` in Traditional DBL is limited compared to .NET, we will call %STRING to pass the correct type to `Console.WriteLine`. This will, of course, show the somewhat mangled output of some of these conversions.
```
record
    decimalData, a*, "1234"
    aFld, a*, "abcd"
    dFld, d*, 1234
    ifld, i4, 12345678

proc
    Console.WriteLine(%string(dFld, "$$$,$$$.XX"))
    Console.WriteLine(%string(ifld, "$$$,$$$.XX"))

    Console.WriteLine(%string(%implied(decimalData) + 4321))
    Console.WriteLine(%string(%implied(ifld) + 87654321))

    Console.WriteLine(%string(%integer(decimalData)))
    Console.WriteLine(%string(%integer(dFld)))
    Console.WriteLine(%string(%integer("99999999999999", 2)))
    Console.WriteLine(%string(%integer("99999999999999", 4)))
    Console.WriteLine(%string(%integer("99999999999999", 8)))
    Console.WriteLine(%string(%size(%integer("1234"))))
```
> #### Output
> ```
>     $12.34
> 123,456.78
> 5555
> 99999999
> 1234
> 1234
> 16383
> 276447231
> 99999999999999
> 4
> ```
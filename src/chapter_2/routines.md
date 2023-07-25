# Routines

Subroutines are self-contained blocks of code designed to perform specific tasks. They are similar to functions with a void return type in other languages. There are two types of subroutines in DBL: local and regular subroutines.


### Subroutines

An external subroutine, simply referred to as a subroutine, is a separate entity from the routine that calls it. This subroutine can be present in the same source file as the invoking routine or in a different file.

To define a subroutine, we use the 'subroutine' statement. The return point to the calling routine is determined by the 'return' or 'xreturn' statement. It's recommended to use 'xreturn' because it supersedes any nested local subroutine calls. If an exit control statement is not explicitly specified, a 'stop' statement is implied at the end of the subroutine.

Parameters for subroutines are listed right after the 'subroutine' statement, using a similar format to field declarations. The subroutine outlines the data type of each parameter, but the size of the argument is defined by the calling program. Note that default parameter values can only be declared when targeting .NET.

```dbl
subroutine mult
    a_result    ,n                    
    a_arg1      ,n
    a_arg2      ,n
proc
    a_result = a_arg1 * a_arg2          
    xreturn                             
endsubroutine
```

A subroutine is invoked using the 'xcall' statement. In Traditional DBL, if the first parameter is not an alpha, these subroutines can also be used as functions (i.e., in the form %subroutine). This requires the first argument passed to contain the result of the operation and the subroutine to be declared as an external function in your code.

### Local subroutines

A local subroutine resides in the same method, function or subroutine in which it's called. It's located between the 'proc' statement and its corresponding 'end' statement of the calling routine, starting with a label and ending with the 'return' statement. Local subroutines don't accept arguments but share the calling routine's data. This makes local subroutines function much like class members or the captured variables of a lambda in other languages, allowing for data sharing within a scope.

To invoke a local subroutine, we use the 'call' statement. After returning from the call, the processing continues at the line immediately following the 'call' statement. This provides a level of encapsulation and data sharing within a single routine or function.

### Functions

a function is a structured block of code designed to perform a specific task. It can return a value and can be invoked from anywhere a literal is allowed in an expression. The function name may optionally be preceded by a percent sign (%). An example invocation of a function could look like this:

```dbl,ignore,does_not_compile
value=%function(arguments)
if (%function(arguments))
xcall subroutine(arg1, %function(arguments), arg3)
```

Functions in DBL can be invoked in a unique manner where the return value is used as an additional argument. For example, a function call that appears as retval = %myRoutine(arg1, arg2) is interpreted by the compiler as xcall myRoutine(retval, arg1, arg2).

Functions are declared using the function statement, followed by data and procedure divisions. The freturn statement is used to terminate the function and return control to the caller.

A function name in traditional DBL can technically be up to 255 characters long. However, names longer than 30 characters get truncated upon linking, so it's important to ensure that the first 30 characters are unique.

Modifiers like LOCAL, STACK, and STATIC can specify the default state of unqualified RECORD statements within a function. These modifiers are mutually exclusive and default to LOCAL in traditional DBL, or STACK in DBL running on .NET, unless another modifier is specified.

The REENTRANT modifier allows a function to be called recursively it also changes the default for unqualified RECORD statements to STACK. This is reflected in the following statement:

```dbl
function fred ,REENTRANT

```

Here, all unqualified RECORD statements in function fred behave as STACK RECORD statements, disallowing initial values in STACK records.

The VARARGS modifier, which is optional for unprototyped functions and subroutines, is required when you want to pass more arguments than declared while using strong prototypes or running on .NET. We will cover prototyping extensively in a later chapter.

> #### Traditional DBL notes
> On Windows and Linux, functions not in the calling chain may be unloaded from memory by default. The RESIDENT modifier, if specified, keeps the function in memory.
>
> In traditional DBL, all expression results are rounded by default, although this behavior can be changed to truncation by enabling system option #11. However, specifying the ROUND or TRUNCATE option on a FUNCTION statement overrides this default behavior for that function. It's important to note that these options cannot coexist in the same statement and aren't supported in DBL running on .NET.
> 

> #### DBL on .NET notes
>Functions can be declared inside or outside of a namespace or class. Functions declared outside of a class are considered global. In DBL running on .NET, the compiler creates a new global class, `_CL`, for these global functions during the .exe or .dll file generation. If a function is also declared outside of a namespace, it is housed in `_NS_assemblyname._CL`.
>
> All routines are reentrant and `local` is synonymous with stack when running on .NET

### Defining Subroutines and Functions
> #### Function
> ```dbl,ignore,does_not_compile
> [access] [function_mod ...] FUNCTION name[, return_type[size]][, options, ...]
>    parameter_def
>    .
>    .
>    .
> [ENDFUNCTION|END]
> ```


> #### Subroutine
> ```dbl,ignore,does_not_compile
> [access] [subroutine_mod ...] SUBROUTINE name[, option, ...]
>    parameter_def
>    .
>    .
>    .
> [ENDSUBROUTINE|END]
> ```

#### Common between functions and subroutines

* access (optional): Sets access levels when defined inside a class. Options include PUBLIC, PROTECTED, PRIVATE, INTERNAL, and PROTECTED INTERNAL. More details in the chapter about objects.

* function_mod / subroutine_mod (optional, DBL on .NET only)
  * STATIC: Accessed without a reference object.
  * VARARGS: Accepts more arguments than declared.

* name: The name of the function or subroutine.

* options / option (optional)
  * LOCAL / LOCALDATA: Retains record content for the duration of function/subroutine activation.
  * STACK / STACKDATA: Provides unique record content for each activation of function/subroutine.
  * STATIC / STATICDATA: Retains record content through all function/subroutine activations.
  * REENTRANT: Allows multiple active instances of the function/subroutine.
  * RESIDENT: Keeps function/subroutine in memory when not in use (Windows, Linux only).
  * ROUND: Rounds implied-decimal data types within function/subroutine.
  * TRUNCATE: Truncates implied-decimal data types within function/subroutine (Traditional DBL only).
  * VARARGS: Accepts more arguments than declared.

* parameter_def: Definition of parameters.

#### Function Specific

* return_type (optional): Specifies the return type of the function. Defaults to the type of the variable or literal of the first FRETURN statement.
* size (optional): Specifies the size of the function return value.

### Parameters
The direction of data flow for parameters can be controlled using certain parameter modifiers: IN, OUT, and INOUT.

* IN: Parameters declared with the IN modifier are read-only and meant to be used for input into a subroutine, function, or method. The parameter can be used for reading but cannot be modified, even for local computations within the routine. This is particularly crucial for types like structures, alphas, decimals, integers, and numeric or implied decimal values which are passed by reference, as it ensures that the original data in the calling context isn't inadvertently altered.

* OUT: Parameters with the OUT modifier are designed to return data from a subroutine, function, or method back to the calling context. These parameters do not retain their initial value and are expected to be assigned a new value within the called routine before it returns. This effectively provides a way to 'return' more than one value from a subroutine or function.

* INOUT: When the INOUT modifier is applied, it allows a parameter to serve a dual role - as both an input (like an IN parameter) and an output (like an OUT parameter). These parameters can accept input and, after possible modifications within the routine, return output.

By default, subroutines and functions in DBL treat parameters as 'unspecified', which means they can be used for both input and output, akin to INOUT. However, 'unspecified' carries more risk because it doesn't protect against unintentional modifications to parameters that are not meant to be altered, such as temporary values or literals. Therefore, it's advisable to always specify the direction of a parameter explicitly.

In Traditional DBL, arguments can be passed to subroutines and functions through one of three ways: by descriptor, by reference, or by value. The default mode is by descriptor.

* By Descriptor: This is the default and most commonly used way of passing arguments. In this mode, any changes made to the variable linked with the parameter in the receiving routine are also reflected in the argument's value in the calling routine.

* By Reference: This mode is primarily used when passing arguments to non-DBL routines. It functions similarly to passing by descriptor; any modifications made to the variable linked with the parameter in the receiving routine are also reflected in the argument's value in the calling routine.

* By Value: This mode passes the value of the argument such that any modifications made to the parameter in the routine do not affect the argument's value in the calling routine. Objects and `^val` are passed this way for *IN* parameters.

In DBL running on .NET, two familiar mechanisms for passing parameters are used: by value (BYVAL) and by reference (BYREF). These behave the same way as their counterparts in other languages running on .NET.

* BYVAL: This mode passes a copy of the variable to the routine. This means any changes made to the parameter inside the routine do not affect the original argument.

* BYREF: This mode is used for objects and passes a reference to the object. This means that if the routine modifies the value, the change is reflected in both the calling and the called routine.

When DBL is running on .NET, arguments that would have been passed by descriptor in Traditional DBL are actually an object handle passed by value. This implementation detail aligns with the .NET framework's standard practice of passing objects handles by value. Because of the semantics of in, out and inout this detail is hidden unless you dig into the guts.

> #### Mismatch
> The MISMATCH modifier provides flexibility with weakly typed systems, permitting you to bypass type checking and pass variables of one type as arguments to parameters of a different type without raising a prototype mismatch error.
>
> When MISMATCH is used with an alpha, numeric, decimal, or implied-decimal parameter type, it allows the program to interchangeably pass either alpha or numeric type arguments to that parameter. This feature provides a certain level of freedom, but must be used with care to avoid unexpected behaviors or errors.
> 
> If you use MISMATCH with a numeric parameter, it's recommended to only use it for routines that either pass the parameter as an argument to another routine also marked MISMATCH numeric or where you explicitly control the datatype with casting. Failure to do so might lead to unexpected results, especially when running DBL on .NET as compared to Traditional DBL.
> 
> For instance, when an alpha parameter is passed to a MISMATCH numeric parameter, it's interpreted as decimal in Traditional DBL but remains alpha in DBL on .NET, leading to subtle differences in behavior. To safely pass an alpha to a numeric parameter, consider using explicit casting unless the routine uses MISMATCH numeric.
> 
> When you're dealing with a MISMATCH alpha parameter and expecting a decimal parameter to be treated as an alpha, ensure to explicitly control the datatype with casting.
> 
> In situations where you want to pass a decimal variable to a routine with an alpha parameter and you're not using explicit casting when writing to it, it's not advisable to use MISMATCH alpha. Rather, you should convert the routine to use a numeric parameter and use MISMATCH numeric, along with appropriate casting, when the parameter is used as an alpha.

#### Optional Vs Default
Optional parameters and parameters with default values both offer flexibility in function or method invocation. However, their behavior differs when it comes to determining if an argument was passed.

Optional parameters can be omitted from the function or method call. The built-in function ^PASSED can be used to check at runtime whether an argument for an optional parameter has been supplied. If ^PASSED returns false, it indicates that no argument was provided for the parameter in the function call. here's an example of ^PASSED in action:

```dbl
subroutine sub
    arg1        ,a              ;Optional parameter
    arg2        ,a              ;Optional parameter
    arg3        ,a              ;Optional parameter
proc
    Console.WriteLine(^passed(arg1))
    Console.WriteLine(^passed(arg2))
    Console.WriteLine(^passed(arg3))
endsubroutine

main
proc
    xcall sub(,"hi")
endmain
```

> #### Output
> ```
> 0
> 1
> 0
> ```

For leading or middle parameters you can put `,` without any argument to indicate this parameter is not passed. You can also do this for trailing arguments but as you can see in the example above you can just omit them entirely.

On the other hand, parameters with a default value are technically always supplied an argument. If no explicit argument is passed in the function call, the default value is used. As a result, ^PASSED will always return true for these parameters, indicating that an argument, even if it's the default one, was provided. This behavior effectively makes these parameters a hybrid between optional and required parameters.
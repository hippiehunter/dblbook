# Arrays

## Real Arrays and Pseudo Arrays

Fixed size arrays can either be 'real' or 'pseudo'. While pseudo arrays are deprecated and not recommended for use, understanding their properties and limitations is still important. It's important to note that, unlike many other programming languages, DBL uses a one-based indexing system for arrays, meaning that array elements start at index 1.

### Real Arrays

When declaring a real array, each declared dimension must be specified. For instance, consider a variable declaration where a record named 'brk' is a 3x4 array of type d1. 

```dbl,ignore,does_not_compile
    brk         ,[3,4]d1
```

In this case, 'brk[1,2]' and 'brk[2,2]' are valid references as each dimension is specified. However, 'brk[1]' is not valid and will trigger a compiler error `Error DBL-E-NFND: %DBL-E-NFND, brk[D] not found : Console.WriteLine(brk[1])`, because it specifies only one of the two dimensions.

For purely historical reasons 'brk[ ]' is valid and refers to the entire scope, or contents, of the dimensioned array as a single element. The maximum size of a scope reference is 65,535. If the array is larger than this, the scope size is modulo 65,535.


```dbl
record demo
    alpha       ,[3,2]d2 ,    12 ,34,
&                             56 ,78,
&                             98 ,76
    beta        ,[2,4]a3,     "JOE" ,"JIM" ,"TED" ,"SAM",
&                             "LOU" ,"NED" ,"BOB" ,"DAN"
proc
    Console.WriteLine(demo.alpha[1,2])
    Console.WriteLine(demo.beta[2,1])
    Console.WriteLine(demo.beta[])
```

> #### Output
> ```
> 34
> LOU
> JOEJIMTEDSAMLOUNEDBOBDAN
> ```

### Pseudo Arrays

A pseudo array is a one-dimensional array of type a, d, d., i1, i2, i4, i8, p, or p.. A real array can consist of those same types, as well as structure, but can be defined with multiple dimensions.

In Traditional DBL, if a pseudo array is used in a class, it is converted to a real array by the compiler. A pseudo array is also converted to a real array if it is used outside a class and the code is compiled with -qcheck. If -W4 is set, the compiler reports a level 4 warning regarding this change.

In DBL running on .NET, pseudo arrays are treated as real arrays by the compiler. As a result, you must use the (*) syntax to pass a pseudo array as an argument.

It's worth noting that you cannot declare a real array of .NET value types or objects or of CLS structures. For example, declarations like '10 int' or '10 String' are not permissible. Instead, use an array of i4 or a dynamic array, such as '[#]int' or '[#]String'.

Finally, to get the length of an entire static array, use '^size' on the array variable with empty brackets:

```dbl
record
    fld, 10i4, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
proc
    Console.WriteLine(^size(fld[]))
    Console.WriteLine(^size(fld[1]))
    Console.WriteLine(fld[1])
```

> #### Output
> ```
> 40
> 4
> 1
> ```

## Dynamic Arrays

Dynamic arrays provide a structure for storing multiple values of the same type, with the ability to resize during program execution. They are declared using the hash symbol (`#`) in square brackets, which denotes a dynamic array.

Dynamic arrays are not limited to storing simple data types such as integers or characters; they can also store complex data types like structures or classes. 

Regardless of what platform you're running on, dynamic arrays support various operations for data manipulation and retrieval. These include methods like `Clear()`, `IndexOf()`, `LastIndexOf()`, and `Copy()`, which clear the contents, find an element, find the last matching element, and copying, respectively. The `Length` property can be used to get the current size of the array. Dynamic arrays can be iterated over one element at a time using the foreach loop. When running on .NET you have access to the remainder of the `System.Array` properties and methods.

Using dynamic arrays in DBL can make your code more flexible and efficient, as it allows you to handle varying data quantities without the need for manual memory management using `^m` or huge static arrays.

Here's a few examples showing how to declare and initialize dynamic arrays in DBL

```dbl
record
    myStringArray, [#]String
    anotherStringArray, [#]String
proc
    ;;create a new array with 2 values
    myStringArray = new String[#] { "first value", "second value"}

    ;;replace the second element
    myStringArray[2] = "a different second value"

    Console.WriteLine(myStringArray[2])

    ;;allocate a new array and Copy the elements from myStringArray
    anotherStringArray = new String[2]
    Array.Copy(myStringArray, 1, anotherStringArray, 1, 2)

    Console.WriteLine(anotherStringArray[1])
```

> #### Output
> ```
> a different second value
> first value
> ```


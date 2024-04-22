# Assignment

The equal sign (`=`) is used as an assignment operator. It takes the value from one operand and stores it in the other but with quite a few special behaviors. The left operand must be a variable specification, with the right operand treated as an expression. The evaluated expression is stored in the variable before the variable is used in the rest of the expression.

The basic syntax for an assignment operation is

`destination = source`

-   `destination`: A simple, subscripted, or ranged variable that will be modified.
-   `source`: An expression whose evaluated result is assigned to the destination variable.

### Assignment Operator Examples

For instance, the following assignment operation:

`xcall sub(a=1, b=2, c="ABC")`

is equivalent to

```dbl,ignore,does_not_compile
a=1
b=2
c="ABC"
xcall sub(a, b, c)
```
It's possible to create operations like `X + Y = 3`, where `Y` is set to `3` before it's added to `X`. This allows for expressions like `if ((len=^size(arg)).eq.4)`.

In an assignment statement like `decimal_var = decimal_var * 3`, the current value of `decimal_var` is multiplied by `3`, then reassigned as the new value for `decimal_var`.

DBL typically rounds results when storing from an implied-decimal source expression. To truncate results, use the %TRUNCATE function or set the TRUNCATE option on MAIN, FUNCTION, and SUBROUTINE statements, or set system option #11.

> TODO: init and clear

Data Type Assignments
---------------------

The rules for moving data between variables of different types vary based on the data types involved.

### Alpha to Alpha
when the source and destination are of the same length in an alpha-to-alpha assignment, data is directly copied from the source to the destination. However, if the lengths differ, specific rules are followed:

1.  When the source data length is shorter than the destination's, the data is copied from the source to the destination starting from the left. Any remaining space in the destination is filled with blank spaces to the right.
2.  When the source data length exceeds that of the destination, only the leftmost characters from the source are copied until the destination space is filled. Notably, this process does not trigger any warning or error, meaning the extra characters from the source are simply ignored and not copied.

Here are some examples of alpha-to-alpha assignments:

```dbl
record xyz
    result      ,a4
    afld1       ,a6,    "abcdef"
    afld2       ,a2,    "xy"
proc                            
    Console.WriteLine(result = afld2)
    Console.WriteLine(result = afld1)
    Console.WriteLine(result = "1234")
```

> #### Output
> ```
> xy
> abcd
> 1234
> ```

### Alpha to String
Directly assigning an alpha to a string may not yield the desired results, as whitespace behaves differently in strings and alphas. In strings, leading whitespace is significant, while in alphas it's generally ignored. Therefore, if you assign an alpha to a string as in `string_var = a500Var`, you'll get a 500-character-long string, even if it's all whitespace. To circumvent this and ignore leading whitespace, use `string_var = %atrimtostring(a500var)`

### Alpha to Numeric
The process of assigning an alpha to a numeric destination involves several steps:

1.  Evaluate the source: The alpha source data is evaluated to produce a numeric value.
2.  Assign according to type: The resulting numeric value is assigned to the destination according to the rules for moving a value of the source's data type to the destination's data type.
3.  Check for invalid characters: During the conversion process, if a character that isn't a blank, decimal point, sign (+ or --), or numeric digit is encountered, a "Bad digit encountered" error ($ERR_DIGIT) occurs. Note that sign characters can be positioned anywhere within the alpha source and are applied in the order they appear. Blanks and plus signs (+) are disregarded, and only one decimal point is permitted.
4.  Identify the numeric type: If a decimal point is present in the source, the evaluated result is considered implied-decimal. The rules for moving an implied-decimal source to an implied-decimal destination are then followed. If no decimal point is present, the result is treated as a decimal, and the rules for moving a decimal source to a decimal destination are adhered to.

To illustrate these concepts, here are some examples of alpha-to-decimal assignments:

```dbl
record results
    decvar      ,d6
    impvar      ,d5.3
    int4var     ,i4
    int2var     ,i2
    int1var     ,i1
proc
    Console.WriteLine(decvar =  "-1-2-3")         
    Console.WriteLine(decvar =  "123456789")      ;overflow results in loss of leftmost digits
    Console.WriteLine(decvar = " 3 5 8 ")         
    Console.WriteLine(decvar = "9.78")            ;9 if compiled with DBLOPT #11 (rounding vs truncating)
                                                  
    Console.WriteLine(impvar = " 6448.3")         ;overflow results in loss of leftmost digits
    Console.WriteLine(impvar = "54.32")           
    Console.WriteLine(impvar = "19.3927")         ;19.392 if compiled with DBLOPT #11
                                                
    Console.WriteLine(int1var = "456")            ;overflow results in loss of high-order byte
    Console.WriteLine(int2var = "-231.796")       ;-231 if compiled with DBLOPT #11
    Console.WriteLine(int4var = "123456789")      ;123456789

    Console.WriteLine(decvar = "abcde")           
```

> #### Output
> ```
> -123
> 456789
> 358
> 10
> 48.300
> 54.320
> 19.393
> -56
> -232
> 123456789
> Unhandled exception. Synergex.SynergyDE.BadDigitException: Bad digit encountered
> ```

### Numeric to Alpha
When assigning numeric data to an alpha destination, the runtime must format numeric data to fit the destination. The assignment takes into account whether the format is implicitly or explicitly specified in the statement.

If the source data is integer, packed, or implied-packed, it is first converted to decimal format before the assignment.

Rules for implicit formatting:

If the source is a numeric expression, the assignment follows these rules:

1.  Load right-justified significant digits: The significant digits from the source are right-justified and loaded over leading blanks in the destination.
2.  Load negative sign: If the source value is negative, a minus sign is placed immediately before the leftmost digit.
3.  Insert decimal point: For implied-decimal sources, the decimal point is inserted between the whole number part and the fractional precision.
4.  Handle larger source than destination: If the source value, including any decimal point, has more digits than the destination can hold, only the rightmost part is transferred. If the source is negative, the minus sign is omitted without raising any warning or error.
5.  Handle equivalent source and destination sizes: If the number of digits and any decimal point in the source is the same size as the destination, all digits are transferred. Similar to the previous rule, if the source value is negative, the minus sign is omitted, and no warning or error is raised.

Let's look at some examples of decimal-to-alpha assignments following these implicit formatting rules. Notice the leading whitespace and the effect of storing into the a9 vs a6 fields:

```dbl
record
    alpha6_result      ,a6
    alpha9_result      ,a9
record
    dfld1     ,d3,    -23
    dfld2     ,d6,    -123456
    impfld1   ,d4.2,  68.54
    impfld2   ,d12.4, 12345678.9876
    intfld1   ,i1,    99
    intfld2   ,i2,    1003
    intfld4   ,i4,    82355623
proc
    Console.WriteLine(alpha6_result = dfld1)            
    Console.WriteLine(alpha6_result = dfld2)            
    Console.WriteLine(alpha6_result = impfld1)            
    Console.WriteLine(alpha6_result = impfld2)            
    Console.WriteLine(alpha9_result = dfld2)            
    Console.WriteLine(alpha9_result = impfld2)            
    Console.WriteLine(alpha6_result = intfld1)            
    Console.WriteLine(alpha6_result = intfld2)            
    Console.WriteLine(alpha6_result = intfld4)            
    Console.WriteLine(alpha9_result = intfld4)  
```

> #### Output
> ```
>    -23
> 123456
>  68.54
> 8.9876
>   -123456
> 5678.9876
>     99
>   1003
> 355623
>  82355623
> ```


### Explicit formatting
To format an assignment between a numeric and an alpha value, use the format `destination = source, format`. The format specification depicts how the destination will look. The formatted result fills the destination starting from the right, with any overflow ignored without an error or warning. You will likely encounter this sort of formatting in your codebase, but know that when writing new code using .NET, you can also use `String.Format` and its associated formatting literals.

The format specification contains certain case-sensitive characters:

1.  **X**: This uppercase character represents a single digit from the source data. The digit is placed into the destination starting from the right and continuing to the left. Leftover X positions are filled with zeros.
2.  **Z**: This uppercase character also represents a digit but behaves differently from X. Extra Z positions (when there are fewer digits in the source than Z characters in the format) are filled with blanks, unless there is a period or X to its left.
3.  *****: The asterisk also represents a digit position. When there are no more significant digits to be transferred, the position is filled with an asterisk, not a zero.
4.  **Money sign**: This character functions similarly to Z, but when there are no more digits, the position is filled with a money sign (default "$").
5.  **--**: This minus sign, when placed as the first or last character in a format, indicates negativity. If the source is positive, a blank space is loaded.
6.  **.**: This period character causes a decimal point to be placed at the corresponding position in the destination.
7.  **,**: This comma character is loaded at the corresponding destination position if more source digits remain to be transferred and an X character appears to its left.

Avoid using formatting characters as normal text, as they might cause unexpected results. If you need to substitute formatting characters due to regional preferences, use the LOCALIZE subroutine.

In the following example, you'll notice the format is different because the comma would instead mean a second parameter to `Console.WriteLine` instead of an explicit format specifier.

```dbl
record
        alpha       ,a10
proc                                            
    alpha = 987, "XXXXXX-" 
    Console.WriteLine(alpha)
    alpha = -987, "XXXXXX-"
    Console.WriteLine(alpha) 
    alpha = 987, "-XXX"
    Console.WriteLine(alpha)            
    alpha = 987, "XXXXXX"
    Console.WriteLine(alpha)          
    alpha = 987, "ZZZZZZ"
    Console.WriteLine(alpha)          
    alpha = -987, "-ZZZZZZ"
    Console.WriteLine(alpha)         
    alpha = 987, "******"
    Console.WriteLine(alpha)          
    alpha = 98765, "Z,ZZZ,ZZZ"
    Console.WriteLine(alpha)       
    alpha = 98765, "*,***,***"
    Console.WriteLine(alpha)       
    alpha = 9, "***.**"
    Console.WriteLine(alpha)          
    alpha = 9876, "$$$,$$$.$$"
    Console.WriteLine(alpha)      
    alpha = 9876, "$$*,***.XX"
    Console.WriteLine(alpha)      
    alpha = 9876, "Val: Z.ZZ"
    Console.WriteLine(alpha)       
    alpha = 95, "This puts a X in"
    Console.WriteLine(alpha)
```

> #### Output
> ```
>    000987
>    000987-
>        987
>     000987
>        987
>    -   987
>     ***987
>     98,765
>  ***98,765
>     ***.09
>     $98.76
>  $***98.76
>  Val: 8.76
> uts a 5 in
> ```

### Explicit justify
When you make a numeric-to-alpha assignment the formatted information gets loaded right-justified by default. To change it, you can include a justification control—either `LEFT` or `RIGHT`—at the end of the assignment statement, like this: `statement[ [justification[:variable]]]`. Here, variable is updated with the number of characters loaded into the destination, not counting leading blanks.

The following examples illustrate the usage and combination with explicit formatting. As with explicit formatting, this syntax does not work when the assignment operator is not by itself on the line: <!--Do we mean "...when the assignment operation is not the only thing on the line?--> 

```dbl
record
        alpha       ,a10
        len         ,i2
proc
    len = 7
    Console.WriteLine(alpha = 12345)
    alpha = 12345 [LEFT]
    Console.WriteLine(alpha)
    alpha = 12345 [RIGHT:len]
    Console.WriteLine(alpha)

    len = 6
    alpha = 12345, "ZZ,ZZZ.ZZ-"
    Console.WriteLine(alpha)
    alpha = 12345, "ZZ,ZZZ.ZZ-" [LEFT]
    Console.WriteLine(alpha)          
    alpha = 12345, "ZZ,ZZZ.ZZ-" [RIGHT:len]
    Console.WriteLine(alpha) 
```

> #### Output
> ```
>      12345
> 12345
>      12345
>    123.45
> 123.45
>    123.45
> ```
# Math
`Would Arithmetic be a more accurate title?`
Arithmetic operations are performed using various operators such as addition (+), subtraction (-), multiplication (*), division (/ and //), and, when running on .NET, modulo (.mod.). These operators execute standard, signed, whole number arithmetic if the operands don't have an implied decimal point. However, if at least one operand contains an implied decimal point, the intermediate result will also include a fractional portion.

Unary plus (+) and minus (-) operators have a special function to denote whether a numeric operand is positive or negative. The unary plus operator is generally ignored, as unsigned values are assumed to be positive, whereas the unary minus operator changes the sign of the operand to its right. If multiple minuses are used consecutively, they are combined algebraically, with two minuses forming a plus, three minuses forming a single minus, and so forth. Importantly, the resulting data type from such operations will mirror that of the operand.

In instances where different numeric types are involved in the same operation, the lower type in the numeric data hierarchy (which includes integer, decimal, implied-decimal, packed, and implied-packed types) is upgraded to match the higher one. Therefore, if an integer and an implied-decimal value are part of the same expression, the integer will be promoted to an implied-decimal before the operation, resulting in an implied-decimal outcome.

In division operations, it's important to remember that dividing by zero is not allowed and will result in an error. If the division operation ("/") involves two whole numbers, the fractional part of the result will be discarded without rounding. However, using the "//" division operator always results in an intermediate result that includes a fractional portion. For the modulo operation, available only in DBL on .NET, it provides the remainder of a division operation between two numbers.

Lastly, it's crucial to note that when a numeric field is used in an arithmetic expression, the language checks for invalid characters or an excessive length. Nonnumeric characters other than a blank, a decimal point, or a sign (+ or -) are not allowed in the operands, and any violation of these rules results in errors. If invalid characters sound odd to you, rest assured we will explain how this happens and how to avoid it when discussing project structure and prototyping. Similarly, if a field exceeds the maximum size for its data type, an error is generated.

### Explicit Rounding
When manipulating financial data, such as calculating taxes or discounts, accurate rounding of decimal values is a hard requirement. Even slight rounding errors can accumulate over numerous transactions, leading to significant discrepancies. Similarly, when processing user input, rounding to the nearest whole number or a specific decimal place is useful in maintaining output consistency and limiting the precision to an appropriate level for the application's context. Moreover, while it may be beneficial to retain higher precision in internal computations, rounding can enhance readability when presenting these results to users. In this way, rounding serves multiple purposes, from ensuring accuracy in financial operations to streamlining user interactions and enhancing data presentation.

Rounding operators, specifically the # and ## operators, are often used to manipulate numeric values in precise ways. The # operator is a binary operator that serves to round the left operand, or the value being rounded, according to the number of digits specified by the right operand, the round value. For instance, if the round value is 3, the operation will discard the rightmost three digits of the value being rounded and add 1 to the resulting number if the leftmost discarded digit is 5 or greater.

Both operators follow these shared rules:

1.  Both operands must be decimal or integer.
2.  The sign of the result is the same as the sign of the value being rounded.
3.  If the number of significant digits in the value being rounded is less than the round value, the result is zero.

The `#` operator, also known as the truncating rounding operator, rounds the left operand (the value being rounded) by the number of digits specified by the right operand (the round value). The distinctive rules for the `#` operator are as follows:

1.  The round value must be in the range 1 through 28. Any value outside this range triggers an "Invalid round value" error.
2.  The resulting data type is identical to that of the original operand.

Conversely, the `##` operator allows for true rounding (rounding without discarding any digits in the value to be rounded). Its unique rules are as follows:

1.  The round value must be in the range -28 through 28.
2.  If the round value is positive, rounding begins "round value" places to the left of the decimal point. For example, if the round value is 3, rounding begins three places to the left of the decimal point.
3.  If the round value is negative, rounding begins "round value + 1" places to the right of the decimal point. For example, if the round value is -1, rounding begins two places to the right of the decimal point.
4.  The resulting data type is always decimal.

### Examples
The following examples demonstrate the use of the `#` and `##` operators:

```dbl
record
        tmpdec, d9, 12345
proc
    Console.writeLine(%string(12345.6789 ## 3))
    Console.writeLine(%string(12345.6789 ## 1))
    Console.writeLine(%string(12345.6789 ## 30))

    Console.writeLine(%string(12345.6789 ## -3))
    Console.writeLine(%string(12345.6789 ## -1))
    Console.writeLine(%string(12345.6789 ## -30))

    Console.writeLine(%string(tmpdec # 3))
    Console.writeLine(%string(tmpdec # 1))
    ;;fails with DBR-E-RNDVAL, Invalid round value:  30
    Console.writeLine(%string(tmpdec # 30))
```

> #### Output
> ```
> 12000
> 12350
> 0
> 12345.679
> 12345.7
> 12345.6789
> 12
> 1235
> 
> %DBR-E-RNDVAL, Invalid round value:  30
> ```

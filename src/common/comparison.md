# Comparison

Comparison, or relational, operators are used to evaluate the relationship between two values, or operands. They return a boolean result, true or false, which is internally represented as 1 and 0, respectively. Relational operators are predominantly used in conditional statements, like 'if', 'using', or 'while' loops, to guide program flow based on the results of comparisons. For instance, they can be used to check if a user input matches a specific value, if a number is greater or smaller than a threshold, or if a string is alphabetically before or after another one. By comparing values and acting upon those comparisons, programs can make decisions and perform actions that adapt to specific conditions or user inputs. We'll talk more about controlling program flow in the next section.

Here are the comparison operators:

1.  **Equal to (`==` or `.EQ.`)**: Returns `true` if the left and right operands are equal.
2.  **Not equal to (`!=` or `.NE.`)**: Returns `true` if the left and right operands are not equal.
3.  **Greater than (`>` or `.GT.`)**: Returns `true` if the left operand is greater than the right one.
4.  **Less than (`<` or `.LT.`)**: Returns `true` if the left operand is less than the right one.
5.  **Greater than or equal to (`>=` or `.GE.`)**: Returns `true` if the left operand is greater than or equal to the right one.
6.  **Less than or equal to (`<=` or `.LE.`)**: Returns `true` if the left operand is less than or equal to the right one.

Comparing Alphanumeric and String Operands
-------------------------------------------------

When you are comparing alphanumeric (`alpha`) operands or `System.String` operands, it's essential to understand their differences. `==` is translated to mean `.EQ.` when both operands are `alpha` but `.EQS.` if one of the operands is a `System.String`. 

### Alphanumeric Comparisons

The `alpha` type refers to a sequence of characters that has a fixed length. When you compare two `alpha` operands, you're comparing them character by character, based on the order of characters in the ASCII character set. The comparison only considers the length of the shorter operand, meaning that if one operand is shorter, only the first N characters (where N is the length of the shorter operand) of the longer operand are considered in the comparison. This might be considered odd, but it's a necessary behavior because alphas are fixed size and don't grow or shrink with their contents.

### String Comparisons

Unlike `alpha`, `System.String` operands are not fixed in length. They are expected to be exactly the size of their intended contents. When one of the operands in a comparison is a `System.String`, the DBL compiler assumes both operands are `System.String` or can be converted to `System.String`, and uses the corresponding string comparison operator. This makes string comparisons more like the comparisons seen in other programming languages.


Boolean Operators
------------------------

In addition to comparison operators, we have Boolean operators. They compare the truth value of operands and return `true` or `false` just like comparison operators. Here they are:

1.  **OR (`||` or `.OR.`)**: Returns `true` if either operand is `true`.
2.  **Exclusive OR (`.XOR.`)**: Returns `true` if exactly one operand is `true`.
3.  **AND (`&&` or `.AND.`)**: Returns `true` if both operands are `true`.
4.  **NOT (`!` or `.NOT.`)**: Returns `true` if the operand is `false`.

> Like most programming languages, DBL evaluates Boolean operators from left to right. If the result can be determined by the left operand, DBL won't process the right operand. This is known as short-circuit evaluation.

Ternary Operator
---------------------------

The ternary operator, also known as the conditional operator, is a concise way to perform simple if-else logic in a single line of code. It is called 'ternary' because it takes three operands: a condition, a result for when the condition is true, and a result for when the condition is false. The general syntax is condition ? result_if_true : result_if_false. This operator is incredibly useful for simplifying code when assigning a value to a variable based on a condition. It can make the code more readable by reducing the need for more verbose control structures, especially in situations where the control flow logic is straightforward. However, caution should be taken not to overuse the ternary operator or use it in overly complex expressions, as it can lead to code that is difficult to read and understand. Always consider the balance between brevity and clarity in your code.

Here's a few example expressions, notice from the output that true is 1 and false is 0:

```dbl
proc
    ;;alpha comparisons
    Console.WriteLine("ABCDEF" .eq. "ABC")
    Console.WriteLine("ABCDEF" .eq. "ABD")
    Console.WriteLine("ABCDEF" .eq. "abc")

    ;;string style comparisons
    Console.WriteLine("ABCDEF" .eqs. "ABC")
    Console.WriteLine("ABCDEF" .eqs. "ABCDEF")
    Console.WriteLine("ABCDEF" .eqs. "abcdef")
    Console.WriteLine("ABCDEF" .eqs. "FEDCBA")

    Console.WriteLine(5 > 8)
    Console.WriteLine(5 < 8)

    Console.WriteLine(true && false)
    Console.WriteLine(true && true)

    Console.WriteLine(true || false)
    Console.WriteLine(true || true)

    Console.WriteLine(5 > 8 ? "how did this happen" : "everything normal")
```

> #### Output
> ```
> 1
> 0
> 0
> 0
> 1
> 0
> 0
> 0
> 1
> 0
> 1
> 1
> 1
> everything normal
> ```
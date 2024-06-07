# Comparison

Comparison, or relational, operators are used to evaluate the relationship between two values, or operands. They return a Boolean result, true or false, which is internally represented as 1 and 0, respectively. Relational operators are predominantly used in conditional statements, like IF, USING, or WHILE loops, to guide program flow based on the results of comparisons. For instance, they can be used to check if a user input matches a specific value, if a number is greater or smaller than a threshold, or if a string is alphabetically before or after another one. By comparing values and acting upon those comparisons, programs can make decisions and perform actions that adapt to specific conditions or user inputs. We'll talk more about controlling program flow in the next section.

Here are the basic comparison operators:

- **Equal to (== or .EQ.)**: Returns `true` if the left and right operands are equal.
- **Not equal to (!= or .NE.)**: Returns `true` if the left and right operands are not equal.
- **Greater than (> or .GT.)**: Returns `true` if the left operand is greater than the right one.
- **Less than (< or .LT.)**: Returns `true` if the left operand is less than the right one.
- **Greater than or equal to (>= or .GE.)**: Returns `true` if the left operand is greater than or equal to the right one.
- **Less than or equal to (<= or .LE.)**: Returns `true` if the left operand is less than or equal to the right one.

These descriptions are accurate for numbers and generally true for strings and objects, but things get a little weird when comparing alphas. So this is going to take a lot of example code to explain.

### Alpha comparisons
Alphas have their own set of comparison operators. They use the same symbolic operators like `==` and `!=`, but they have different meanings. The following table shows the alpha comparison operators and their symbolic equivalents:

- **Equal to (== or .EQ.)**: Returns `true` if operands are equal in content and length or equal in content up to the shorter of the two operands.
- **Not equal to (!= or .NE.)**: Returns `true` if operands are not equal in content and length or not equal in content up to the shorter of the two operands.
- **Greater than (> or .GT.)**: Returns `true` if the left operand is greater than the right one, considering at most the length of the shortest of the two operands.
- **Less than (< or .LT.)**: Returns `true` if the left operand is less than the right one, considering at most the length of the shortest of the two operands.
- **Greater than or equal to (>= or .GE.)**: Returns `true` if the left operand is greater than or equal to the right one, considering at most the length of the shortest of the two operands.
- **Less than or equal to (<= or .LE.)**: Returns `true` if the left operand is less than or equal to the right one, considering at most the length of the shortest of the two operands.

The alpha type refers to a sequence of characters that has a fixed length. When you compare two alpha operands, you're comparing them character by character, based on the order of characters in the ASCII character set. The comparison only considers the length of the shorter operand, meaning that if one operand is shorter, only the first *n* characters (where *n* is the length of the shorter operand) of the longer operand are considered in the comparison. This is definitely considered odd in the world of programming languages, but it's stuck that way now.<!--Maybe "but that's the way it is" instead.--> As an example, consider the following expressions:

```dbl,ignore,does_not_compile
data alpha1, a6, "ABCDEF"
data alpha2, a3, "ABC"

;this returns true because the first 3 characters of alpha1 are equal to alpha2
alpha1 .eq. alpha2
;because both operands are alpha, == works like .eq above
alpha1 == alpha2

;this returns false because the alphas are a different length
alpha1 .eqs. alpha2
```

If you want to compare the entire contents of two alpha operands, you can use the alpha comparison operator .EQ.. This operator will return `true` only if both operands are the same length and contain the same characters in the same order. For example, the following expression will return `false`:

```dbl,ignore,does_not_compile
"ABCDEF" .eq. "ABC"
```

There is no direct way to perform a case insensitive comparison on alphas, but the common approach is to call UPCASE on both operands before comparing them. For example, the following expression will return `true`:

```dbl,ignore,does_not_compile
upcase(alpha1)
upcase(alpha2)
alpha1 .eq. alpha2
```

It's important to note, though, that UPCASE/LOCASE will change the contents of the operands, so if you need to preserve the original values, you'll need to make a copy of the operands before calling UPCASE/LOCASE.

### String comparisons

Unlike alpha, System.String operands are expected to be exactly the size of their intended contents. When one of the operands in a comparison is a System.String, the DBL compiler assumes both operands are System.String or can be converted to System.String and uses the corresponding string comparison operator. This makes string comparisons more like the comparisons seen in other programming languages. Consider the following  snippet of code that shows a few string comparisons:

```dbl,ignore,does_not_compile
data alpha1, a6, "ABCDEF"
data alpha2, a3, "ABC"

;;this one is true because .eq. of two strings works like .eq. of two alphas
string1 .eq. string2 
;;this one is true because both operands are actually the same
string1 .eq. "ABCDEF" 

;;these expressions all evaluate to false
string1 .eqs. string2 
string1 .eqs. "ABC   " 
string1 .eqs. alpha2
string1 == string2 
string1 == "ABC   "
string1 == alpha2

```

As you can see, it's pretty easy to get confused. Here's the same table as above, but you can apply these rules if at least one of the operands is a System.String:

1.  **Equal to (== or .EQS.)**: Returns `true` if the left and right operands are equal.
2.  **Not equal to (!= or .NES.)**: Returns `true` if the left and right operands are not equal.
3.  **Greater than (> or .GTS.)**: Returns `true` if the left operand is greater than the right one.
4.  **Less than (< or .LTS.)**: Returns `true` if the left operand is less than the right one.
5.  **Greater than or equal to (>= or .GES.)**: Returns `true` if the left operand is greater than or equal to the right one.
6.  **Less than or equal to (<= or .LES.)**: Returns `true` if the left operand is less than or equal to the right one.

In Traditional DBL, there is no direct way to perform a case-insensitive comparison on strings, but the common approach is to call ToUpper/ToLower on both operands before comparing them. Unlike calling UPCASE/LOCASE, this will not modify the contents of the operands. As an example showing a case-insensitive comparison, the following expression will return `true`:

```dbl,ignore,does_not_compile
string1.ToUpper() == string2.ToUpper()
```

In DBL running on .NET you can use the System.String comparison method directly. For an example showing a case-insensitive comparison, the following expression will return `true`:

```dbl,ignore,does_not_compile
string1.Compare(string2, true) == 0
```

## Ternary Operator
> TODO: move this into control flow to highlight its similarity to IF/ELSE
The ternary operator, also known as the conditional operator, is a concise way to perform simple IF-ELSE logic in a single line of code. It is called "ternary" because it takes three operands: a condition, a result for when the condition is true, and a result for when the condition is false. The general syntax is `condition ? result_if_true : result_if_false`. This operator is incredibly useful for simplifying code when assigning a value to a variable based on a condition. It can make the code more readable by reducing the need for more verbose control structures, especially in situations where the control flow logic is straightforward. However, you should take caution not to overuse the ternary operator or use it in overly complex expressions, as it can lead to code that is difficult to read and understand. Always consider the balance between brevity and clarity in your code.

Here are a few example expressions. Notice from the output that true is 1 and false is 0:

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
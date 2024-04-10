# Strings
The term "string" is a fundamental concept representing a sequence of characters. These characters can range from letters and numbers to symbols and whitespace. Strings are used in virtually all programming languages to handle and manipulate text data. In DBL, two types are primarily used for storing string data: alpha and the built-in class System.String.

To avoid confusion, it's important to understand that "strings" as a universal concept differ from System.String in terms of abstraction levels. While "strings" are a ubiquitous programming concept, System.String is a specific implementation in Traditional DBL and .NET. It provides a set of built-in methods to handle and manipulate text-based data.

Both alphas and System.String types are extensively used for various purposes, like storing and displaying messages and representing names, addresses, and virtually any other type of written information.

Common string operations include

-   Concatenation: Joining two or more strings together.
-   Length calculation: Determining the number of characters a string contains.
-   Substring extraction: Taking part of a string.
-   String replacement: Replacing a specific part of a string with another string.
-   Case conversion: Changing a string to all uppercase or all lowercase letters.
-   Comparison: Checking if strings are identical or determining their alphabetical order.

> ### TODO Note
> include when to use LOCALIZE, INSTR, LOCASE, UPCASE, %STRING, %ATRIM, %ATRIMTOSTRING, %TRIMZ, TRIM vs ATRIM, %CHAR, %UCHAR, make note of ranging in the advanced_memory_managment chapter 

# Alpha-specific routines
These routines are specific to alpha data, and while the compiler will allow you to pass a string to them, you will get unexpected results if the length of the string data is larger than the size limit for alphas on your platform. As a general recommendation, avoid using these routines with strings.

## %ATRIM
%ATRIM is used to remove trailing spaces from an alpha. Its syntax is

`trimmedAlpha = %ATRIM(alpha_expression)`

The %ATRIM function is designed to return an alpha descriptor that points to the same original alpha expression, but with its length adjusted to exclude any trailing blanks. This means that while the returned alpha descriptor represents the trimmed version of the string, the underlying data in memory remains unchanged. For example, if you have an alpha string 'Hello ' (with trailing spaces), %ATRIM will return a descriptor that effectively 'views' this string as 'Hello' without the spaces. However, the original string in memory still contains the spaces. This subtle yet significant behavior of %ATRIM ensures that the original data is preserved, allowing for efficient string manipulation without the overhead of duplicating data. It is particularly useful in any scenarios where you have a large alpha buffer and don't want record `**I think there's a word or words missing here.` or show a bunch of extra whitespace.

## %ATRIMTOSTRING
%ATRIMTOSTRING is used to remove trailing spaces from an alpha and return the result as a string. Its syntax is

`trimmedString = %ATRIMTOSTRING(alpha_expression)`

%ATRIMTOSTRING works just like %ATRIM, but it's more efficient in scenarios where the ultimate destination is a System.String. This is a very common use case when using the newer built-in APIs and/or libraries from the .NET ecosystem.

## %TRIM / %TRIMZ
%TRIM returns the length of the passed-in expression minus the quantity of trailing whitespace. Its syntax is

`trimmedLength = %TRIM(alpha_expression)`

The trimmed length for an otherwise blank alpha expression is 1 for %TRIM and 0 for %TRIMZ. That's the entire difference between the two. My preference is %TRIMZ because I want to treat blank strings as blank strings and not as strings with a single space in them. I think this is a more intuitive behavior, but you may see code that uses %TRIM instead.

## UPCASE / LOCASE
UPCASE and LOCASE are used to convert an alpha to all uppercase or lowercase. They are similar to the ToUpper and ToLower methods on System.String. The main functional difference between UPCASE/LOCASE and ToUpper/ToLower is that UPCASE/LOCASE performs its operation in place, modifying the original data. The syntax is

`UPCASE(alpha_expression)`
`LOCASE(alpha_expression)`

# System.String specifics

An important thing to note is that System.String is "immutable," meaning it can't be changed once created. Any operation that seems to modify a System.String is actually creating a new one. Also, since System.String is an object, it can't be included directly in a record or structure intended for writing to disk or network transmission. While it's possible to perform these operations, extra coding is required due to the object's non-fixed size at compile time.

System.String can be treated as a collection of characters. In .NET, this is a Fixed-width 16-bit character set commonly referred to as UTF-16, but the actual implementation is an earlier variant, UCS-2. In Traditional DBL System.String is made up of 8-bit characters. Because System.String is a [collection](../collections/collections.md), you can index into it or iterate over it using a FOREACH loop.

# Building strings

Options for string concatenation and construction include `+` directly on a System.String or alpha, StringBuilder, and S_BLD. StringBuilder gets its design from .NET and is an optimal choice for operations requiring repetitive concatenation due to its improved performance. S_BLD predates StringBuilder and has significantly more options for formatting the DBL types

The syntax for S_BLD is

`xcall S_BLD(destination, [length], control[, argument, ...])`

In this syntax, "destination" is the variable loaded with the formatted string. It can be either an alpha or a StringBuilder type. "Length" is an optional variable loaded with the formatted string's length. "Control" is the processing control string for the build, and "argument" can include up to nine arguments as required by the control string.

The S_BLD subroutine builds a formatted string. If the destination is an alpha field, it's cleared before being loaded with new text from the first position. However, if the destination is a StringBuilder object, the generated string is appended to the object's existing text.

An example using S_BLD is

`xcall s_bld(sb,,"%%+07.03=d => {%+07.03=d}", f2)`

The control string can consist of one or more text and/or format segments. Text segments are copied directly into the destination field. If the first two characters of the control are "%&", the output string is appended to the existing content. Otherwise, the destination is replaced with new text.

To achieve results equivalent to a Synergy decimal-to-alpha conversion, use a control string of "%0.0nd", where "n" is the desired precision.

A format segment takes the next argument from the S_BLD call and formats it into the destination field. It follows this syntax:

`%[justification size[.precision]][=]type`

Here, "justification" can be either left or right within the specified size. "Size" is the minimum width of the formatted result field, and "precision" is the displayed fractional precision. The presence of "=" indicates no leading strip processing. "Type" specifies the type of the next argument to be consumed, either "A" for alpha type or "D" for numeric type.
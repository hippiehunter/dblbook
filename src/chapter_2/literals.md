# Literals
In DBL, the term 'literal' refers to a specific, unchangeable value that's defined at compile time. This value can either be a textual literal, which is a sequence of characters enclosed between matching single or double quotation marks, or a numeric literal, which consists of numeric characters and can be prefixed with a plus or minus sign.

Now, let's look at the various types of literals:

#### Alpha Literals

An alpha literal is essentially a string of characters, enclosed in either single or double quotation marks, and can be up to 255 characters long. For example:
```
'This is an ALPHA literal'
"This is also an ALPHA literal"
```

If you need to include a quotation mark within an alpha literal, you must use two successive quotation marks. However, if the embedded character is different from the ones delimiting the literal, no such doubling is required.

You may also split an alpha literal into smaller parts, with the DBL compiler concatenating these parts if separated by blanks and tabs. Remember that alpha literals are case sensitive.

#### Decimal Literals

A decimal literal is a sequence of numeric characters, which can be positive or negative, and can include up to 28 digits. Leading zeros without significance are removed by the compiler. Examples include:
```
273949830000000000
1
-391
```
#### Implied-Decimal Literals

Implied-decimal literals are similar to decimal literals but include a decimal point. There can be up to 28 digits on either side of the decimal point. For instance:

```
1234.567
123456789012345678.0123456789
+18.10
0.928456989
-518.0
```

The compiler removes nonsignificant leading or trailing zeros before processing.

#### Integer Literals

In DBL, you can't directly write integer literals like you would with alpha, decimal, or implied-decimal literals. However, when a decimal literal is part of an arithmetic expression with an integer variable, the compiler generates an integer literal in the code.

#### Literal definition
The *literal* declaration allows you to define a local, read-only data structure that cannot be altered during the execution of the program. The companion statement *endliteral* marks the conclusion of this declaration.

You can declare a literal within a routine. Depending on your needs, a literal can be either *global*, which results in the allocation of data space, or *external*, which simply references the data space. The fields within an *external literal* map to the data in the corresponding *global literal* based on matching field names. If you do not specify either *global* or *external*, the literals are added to the local literal space.

When you provide a name for your literal, it can be accessed as an alpha data type. If no name is given, the literal is deemed unnamed. Bear in mind that each literal's name must be distinct within the routine it is declared in.

For unnamed literals, the data space related to a field is not created until the field is referenced for the first time. In contrast, if a literal is named, all of its contents are instantiated unconditionally. If a group is defined within an unnamed literal, the group's contents are also unconditionally created, even if they are not referenced.

Note that if you do not specify the size of an integer global literal, DBL defaults the size to i4. As an example, in the following definition, 'lit' is created as an i4 literal:

```dbl,ignore,does_not_compile
global literal
    lit     ,i,         2
```

For an external literal, automatic size specification (*) is not permitted.

You are only allowed to specify a field position indicator for a literal field under two conditions: if the literal record is named, or if the field is located within a *group-endgroup* block. For instance, the following would be considered an invalid literal declaration:

```dbl,ignore,does_not_compile
literal                     ;Invalid literal declaration
    lit1    ,i4
    lit2    ,i4  @lit1
```

it's possible to overlay a literal record onto another if the overlaid record is named. However, you cannot specify a packed data field within the scope of a literal field.

#### Boxing Literals
In the .NET environment, literals or those cast as type 'object' undergo a type conversion from a DBL literal type to a .NET literal type before being boxed. For instance, the literal "abc" is changed to a string type, and the number 10 becomes @int. If you wish to retain an alpha, decimal or implied decimal literal type, simply cast the literal as the desired DBL type (@a, @d, @id). Boxing literals usually happens when adding a literal directly to an object collection or passing it as a parameter to a routine that takes an object parameter.
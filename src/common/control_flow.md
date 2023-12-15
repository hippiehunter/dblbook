# Control Flow

### IF-THEN-ELSE
IF is the most basic statement that allows for conditional control flow in a DBL program. The IF statement checks a specified condition (a statement that evaluates to a Boolean value), and if the condition is true, it executes an associated statement or block of code (a BEGIN-END block).

Here's a basic example: `**Why BEGIN and END? With simple statements we can omit these, right, and perhaps we should have at least one example that shows IF without BEGIN-END. But really there's a bigger issue here. Throughout this chapter we talk about "the statement" that will be executed for the if/loop/etc., but really it could be a statement or a block of statements in each case I suspect. It might be a bit wordy to say "the statement of block of statements" whenever we're talking about "the statement", so maybe "the statements"? E.g., "If the ELSE-IF condition evaluates to true, its corresponding statements are executed." Maybe this wouldn't be too awkward. But of course this sort of change isn't necessary if readers will understand "the statement" as meaning a statement or a BEGIN-END block of statements. `

```dbl,ignore,does_not_compile
if x > y
begin
    ; This statement will be executed if x is greater than y
    Console.WriteLine("x is greater than y")
end
```

An ELSE statement is used in conjunction with an IF statement to provide an alternative branch of execution when the IF condition is not met&mdash;i.e., when it evaluates to false. The THEN keyword is required in this case, and it makes the syntax more readable, clearly defining the separate paths of execution. The general structure is this: `IF condition THEN statement_1 ELSE statement_2`. You will often see the form `IF(condition) statement` in DBL code. The parentheses can improve readability but are entirely optional.

```dbl,ignore,does_not_compile
if x > y then
begin
    ; This statement will be executed if x is greater than y
    Console.WriteLine("x is greater than y")
end
else
begin
    ; This statement will be executed if x is not greater than y
    Console.WriteLine("x is not greater than y")
end
```

The ELSE-IF statement makes it possible to specify multiple alternative conditions. It is used after an IF and before a final ELSE statement. If the initial IF condition evaluates to false, the program checks the first ELSE-IF condition. If the ELSE-IF condition evaluates to true, its corresponding statement or code block is executed. If not, the program proceeds to the next ELSE statement, if there is one. The THEN keyword is required for the initial IF and each ELSE-IF until the last ELSE.

```dbl,ignore,does_not_compile
if x > y then
begin
    ; This statement will be executed if x is greater than y
    Console.WriteLine("x is greater than y")
end
else if x = y then
begin
    ; This statement will be executed if x is equal to y
    Console.WriteLine("x is equal to y")
end
else
begin
    ; This statement will be executed if x is not greater than y and x is not equal to y
    Console.WriteLine("x is less than y")
end
```

Here's another example that shows when THEN is required and when it is not allowed: `**"fld1 is on its way up" doesn't quite make sense to me. It's just less than 9000 and greater than 100.`

```dbl,ignore,does_not_compile
record
    fld1, d10, 99999
proc
    if(fld1 > 100) then
        Console.WriteLine(fld1)
    else if(fld1 < 9000) ;;The missing THEN causes the first error shown in the output (shown below)
        Console.WriteLine("fld1 is on its way up")
    else
        Console.WriteLine("fld1 was over 9000!")


    if(fld1 > 100) then
        Console.WriteLine(fld1)
    else if(fld1 < 9000) then ;;This THEN causes the second error in the output
        Console.WriteLine("fld1 is on its way up")
```

> #### Compiler Output
> ```
> %DBL-E-INVSTMT, Invalid statement at or near {END OF LINE} : else
> %DBL-E-NOSPECL, Else part expected : Console.WriteLine("fld1 is on its way up")
> ```

A newline is not required between an IF statement's condition and its statement:

```dbl,ignore,does_not_compile
if x > y then
    Console.WriteLine("x is greater than y")
else if x = y then Console.WriteLine("x is equal to y")
else Console.WriteLine("x is less than y")
```

## Multi-way control flow
Complex control flow statements can be an improvement over long chains of IF-THEN-ELSE. These statements operate in ways that are similar to the "switch" statement in the C family of languages. There are three main variants:

- CASE
- USING
- USING-RANGE

These all have a similar purpose: an expression is evaluated and then one of several possible statements or code blocks, or none at all, is executed depending on the value of the expression. However, there are important differences in their syntax, how they evaluate conditions, and their performance characteristics.

### CASE

CASE is the most basic of the multi-way control statements. It selects from a set of unlabeled or labeled statements, based on the value of a control expression. 

- **Unlabeled CASE** - The control expression is non-implied numeric`**what exactly do we mean by "non-implied numeric?` and is interpreted as an ordinal number 1 through *n*, where *n* is the number of statements in the set. For example, a value of 6 selects the sixth statement.

- **Labeled CASE** - All statements are identified with labels that must be literals and of the same type as the control expression. Labels can be a single literal or a range (delineated with a hyphen). The control expression is matched with these labels to select the corresponding statement.

ELSE is used to specify a block of code to be executed if no case labels match the value of the switch expression. It's akin to an ELSE clause in an IF-THEN-ELSE conditional block. If the ELSE case is not provided and no match is found, the case statement will simply do nothing.
`**why do we indent statements within BEGINCASE-ENDCASE  when they're unlabeled, but not when they're labeled?`

```dbl
record
    num, i4
    color, a10
proc
    num = 2
    color = "red"
    ;;Labeled case
    case num of
    begincase
    1:  Console.WriteLine("The number is 1")
    2:  Console.WriteLine("The number is 2")
    3:  Console.WriteLine("The number is 3")
    endcase
    else
        Console.WriteLine("The number is some other number")

    case num of
    begincase
    1-5:  Console.WriteLine("The number is between 1 and 5")
    6-9:  Console.WriteLine("The number is between 6 and 9")
    10-15:  Console.WriteLine("The number is between 10 and 15")
    endcase

    ;;Labeled case - alpha
    case color of
    begincase
    "red":  Console.WriteLine("The color is red")
    "blue":  Console.WriteLine("The color is blue")
    "green":  Console.WriteLine("The color is green")
    endcase
    else
        Console.WriteLine("The color is something else")

    ;;Unlabeled case
    case num of
    begincase
        Console.WriteLine("The number is 1")
        Console.WriteLine("The number is 2")
        Console.WriteLine("The number is 3")
    endcase
```

> #### Output
> ```
> You entered 2
> You entered something between 1 and 5
> You entered red
> You entered 2
> ```

### USING

The USING statement selects a statement for execution based on the evaluation of a control expression against one or more match term conditions. Each match term is evaluated from top to bottom and left to right`**These phrases ("from top..." and "left to...") probably need more explanation`. Once a match is found, no other condition is evaluated. The USING statement is more efficient than CASE when using an `i` or `d` control expression and when all match terms are compile-time literals. `**Mention that empty parens are the default if they're the final match term condition?`

```dbl
record
    code, a2
proc
    code = "AA"

    using code select
    ('0' thru '9'),
    begin
        ;;BEGIN-END can be used here
        Console.WriteLine("matched 0 thru 9")
    end
    ('A' thru 'Z'),
        Console.WriteLine("matched A thru Z")
    ("99", '$'),
        Console.WriteLine("matched 99 or $")
    (.gt.'z'),
        Console.WriteLine("matched greater than z")
    (),
        Console.WriteLine("fell through to the default")
    endusing
```


> #### Output
> ```
> matched A thru Z
> ```

### USING-RANGE

The USING-RANGE statement is similar to USING but adds a range for the control expression. This allows you to define a range of values within which the control expression is evaluated, enabling you to supply separate default statements for values that fall within the range (by using the %INRANGE label) and values that are outside the range (by using the %OUTRANGE label). The USING-RANGE statement builds a dispatch table at compile time and is typically faster than the USING statement. `**mention that "in traditional Synergy, implied values used by control, low, high, and match_value are always rounded or truncated in the USING-RANGE statement"?`

In the following example, the range 1-12 is specified for the USING-RANGE statement, so any value from 1 through 12 is considered in range and will invoke the (%INRANGE) statement if there is no more specific match. In the example, however, there is a more specific match, (3), so the `monthName = "March"` statement is executed. If `month` had instead been set to 10, the %INRANGE statement would be executed (resulting "shrug"). But if `month` had been set to 13, the %OUTRANGE statement would be executed, and the output would be "wild month".


```dbl
record
    month,        int
    monthName,    a10

proc
    month = 3   ; Let's assume the month is March

    USING month RANGE 1 THRU 12 SELECT
    (1),
        monthName = "January"
    (2),
        monthName = "February"
    (3),
        monthName = "March"
    (4),
        monthName = "April"
    (%OUTRANGE), 
        monthName = "wild month"
    (%INRANGE),  
        monthName = "shrug"
    ENDUSING

    Console.WriteLine(monthName)
```

> #### Output
> ```
> March
> ```

> #### Quiz
> What does this program output if month is 5 instead of 3?
> What does this program output if month is 5555 instead of 3?


While each of these multi-way control mechanisms has its uses, in most modern coding scenarios USING tends to be the go-to choice due to its flexibility and powerful matching conditions. The CASE statement is straightforward and simple to use, and you'll frequently encounter it in legacy code (it was developed earlier than USING), but it is generally slower. Also, the USING-RANGE statement provides a slight efficiency boost when a control expression is evaluated within a predefined range.

It's important to consider several factors when deciding which mechanism to use in your specific use case. These include the complexity of your matching conditions, the need for a defined range for the control expression, and the importance of execution speed. However, given its power and versatility, the USING statement is often a sensible default choice for new code.

## Loops

Loops are foundational constructs used to automate and repeat tasks a certain number of times, or until a specific condition is met. FOR loops are typically used in cases where the exact number of iterations is known beforehand, whereas WHILE and WHILE-DO loops are more suitable when iterations depend on certain conditions. FOREACH is used to improve readability when operating over a collection or dynamic array. `**If FOR-DO can't iterate over a collection (and I haven't seen anything yet to indicate it can), this last statement doesn't work--i.e., it's not "used to improve readability when operating over a collection or dynamic array"; it's simply "used to iterate over a collection." It'd be the only mechanism for this, right? Also, it doesn't seem that the phrase "collection or dynamic array" quite covers it. The FOREACH doc lists a number of things it can iterate over.`

There are other methods to handle repetition in code. For instance, recursion, where a function calls itself, is an alternative that can be more intuitive for certain tasks, such as traversing tree-like data structures. However, recursion may lead to higher memory usage and potential stack overflow errors if not used correctly. Furthermore, earlier, less structured looping mechanisms, such as the GOTO statement, can be used to jump to different points in the code. Some of these less structured mechanisms can be useful, but GOTO, which provides a great degree of freedom, often leads to "spaghetti code," which is hard to read and maintain due to its lack of structure. We'll discuss GOTO and other les structured mechanisms in [Unconditional control flow](#unconditional-control-flow) below.

### FOR-FROM-THRU
The FOR-FROM-THRU loop (`FOR variable FROM initial THRU final [BY incr]`) executes a statement as long as the value of a variable (*variable*) is within the specified range. The variable's value is incremented after each iteration. The default increment value is 1, but you can specify an increment amount by using `BY incr`.

```dbl
record
        var, i4
        initial, i4, 0
        final, i4, 5
proc
    for var from initial thru final by 1
    begin
        Console.WriteLine(var)
    end

    for var from 5 thru 7 by 1
    begin
        Console.WriteLine(var)
    end

```

> #### Output
> ```
> 0
> 1
> 2
> 3
> 4
> 5
> 5
> 6
> 7
> ```

### WHILE
The WHILE loop (`WHILE condition [DO] statement`) continues as long as the specified condition is true. Once the condition is no longer true, the loop will be exited. DO is optional and has no effect on the loop. But if it is there, the statement to be executed must be on the same line as WHILE. `**would an example be helpful?`

### FOREACH-IN
The FOREACH-IN loop (`FOREACH loop_var IN collection [AS type]`) iterates over each element in a collection, setting a loop variable (*loop_var*) to each element in turn and executing the statement`**Should we list what types of "collections" this can iterate over?`. Note that the loop variable must be the same type as the elements in the collection, or an "Invalid Cast" exception will occur. We'll cover collections and arrays in more detail in the [Collections](../collections/collections.md) chapter.

You can use DATA to declare the iteration variable directly inside a FOREACH loop. If the compiler can't infer the type of the variable, you will need to specify it using the `AS type` syntax, which is discussed below. Here's an example with and without an inline variable declaration:

```dbl
record
        string_array, [#]String
        explicit_iteration_variable, @String
proc
    string_array = new String[#] { "hello", "for", "each", "loops" }
    foreach data element in string_array
    begin
        Console.WriteLine(element)
    end

    foreach explicit_iteration_variable in string_array
    begin
        Console.WriteLine(explicit_iteration_variable)
    end
```

> #### Output
> ```
> hello
> for
> each
> loops
> hello
> for
> each
> loops
> ```

 #### Advanced FOREACH-IN features
You can use the `AS type` syntax to cast the loop variable to a different type. Explicitly defining the iteration variable's type in a FOREACH-IN loop is useful when working with untyped collections, such as [ArrayList](../collections/array_list.md). This allows you to leverage your knowledge of the actual type of items in the collection. 
 
```dbl,ignore,does_not_compile
foreach mydecimalvar in arraylist as @int
begin
    ; statement
end
```

Similarly, if the collection contains elements of type `structfield`, and the loop variable is of type `a`, you could cast the loop variable to the structure's type: `**seeing the declaration for structure_name would be helpful`

```dbl,ignore,does_not_compile
foreach avar in arraylist as @structure_name
begin
   ; statement
end
```

### Less common loops
While you will likely encounter the following loop types in your codebase, there are few, if any, non-historical reasons to write these loops into new code.

`**This and the next section have quasi-subsections with inline bold quasi-headings. Might be better just to make fourth-level headings. Actual headings might be easier to find, but then again, they might not be different enough from third-level headings, which would make the structure difficult to see.`
The **DO FOREVER** (`DO FOREVER statement`) loop endlessly executes a statement until broken through an EXITLOOP statement, a GOTO statement, or error catching.

```dbl
do forever
begin
    ; statement
end
```

The **REPEAT** loop (`REPEAT statement`), like the DO FOREVER loop, continually executes a statement until control is transferred to another statement due to some condition (e.g., EXITLOOP or GOTO). Having two loop types that work the same is a historical artifact rather than a difference based on some sort of tradeoff.

```dbl
repeat
begin
    ; statement
end
```

A **DO-UNTIL** loop (`DO statement UNTIL condition`) runs a specified statement until a provided condition becomes true. It evaluates the condition after each iteration, and if it's false, the statement is executed again.

```dbl
do
begin
    ; statement
end
until condition
```

A **FOR-DO** loop (`FOR variable = value[, ...] DO`) executes a statement for each value in a given list`**Can it be an array or list variable (e.g., FOR someInt = myIntArray DO...)? Or do the values have to be spelled out after FOR?`. The loop concludes when all values in the list have been assigned to the variable and the statement has been executed for all.

`**Why do we include the "..." in the example. Why not something like "for variable = value, value2, value3 do"`

```dbl,ignore,does_not_compile
for variable = value, value2, ... do 
begin
    ; statement
end
```

The **FOR-UNTIL-DO** loop (`FOR variable = initial [STEP incr] UNTIL final DO`) behaves similarly to the FOR-FROM-THRU loop but allows for modifications to the *final* value and *incr* during the loop's execution.

```dbl,ignore,does_not_compile
for var = initial step 1 until final do
begin
    ; statement
end
```

## Unconditional control flow
Unconditional control flow refers to DBL statements that alter the sequential execution of code without evaluating conditions. These instructions, such as`**"such as"? Or is this the full list? maybe error processing (and recursion?) counts in this regard, so this is not a full list and "such as" makes sense.` GOTO, EXIT, EXITLOOP, and NEXTLOOP, jump to a specific point in the code or terminate loops prematurely, regardless of any loop conditions. Because these statements don't have their own conditions, they are almost always paired with an IF.  

The **EXIT** statement (`EXIT[label]`) transfers control to the END statement of the current BEGIN-END block. If there are nested BEGIN-END blocks, you can optionally use a label to specify which block you want to exit. The label corresponds to a label on a BEGIN statement. `**example?`

The **GOTO** statement (`GOTO label` or `GOTO(label[, ...]), selector`) redirects execution control to a specific label. You can specify a single label directly or use a list of labels with a selector. The selector is an expression that selects an element from the list of labels (1 for the first label, 2 for the second, and so on). If the value of the selector is less than 1 or more than the number of labels, execution continues with the statement following the GOTO. You may see the computed GOTO form in your codebase, but it's best to use one of the more structured control flow options such as USING. `**Example of the version with the selector? Or do we not want to supply an example for GOTO since no one should use it? Also, doesn't the second form need to be "GOTO(label, label2[, ...]), selector" to be meaningful?`

The **EXITLOOP** statement is used to break out of a loop prematurely. When EXITLOOP is executed, it terminates the current loop (DO FOREVER, FOR, REPEAT, WHILE, etc.), and control is transferred to the statement immediately after the loop. `**example?`

Use **NEXTLOOP** when you want to terminate the current iteration of a loop, but not all remaining iterations. After executing NEXTLOOP, control goes to the next iteration of the current loop (DO, FOR, REPEAT, WHILE, etc.). `**example?`

As a best practice, limit or eliminate the use of GOTO, as it can make code difficult to read and maintain. Structured control flow with loops, conditionals, and routine calls is preferable. EXIT and EXITLOOP, however, can be very useful for managing control flow, especially when you need to leave a loop or block due to an error condition or when a certain condition is met. NEXTLOOP is also a handy tool when you want to skip the current iteration and continue with the next one.

> ## Quiz
> 1. Consider the following IF construct: `IF condition THEN statement1 ELSE statement2`. What does "statement2" represent?
>    - [ ] The statement to be executed when the condition is true.
>    - [ ] The statement to be executed when the condition is false.
>    - [ ] The condition to be checked after the initial condition is checked.
>    - [ ] The default statement that is always executed.
> 
> 2. In an IF construct, are parentheses around the condition required?
>    - [ ] Yes, the condition must always be enclosed in parentheses.
>    - [ ] Yes, but only when using the ELSE IF clause.
>    - [ ] No, parentheses can improve readability but are entirely optional.
>    - [ ] No, parentheses are not allowed in the IF construct.
> 
> 3. Which statement about THEN in DBL is correct?
>    - [ ] THEN is always required in IF and ELSE IF statements.
>    - [ ] THEN is only required in IF statements.
>    - [ ] THEN is never required in DBL.
>    - [ ] THEN is required if another ELSE or ELSE-IF will follow but it is not allowed on the last one.
> 
> 4. Consider you have a piece of code where you need to execute different blocks of code based on the value of a single variable. Which control flow structures are the most appropriate for this purpose in the DBL programming language?
>    - [ ] IF, ELSE IF, ELSE
>    - [ ] USING, CASE
>    - [ ] FOR, WHILE
>    - [ ] BEGIN, END
> 
> 5. What is the purpose of the ELSE clause in a CASE control flow statement?
>    - [ ] It provides a condition to be checked if no prior conditions have been met.
>    - [ ] It acts as the default case that is always executed.
>    - [ ] It specifies a block of code to be executed if no case labels match the value of the switch expression.
>    - [ ] It causes the program to exit the CASE statement if no match is found.

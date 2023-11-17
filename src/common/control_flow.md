# Control Flow

### IF-THEN-ELSE
The IF statement allows for conditional control flow within your program. The statement checks a certain condition and executes an associated block of code if the condition is true.

The IF construct works in the following way: IF condition statement. Here, the condition must be an expression that evaluates to a boolean value, true or false. If the condition evaluates to true, the program will execute the statement.

Here's a basic example:

```dbl,ignore,does_not_compile
if x > y
begin
    ; this statement will execute if x is greater than y
    Console.WriteLine("x is greater than y")
end
```
The `ELSE` statement is used in conjunction with the `IF` statement to provide an alternative branch of execution when the `IF` condition is not met, i.e., it evaluates to false. The `THEN` keyword is used in this case to make the syntax more readable and clearly define the separate paths of execution. The general structure is: `IF condition THEN statement_1 ELSE statement_2`. You will often see the form `IF(condition) statement` the parenthesis can improve readability but are entirely optional.

```dbl,ignore,does_not_compile
if x > y then
begin
    ; this statement will execute if x is greater than y
    Console.WriteLine("x is greater than y")
end
else
begin
    ; this statement will execute if x is not greater than y
    Console.WriteLine("x is not greater than y")
end
```

Similarly, the `ELSE IF` statement allows for checking multiple conditions. It is used after an `IF` and before an `ELSE` statement. If the initial `IF` condition evaluates to false, then the program checks the `ELSE IF` condition. If the `ELSE IF` condition evaluates to true, its corresponding `statement` is executed. If not, the program proceeds to the `ELSE` statement, if one is present. Here also, the `THEN` keyword is required for each `ELSE IF` until the the last one.

```dbl,ignore,does_not_compile
if x > y then
begin
    ; this statement will execute if x is greater than y
    Console.WriteLine("x is greater than y")
end
else if x = y then
begin
    ; this statement will execute if x is equal to y
    Console.WriteLine("x is equal to y")
end
else
begin
    ; this statement will execute if x is not greater than y and x is not equal to y
    Console.WriteLine("x is less than y")
end
```

`BEGIN-END` is required when your `IF`, `ELSE`, `ELSE IF` needs to have multiple statements in its body. But for simple statements you can omit them, additionally newlines are not required between the expression part and the statement part.

```dbl,ignore,does_not_compile
if x > y then
    Console.WriteLine("x is greater than y")
else if x = y then Console.WriteLine("x is equal to y")
else Console.WriteLine("x is less than y")
```

`THEN` is required if another `ELSE` or `ELSE-IF` will follow but it is not allowed on the last one.

Here's an example to show when `THEN` is required and not allowed:

```dbl,ignore,does_not_compile
record
    fld1, d10, 99999
proc
    if(fld1 > 100) then
        Console.WriteLine(fld1)
    else if(fld1 < 9000) ;;missing 'then' makes the first error
        Console.WriteLine("fld1 is on its way up")
    else
        Console.WriteLine("fld1 was over 9000!")


    if(fld1 > 100) then
        Console.WriteLine(fld1)
    else if(fld1 < 9000) then ;;this extra 'then' makes the second error
        Console.WriteLine("fld1 is on its way up")
```

> #### Compiler Output
> ```
> %DBL-E-INVSTMT, Invalid statement at or near {END OF LINE} : else
> %DBL-E-NOSPECL, Else part expected : Console.WriteLine("fld1 is on its way up")
> ```

## Multi-way Control Flow
Complex control flow statements can be an improvement over long chains of if-then-else. It operates in ways that are similar to the `switch` statement in the C family of languages. There are three main variants:

- `CASE`\
- `USING`\
- `USING-RANGE`

All of these structures have a similar purpose - evaluate an expression and then executes one of several possible code blocks (or none at all) depending on the value of that expression.. However, there are important differences in their syntax, how they evaluate conditions, and their performance characteristics.

### CASE

`CASE` is the most basic of the control flow statements. It allows selection from a set of unlabeled or labeled statements, based on the value of the control expression. 

- **Unlabeled CASE**: The control expression is non-implied numeric and interpreted as an ordinal number 1-n, where n is the number of statements in the set. For example, a value of 6 selects the sixth statement.

- **Labeled CASE**: All statements are identified with labels which must be literals and of the same type as the control expression. Labels can be a single literal or a range (using a hyphen). The control expression is matched with these labels to select the corresponding statement.

`ELSE` is used to specify a block of code to be executed if no case labels match the value of the switch expression. It's akin to an "else" clause in an if-then-else conditional block. If the else case is not provided and no match is found, the case statement will simply do nothing.

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
    1:  Console.WriteLine("You entered 1")
    2:  Console.WriteLine("You entered 2")
    3:  Console.WriteLine("You entered 3")
    endcase
    else
        Console.WriteLine("You entered something else")

    case num of
    begincase
    1-5:  Console.WriteLine("You entered something between 1 and 5")
    6-9:  Console.WriteLine("You entered something between 6 and 9")
    10-15:  Console.WriteLine("You entered something between 10 and 15")
    endcase

    ;;Labeled case - alpha
    case color of
    begincase
    "red":  Console.WriteLine("You entered red")
    "blue":  Console.WriteLine("You entered blue")
    "green":  Console.WriteLine("You entered green")
    endcase
    else
        Console.WriteLine("You entered something else")

    ;;Unlabeled case
    case num of
    begincase
        Console.WriteLine("You entered 1")
        Console.WriteLine("You entered 2")
        Console.WriteLine("You entered 3")
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

The `USING` statement selects a statement for execution from a set based on the evaluation of a control expression against one or more `match_term` conditions. Each `match_term` is evaluated from top to bottom and left to right. Once a match is found, no other conditions are evaluated. The `USING` statement is more efficient than `CASE` when using an `i` or `d` control expression and all `match_terms` are compile-time literals.

```dbl
record
    code, a2
proc
    code = "AA"

    using code select
    ('0' thru '9'),
    begin
        ;;begin end can be used here
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

The `USING-RANGE` statement is similar to `USING` but adds a range for the control expression. This allows you to define a range of values within which the control expression is evaluated. The `USING-RANGE` statement builds a dispatch table at compile time and is typically faster than the `USING` statement.

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
> What does this program output if month = 5 instead of 3?
> 
> What does this program output if month = 5555 instead of 3?

---

While each of these control flow mechanisms has its uses, in most modern coding scenarios the USING statement tends to be the go-to choice due to its flexibility and powerful matching conditions. Although the `CASE` statement is straightforward and simple to use, it tends to be slower. As it was developed earlier, it is frequently encountered in legacy code. In the other direction, the USING-RANGE statement provides a slight efficiency boost when a control expression is evaluated within a predefined range.

It's important to consider several factors when deciding which structure to use in your specific use case. These include the complexity of your matching conditions, the need for a defined range for the control expression, and the importance of execution speed. However, given its power and versatility, the USING statement will often be a sensible default choice for new code.

## Loops

Loops are foundational constructs used to automate and repeat tasks a certain number of times, or until a specific condition is met. The `for` loop is typically utilized when the exact number of iterations is known beforehand, whereas `while` and `do-while` loops are more suitable when the iterations depend on certain conditions. `foreach` is used to improve readability when operating over a collection or dynamic array. However, there are other methods to handle repetition in code. For instance, recursion, where a function calls itself, is an alternative to loops that can be more intuitive for certain tasks, such as traversing tree-like data structures. Recursion may lead to higher memory usage and potential stack overflow errors if not implemented correctly. Furthermore, earlier, less-structured looping mechanisms like the `goto` statement were used to jump to different points in the code. While goto provides a great degree of freedom, it often leads to "spaghetti code," which is hard to read and maintain due to its lack of structure.

The `FOR variable FROM initial THRU final [BY incr]` loop executes the statement as long as the variable's value is within the specified range. The variable's value is incremented by an optional `incr` value (default is 1) after each iteration.

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

The `WHILE condition [DO] statement` loop continues as long as the specified condition is true. Once the condition is no longer true, the loop will be exited.


The `FOREACH loop_var IN collection [AS type]` loop iterates over each element in a collection, setting `loop_var` to each element in turn and executing the statement. Note that the loop variable must be the same type as the elements in the collection, or an "Invalid Cast" exception will occur. We'll cover collections and arrays in more detail in the [Collections](../collections/collections.md) chapter.

You can use `data` to declare the iteration variable directly inside the foreach loop, If the compiler cant infer the type of the variable you will need to specify it using the `AS type` syntax. Here's an example with an without an inline variable declaration.

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

> #### Advanced features
> You can use the `AS type` syntax to cast the loop variable to a different type. Explicitly defining the iteration variable's type in a FOREACH loop is useful when working with untyped collections, such as [ArrayList](../collections/array_list.md). This allows you to leverage your knowledge of the actual type of the items in the collection:
> 
> ```dbl,ignore,does_not_compile
> foreach mydecimalvar in arraylist as @int
> begin
>     ; statement
> end
> ```
>
> Similarly, if the collection contains elements of type `structfield` and the loop variable is of type `a`, you could cast the loop variable to the structure's type:
>
> ```dbl,ignore,does_not_compile
> foreach avar in arraylist as @structure_name
> begin
>    ; statement
> end
> ```

### Less common loops
While you will likely encounter these loop types in your codebase there are few if any non historical reasons to write them into new code.

The `DO FOREVER` loop endlessly executes a provided statement until broken either through an `EXITLOOP` or `GOTO` statement, or by an error.

```dbl
do forever
begin
    ; statement
end
```

The `REPEAT` loop, much like the `DO FOREVER` loop, continually executes a provided statement until control is transferred to another statement due to some condition. Having 2 loops that work the same is a historical artifact rather than some sort of trade off between the two.

```dbl
repeat
begin
    ; statement
end
```

The `DO UNTIL` loop runs a specified statement until a provided condition becomes true. It evaluates the condition after each iteration and, if false, the statement executes again.

```dbl
do
begin
    ; statement
end
until condition
```

The `FOR variable = value, ... DO` loop executes the statement for each value in a given list. The loop concludes when all the values in the list have been assigned to the variable and the statement has been executed accordingly.

```dbl,ignore,does_not_compile
for variable = value, value2, ... do
begin
    ; statement
end
```

The `FOR variable = initial [STEP incr] UNTIL final DO` loop behaves similarly to the `FOR FROM THRU` loop but allows modifications to the final value and increment during the loop's execution.

```dbl,ignore,does_not_compile
for var = initial step 1 until final do
begin
    ; statement
end
```


### Unconditional Control Flow
Unconditional control flow in refers to statements that alter the sequential execution of code without evaluating any conditions. These instructions, such as `GOTO`, `EXIT`, `EXITLOOP`, and `NEXTLOOP`, allow the programmer to jump to a specific point in the code or terminate loops prematurely, regardless of any attached conditions. Because these statements don't have their own conditions, they are almost always paired with some kind of `IF`. 

1.  `EXIT [label]`: Use the `EXIT` statement to transfer control to the `END` statement of the current `BEGIN-END` block. If there are nested `BEGIN-END` blocks, you can use an optional label with `EXIT` to specify which block you want to exit. The label corresponds to a label on a `BEGIN` statement.

2.  `GOTO label` or `GOTO(label[, ...]), selector`: `GOTO` is used to redirect execution control to a specific label in your program. You can specify a single label directly or use a list of labels with a selector. The selector is an expression that selects an element from the list of labels (1 for the first label, 2 for the second, and so on). If the value of the selector is less than 1 or more than the number of labels, execution continues with the statement following the `GOTO`. You may see the computed goto form in your codebase but should strongly prefer one of the more structured control flow options such as `USING`

3.  `EXITLOOP`: This statement is used to break out of a loop prematurely. When `EXITLOOP` is executed, it terminates the current loop (`DO FOREVER`, `FOR`, `REPEAT`, `WHILE`, etc.) and control is transferred to the statement immediately after the loop.

4.  `NEXTLOOP`: Use `NEXTLOOP` when you want to terminate the current iteration of a loop, but not the entire loop. After executing `NEXTLOOP`, control goes to the next iteration of the current loop (`DO`, `FOR`, `REPEAT`, `WHILE`, etc.).

As a best practice, try to limit the use of `GOTO`, as it can make code difficult to read and maintain. Structured control flow with loops, conditionals, and routine calls is generally preferred. However, `EXIT` and `EXITLOOP` can be very useful for managing control flow, especially when you need to leave a loop or block due to an error condition or when a certain condition is met. `NEXTLOOP` is also a handy tool when you want to skip the current iteration and continue with the next one.


> #### Quiz
> 1. Consider the following IF construct: `IF condition THEN statement1 ELSE statement2`. What does `statement2` represent?
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
> 3. Which statement about `THEN` in DBL is correct?
>    - [ ] `THEN` is always required in IF and ELSE IF statements.
>    - [ ] `THEN` is only required in IF statements.
>    - [ ] `THEN` is never required in DBL.
>    - [ ] `THEN` is required if another `ELSE` or `ELSE-IF` will follow but it is not allowed on the last one.
> 
> 4. Consider you have a piece of code where you need to execute different blocks of code based on the value of a single variable. Which control flow structures are the most appropriate for this purpose in the DBL programming language?
>    - [ ] `IF`, `ELSE IF`, `ELSE`
>    - [ ] `USING`, `CASE`
>    - [ ] `FOR`, `WHILE`
>    - [ ] `BEGIN`, `END`
> 
> 5. What is the purpose of the `ELSE` clause in a `CASE` control flow statement?
>    - [ ] It provides a condition to be checked if no prior conditions have been met.
>    - [ ] It acts as the default case that is always executed.
>    - [ ] It specifies a block of code to be executed if no case labels match the value of the switch expression.
>    - [ ] It causes the program to exit the `CASE` statement if no match is found.



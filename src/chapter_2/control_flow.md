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

### USING

The `USING` statement selects a statement for execution from a set based on the evaluation of a control expression against one or more `match_term` conditions. Each `match_term` is evaluated from top to bottom and left to right. Once a match is found, no other conditions are evaluated. The `USING` statement provides more powerful structures than `CASE` and is efficient when using an `i` or `d` control expression and all `match_terms` are compile-time literals.

### USING-RANGE

The `USING-RANGE` statement is similar to `USING` but adds a range for the control expression. This allows you to define a range of values within which the control expression is evaluated. If the control expression falls outside the defined range, the compiler generates warnings. The `USING-RANGE` statement builds a dispatch table at compile time and is typically faster than the `USING` statement.

---

Each of these structures is powerful in their own way. The `CASE` statement is straightforward and simple to use, the `USING` statement offers more powerful matching conditions, and the `USING-RANGE` statement provides an efficiency boost when a control expression is evaluated within a predefined range.

The choice among these options will depend on your specific use case. Factors to consider include the complexity of your matching conditions, the need for a defined range for the control expression, and the importance of execution speed.

## Loops

The `FOR variable FROM initial THRU final [BY incr]` loop executes the statement as long as the variable's value is within the specified range. The variable's value is incremented by an optional `incr` value (default is 1) after each iteration.

```dbl,ignore,does_not_compile
for var from initial thru final by 1
begin
    ; statement
end
```

The `WHILE condition [DO] statement` loop continues as long as the specified condition is true. Once the condition is no longer true, the loop will be exited.


The `FOREACH loop_var IN collection [AS type]` loop iterates over each element in a collection, setting `loop_var` to each element in turn and executing the statement. Note that the loop variable must be the same type as the elements in the collection, or an "Invalid Cast" exception will occur.

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
> You can use the `AS type` syntax to cast the loop variable to a different type. Explicitly defining the iteration variable's type in a FOREACH loop is useful when working with untyped collections, such as ArrayList. This allows you to leverage your knowledge of the actual type of the items in the collection:
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

```dbl
for var = initial step 1 until final do
begin
    ; statement
end
```

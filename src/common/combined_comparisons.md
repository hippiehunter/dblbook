# Combining Comparisons
In the previous sections, we explored the fundamentals of relational comparisons, such as == and >, and delved into the essentials of control flow. Building on that foundation, this section, "Combining Comparisons" compares what life would be like with and without Boolean operators. Seeing the fully expanded long form explanation will hopefully make it easier for you to reason about complex logical expressions and also help you break down any overly complex expressions you encounter in your codebase. 

## Boolean operators

In addition to comparison operators, we have Boolean operators. They compare the truth value of operands and return `true` or `false` just like comparison operators. Here they are:

1.  **OR (`||` or `.OR.`)**: Returns `true` if either operand is true.
2.  **Exclusive OR (`.XOR.`)**: Returns `true` if exactly one operand is true.
3.  **AND (`&&` or `.AND.`)**: Returns `true` if both operands are true.
4.  **NOT (`!` or `.NOT.`)**: Returns `true` if the operand is false.
`**Consider formatting these as bullets rather than a numbered list, which implies steps`

> Like most programming languages, DBL evaluates Boolean operators from left to right. If the result can be determined by the left operand, DBL won't process the right operand. This is known as short-circuit evaluation.

To better understand these operators, let's take a look at some basic examples contrasted with their equivalent code without the operators.

### Using the `&&` operator

**With &&:**

```dbl,ignore,does_not_compile
data isAdult = true
data hasTicket = true

if (isAdult && hasTicket)
    Console.WriteLine("Access granted.")
```

**Without &&:**

```dbl,ignore,does_not_compile
data isAdult = true
data hasTicket = true

if (isAdult)
begin
    if (hasTicket)
        Console.WriteLine("Access granted.")
end
```

### Explanation:

- **With `&&`**: The IF statement checks both conditions (`isAdult` and `hasTicket`) in a single line. If both are true, the message "Access granted." is printed.
- **Without `&&`**: We use nested IF statements. The outer IF checks `isAdult`, and the inner IF checks `hasTicket`. The same result is achieved, but with more verbose code.

### Using the `||` operator

**With ||:**

```dbl,ignore,does_not_compile
data isRainy = true
data isSnowy = false

if (isRainy || isSnowy)
    Console.WriteLine("Take an umbrella.")
```

**Without ||:**

```dbl,ignore,does_not_compile
data isRainy = true
data isSnowy = false

if (isRainy) then
    Console.WriteLine("Take an umbrella.")
else if (isSnowy)
    Console.WriteLine("Take an umbrella.")
```

### Explanation:

- **With `||`**: The IF statement checks if either `isRainy` or `isSnowy` is true. If either condition is met, the message "Take an umbrella." is printed.
- **Without `||`**: We use separate IF and ELSE IF statements to check each condition independently. The message is printed if either condition is true, but the code is less concise.

Let's compare the use of the `!` (logical NOT) and `^` (logical XOR, exclusive OR) operators in C# with alternative implementations using IF ELSE statements.

### Using the `!` operator

**With !:**

```dbl,ignore,does_not_compile
data isClosed = true

if (!isClosed)
    Console.WriteLine("The door is open.")
```

**Without !:**

```dbl,ignore,does_not_compile
data isClosed = true

if (isClosed) then
begin
    ;; Do nothing if the door is closed
end
else
    Console.WriteLine("The door is open.")
```

### Explanation:

- **With `!`**: The `!` operator inverts the Boolean value of `isClosed`. The IF statement checks if `isClosed` is not true (i.e., false), and if so, prints the message.
- **Without `!`**: We use an IF ELSE statement. The IF part is essentially a placeholder, and the ELSE part handles the case when `isClosed` is false, printing the message.

### Using the `.XOR.` operator

**With .XOR.:**

```dbl,ignore,does_not_compile
data switch1 = true
data switch2 = false

if (switch1 .XOR. switch2)
    Console.WriteLine("The light is on.")

```

**Without .XOR.:**

```dbl,ignore,does_not_compile
data switch1 = true
data switch2 = false

if (switch1) then
begin
    if (switch2) then
    begin
    end
    else
        Console.WriteLine("The light is on.")
end
else
begin
    if (switch2)
        Console.WriteLine("The light is on.")
end
```

### Explanation:

- **With `.XOR.`**: The `.XOR.` operator performs an exclusive OR operation. It returns `true` if exactly one of `switch1` or `switch2` is true. The message is printed when this condition is met.
- **Without `.XOR.`**: We use nested IF ELSE statements. The first IF checks `switch1`, and its nested IF checks `switch2`. The ELSE part handles the case when `switch1` is false. This approach is more verbose and less straightforward compared to using the `.XOR.` operator.



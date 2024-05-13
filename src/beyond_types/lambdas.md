# Lambdas
Lambdas, a concept that might initially seem daunting, are a powerful feature in modern programming languages like DBL. They allow you to write more concise and flexible code. To understand lambdas, it's crucial to grasp two key concepts: "captured variables" and "code as data."

#### What are lambdas?

Lambdas are essentially anonymous functions—functions without a name—that you can define inline (within another method) and use for short and specific tasks. They are particularly handy when you want to pass a block of code as an argument to a method or when you need a simple function for a short duration.

#### Captured variables

When you define a lambda inside a method, it can access variables from its enclosing scope. These are known as "captured variables." This ability is powerful as it allows the lambda to interact with the context in which it was defined.

For example, consider a lambda defined inside a routine where it captures a local variable:

```dbl
function MyFunction, void
    record
        localVar, int
proc
    data localVar2, int, 10
    localVar = 50
    lambda AddToVar(x)
    begin
        mreturn localVar + x + localVar2
    end

    ;; Here, the lambda 'AddToVar' captures 'localVar'
    ;; and 'localVar2' from the enclosing scope.
    data invocable, @Func<int, int>, AddToVar
    Console.WriteLine(%string(invocable(5))) ;; prints 65
end
```

The lambda `AddToVar` can access and use the variable `localVar` which is defined outside of it, in `MyMethod`.

### Understanding captured variables in the context of loops

Captured variables in the context of loops in DBL (or any programming language that supports lambdas and closures) can be a source of confusion, especially when it comes to understanding how they behave inside the loop. Let's break this down for clarity.

#### What is a captured variable?

A captured variable is a variable that is declared in an outer scope (like a method) and is used or "captured" inside a lambda expression or anonymous function. This lambda is then able to use or modify the variable even if it executes in a different scope than where the variable was originally declared.

#### Capturing variables in loops

The most common issue arises when lambdas are stored (e.g., added to a list) during each iteration and executed later. Developers might expect each lambda to remember the value of `i` at the time of its creation, but instead, they all reflect the final loop value.

To demonstrate this, consider the following example:

```dbl
subroutine ExampleRoutine
    record
        i, int
        funcs, @List<Func<string>>
proc
    funcs = new List<Func<string>>()
    for i from 1 thru 5
    begin
        data currentIteration = i  ;; Local copy
        lambda MyLambda()
        begin
            mreturn "Value: " + %string(currentIteration)
        end
        ;; Store MyLambda
        funcs.Add(MyLambda)
    end

    ;; Invoke all lambdas
    foreach data func in funcs
        Console.WriteLine(func())

    xreturn
end
```

When you run this code, you might expect the following output:

```console
Value: 1
Value: 2
Value: 3
Value: 4
Value: 5
```

But instead you get:

```console
Value: 5
Value: 5
Value: 5
Value: 5
Value: 5
```

To solve this problem, you can use a lambda factory method to create a new lambda for each iteration. This ensures that each lambda has its own copy of the captured variable.

```dbl
namespace Example
    class ExampleClass
        public static method CreateLambdaWithValue, @Func<string>
            val, int
        proc
            lambda MyLambda()
            begin
                mreturn "Value: " + %string(val)
            end
            mreturn MyLambda
        endmethod
    endclass
endnamespace

subroutine ExampleRoutine
    record
        i, int
        funcs, @List<Func<string>>
proc
    funcs = new List<Func<string>>()
    for i from 1 thru 5
    begin
        ;; Store MyLambda
        funcs.Add(ExampleClass.CreateLambdaWithValue(i))
    end

    ;; Invoke all lambdas
    foreach data func in funcs
        Console.WriteLine(func())

    xreturn
end
```

Now when you run it, you get the expected output:

```console
Value: 1
Value: 2
Value: 3
Value: 4
Value: 5
```

This can also be considered partial application, which is a technique that allows you to create a new function by pre-filling some of the arguments of an existing function. Currying is a functional programming technique used to transform a function with multiple arguments into a sequence of functions, each with a single argument. In essence, currying takes a function that accepts multiple parameters and breaks it down into a series of unary functions (functions with only one parameter). This is achieved by returning a new function for each argument, which captures the argument passed to it and returns a new function expecting the next argument. This process continues until all arguments are received, at which point the original function is executed with all of the captured arguments. Currying is particularly useful for creating a higher-order function that is both reusable and easily configurable. It enables partial function application, where a function that takes multiple arguments can be transformed into a chain of functions, each taking a part of the arguments, thereby creating new functions with fewer parameters. This approach enhances the flexibility and modularity of the code, allowing functions to be more dynamically composed and adapted to various contexts in a clean and intuitive manner.

#### Code as data

Lambdas embody the concept of treating code as data. This means you can assign a block of code to a variable, pass it as a parameter, or even return it from a function, just like you would with data.

For example:

```dbl
main
proc
    lambda MyLambda(x, y)
    begin
        mreturn x * y
    end

    ;; Assigning lambda to a variable
    data myFunc, @Func<int, int, int>, MyLambda

    ;; Passing lambda as an argument
    SomeMethod(MyLambda)
endmain
```

#### Practical use of lambdas

Lambdas are widely used for

- **Event handling**: Assigning a lambda to an event makes the code more readable and concise.
- **Working with collections**: Operations like sorting, filtering, and transforming collections are more straightforward with lambdas.
- **Asynchronous programming**: Lambdas make it easier to write asynchronous code, especially with the `ASYNC` keyword.

#### Why use lambdas?

1. **Conciseness**: Lambdas reduce the boilerplate code required for defining a full-fledged method.
2. **Clarity**: Lambdas often make the code easier to read, as the functionality is defined right where it's used.
3. **Flexibility**: Lambdas allow for dynamic programming patterns, adapting behavior at runtime.

Lambdas might seem complex at first, especially when dealing with captured variables and the notion of code as data. However, with practice, they will become an intuitive and powerful tool in your DBL programming toolkit. Remember, lambdas are all about writing less to do more, elegantly and efficiently.

### A deeper look at currying and partial application
Currying and partial application are powerful concepts in functional programming, and their utility extends to multi-paradigm languages like DBL, which support functional programming constructs. Here's a more compelling explanation of why a developer might want to use currying or partial application:

### Code reusability and abstraction

1. **Modular function design**: Currying allows the breakdown of a complex function with multiple arguments into a series of simpler, unary functions (functions with a single argument). This modular approach makes functions more reusable, as each unary function can be used independently across different parts of the application.

2. **Function specialization**: Partial application is a technique to fix a few arguments of a function and generate a new function. This is particularly useful for creating specialized functions from a general-purpose function without rewriting it. For instance, if you have a generic `add` function, you can create a new function like `addFive` that always adds five to any given number, reusing the `add` function logic.

### Enhanced flexibility and function configuration

1. **Dynamic function configuration**: Currying and partial application allow functions to be dynamically configured with specific arguments ahead of time. This is particularly useful in scenarios where certain parameters of a function are known in advance and remain constant throughout the application.

2. **Delayed execution and function pipelines**: By transforming functions into a chain of unary functions, currying naturally supports delayed execution. Functions can be set up early in the program and executed later when required, enabling the creation of sophisticated function pipelines and data flows.

### Improved readability and maintenance

1. **Cleaner code**: By using curried functions, the code can often be made more concise and readable. It allows for the creation of higher-order functions that encapsulate specific behaviors with descriptive naming, making the codebase easier to understand and maintain.

2. **Better abstraction**: Currying and partial application promote a higher level of abstraction in function definitions. They help in abstracting away the repetitive parts of the code, leading to a DRY (don’t repeat yourself) codebase.

### Practical use cases

1. **Event handling and callbacks**: In event-driven programming, currying can be used to create event handlers or callbacks that need specific data to be executed but don’t get that data until the event occurs.

2. **Dependency injection**: Partial application can be used for a form of dependency injection, where a function requiring several inputs gets some of its inputs (dependencies) pre-filled.

3. **Creating configurable APIs**: APIs that require flexible configuration can benefit from currying and partial application, allowing users to customize and configure behaviors with partial sets of arguments.


### Understanding the flow of execution with lambdas
In imperative programming, code execution is generally linear and procedural. You write code in the order you expect it to run. However, in functional programming with lambdas, execution can be more abstract and deferred. Lambdas are like packaged pieces of code that you define now but execute later. They allow you to pass around behavior (code) as data.

#### Execution flow with lambdas

1. **Defining a lambda**: You define a lambda expression, but at this point, it's just created and stored, not executed.

2. **Passing a lambda around**: The lambda can be passed as an argument, stored in a variable, etc. During this phase, the lambda is still not executed.

3. **Triggering lambda execution**: The lambda is eventually executed at a point where its behavior is needed. This could be in a different part of the code, often in response to an event or when processing a collection of data.

Consider a simple scenario where a lambda is passed to a function and then executed:

```svgbob
Define lambda
      |
      v
+-----------------+
| Lambda creation | -----> Stored in memory, not executed
+-----------------+
      |
      | (Lambda is passed around, e.g., as an argument)
      v
+-----------------+
|  Some function  | -----> Receives the lambda
+-----------------+
      |
      | (Function decides to execute the lambda)
      v
+-----------------+
| Execute lambda  | -----> Lambda code runs here
+-----------------+
```

#### Practical Example

Consider an example with a list of numbers and a lambda for filtering:

```svgbob
Define lambda: x => x > 10
    |
    | (Lambda is created but not executed)
    v
Pass lambda to a filter function
    |
    | (The filter function holds the lambda)
    v
Filter function runs and applies the lambda to each list element
    |
    | (Lambda executes for each element)
    v
Resulting filtered list is obtained
```

#### Explanation for developers new to functional style

- **Lambdas are blueprints**: Think of a lambda as a blueprint or a plan. When you define a lambda, you're drafting a plan on how to do something, but you're not doing it yet.

- **Execution is deferred**: Lambdas don't do anything by themselves when they're defined. They spring into action only when called upon, which can be at a completely different time and place in your code.

- **Passing behavior**: One of the strengths of lambdas is the ability to pass behavior around your application, just like you pass data. It offers a high level of abstraction and modularity.

- **Trigger point**: The actual execution of a lambda usually happens inside a function or a method that accepts lambda as an argument. This function decides when to run the lambda.

Understanding this flow helps in visualizing how lambdas fit into the bigger picture of your application's execution, making them less mysterious and more of a powerful tool in your programming toolkit.

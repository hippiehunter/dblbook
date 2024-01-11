# Testable Code

We sometimes take for granted the idea that code should be testable but what does that really mean? We're going to dig into a few ways that we can incrementally improve the testability of our codebases. This section still matters to you if you're writing brand new code! You can use these techniques to make your code more testable from the start.

## Parameterize all the things!
Passing data through parameters, rather than using globals or data in files, is our starting point for testable code. It's not uncommon (pardon the pun) to see code that uses commons/globals to control the operation of subroutines/functions that are called. This is a very common pattern in legacy codebases and is a major source of pain when trying to test the code. Lets take a look at a simple example to show you what i mean:

```dbl
main
record
    global common
        fld1, d10
    endcommon
    record
        result, i4
proc
    fld1 = 5
    xcall increment()
    Console.WriteLine(result)
endmain

subroutine increment
    common
        fld1, d10
    endcommon
proc
    fld1 += 1
    xreturn
endsubroutine

```

Why didn't everyone just parameterize everything from the start? Well, there are a few reasons but the big ones are solved now. Before the DBL compiler had support for strong prototyping you had no way to reliably detect that the parameter list for a routine had changed. This meant that it was potentially a massive effort to grep through your entire codebase to find all the places that called a routine and potentially update the parameter list. This operation was potentially recursive as you might need to update the parameter list of the routines that called the routine you were updating. Given the pressure to ship working software this was often avoided by just putting tons of stuff into global/common data. Today, not only can the compiler tell you if you've missed a parameter but navigating the codebase to find and updated the calling routines is trivial with Visual Studio.

Lets take a look at a few of the benefits of parameterizing your code:

1. **Isolation of Functions/Methods**: Parameters allow functions or methods to operate in isolation. They don't rely on external state (like global variables) or external resources (like files), making them predictable and consistent. This isolation simplifies the testing process because you only need to consider the inputs and outputs of the function, not the state of the entire system.

2. **Control of Test Conditions**: When you use parameters, you can easily control the input for testing purposes. This enables you to create a wide range of test cases, including edge cases, without needing to manipulate global state or file contents, which can be cumbersome and error-prone.

3. **Reduced Side Effects**: Global variables and file operations often lead to side effects, where a change in one part of the system unexpectedly affects another part. By using parameters, you minimize these side effects, making it easier to understand and test each part of the code in isolation.

4. **Easier Mocking and Stubbing**: In unit testing, it's common to use mocks or stubs to simulate parts of the system. Parameters make it easier to inject these mocks or stubs, as you're simply passing different values or objects through the parameters. This is more complex with global variables or file-based inputs, as it might require changing the global state or file contents for each test.

5. **Concurrency and Parallel Testing**: When tests rely on parameters rather than shared global state or files, they can be run in parallel on .NET without the risk of interfering with each other. This is crucial for efficient testing, especially in large codebases or when running tests in a continuous integration environment.

6. **Documentation and Readability**: Functions that explicitly declare their inputs through parameters are generally more readable and self-documenting. This clarity is beneficial for testing, as it makes it easier to understand what a function does and what it needs to be tested with.

7. **Refactoring and Maintenance**: When your code relies on parameters rather than globals or files, it's often easier to refactor. You can change the internals of a function without worrying about how it will impact the rest of the system, which is particularly important when maintaining and updating older code.

### Parameterizing Existing Code
Hopefully you're convinced that parameters are preferable to globals but you have a massive codebase and you're not sure where to start. The good news is that you can start small and work your way up. Lets talk about using adapters and bridges to incrementally improve the testability of your codebase.

**Before**
The caller communicates with the original function through a combination of zero or more parameters plus global state in the form of a GDS or COMMON.

```svgbob
 .--------------.
 | Global State | <---.
 '--------------'     |
  ^                   |
  |                   |
  v                   v
.--------.   .-------------------. 
| Caller |-> | Original Function |
'--------'   '-------------------'
```

**Bridge Functions**
In a bridge function, the main idea is to connect global state to a more modular, parameterized function. The bridge function handles the passing of global state as parameters.

```svgbob
.--------------.
| Global State | <-.
'--------------'   |
                   v
.--------.   .-----------------.   .------------------------. 
| Caller |-> | Bridge Function |─> | Parameterized Function |
'--------'   '-----------------'   '------------------------'

```

**Adapter Functions**
An adapter function is used to adapt the interface of one function to another. It takes parameters from the caller, modifies the global state and then calls your original parameterless function.

```svgbob
                .--------------.
                | Global State | <---.
                '--------------'     |
                 ^                   |
                 |                   |
                 v                   v
.--------.   .------------------.   .-------------------. 
| Caller |-> | Adapter Function |─> | Original Function |
'--------'   '------------------'   '-------------------'
```

Both adapter functions and bridge functions fall under the category of wrappers. They wrap around the original function, providing a controlled interface to it. The difference is that a bridge function is a wrapper that takes global state as parameters, whereas an adapter function is a wrapper that takes parameters from the caller and modifies global state.

### An Example
We're going to start with a couple of small functions that rely on global state and then we'll show both approaches to parameterizing them.

```dbl
subroutine my_interesting_routine
    out result, n
    common
        fld1, d10
        fld2, d10
proc
    xcall another_routine()
    result = fld1 + fld2
    xreturn
endsubroutine

subroutine another_routine
    common
        fld1, d10
        fld2, d10
    record
        tmp, i4
proc
    tmp = fld1 + fld2
    if((tmp .band. 1) == 1)
        fld1 = fld1 + 1

    xreturn
endsubroutine

main
record
    global common
        fld1, d10
        fld2, d10
    endcommon
    record
        result, i4
proc
    fld1 = 5
    fld2 = 9000
    xcall my_interesting_routine(result)
    Console.WriteLine(result)
endmain
```

This is obviously a silly example but it will demonstrate many of the problems we have to solve for when refactoring legacy code. Lets start by creating a parameterized version of `my_interesting_routine`:

```dbl,ignore,does_not_compile
subroutine my_interesting_routine_p
    in fld1, d10
    in fld2, d10
    out result, n
proc
    xcall another_routine()
    result = fld1 + fld2
    xreturn
endsubroutine
```

This is a good start but we still have a problem. `another_routine` is still using global state. Lets try making a parameterized version of `another_routine`:

```dbl,ignore,does_not_compile
subroutine another_routine_p
    inout fld1, d10
    in fld2, d10
    record
        tmp, i4
proc
    tmp = fld1 + fld2
    if((tmp .band. 1) == 1)
        fld1 = fld1 + 1

    xreturn
endsubroutine
```

If you try to call `another_routine_p` from `my_interesting_routine_p` you'll get a compiler error. The compiler is telling you that you can't pass an `in` parameter to an `inout` parameter. This is a good thing! The compiler is telling you that you're trying to change the value of a parameter that you've told it is read only. This is a great example of how the compiler can help you write better code. 

```dbl
subroutine my_interesting_routine_p
    inout fld1, d10
    in fld2, d10
    out result, n
proc
    xcall another_routine_p(fld1, fld2)
    result = fld1 + fld2
    xreturn
endsubroutine

subroutine another_routine_p
    inout fld1, d10
    in fld2, d10
    record
        tmp, i4
proc
    tmp = fld1 + fld2
    if((tmp .band. 1) == 1)
        fld1 = fld1 + 1

    xreturn
endsubroutine

```

Ok, now the parameterized version of `my_interesting_routine` can be used as a functional equivalent to the original. Lets wrap it with our bridge function:

```dbl
subroutine my_interesting_routine
    out result, n
    common
        fld1, d10
        fld2, d10
proc
    xcall my_interesting_routine_p(fld1, fld2, result)
endsubroutine
```

Not so hard but notice that this method of wrapping our global state away is somewhat viral. We had to change the original function to call the parameterized version and then we had to change the parameterized version to call the parameterized version of the other function. If we had tried to mix a parameterized version with the original or with a wrapper, it would have been very easy to subtly change the behavior of the code, introducing hard to track down bugs. If you're going to use bridge functions as a strategy, this is a good example of why we want to start small and work our way up. 

Now that you've seen how to use a bridge function to wrap a function that uses global state, lets look at how we can use an adapter function to do the same job:

```dbl
subroutine my_interesting_routine_p
    inout param1, d10
    in param2, d10
    out result, n
    common
        fld1, d10
        fld2, d10
proc
    fld1 = param1
    fld2 = param2
    xcall my_interesting_routine()
    param1 = fld1
    xreturn
endsubroutine
```

This version is functionally equivalent to the original but now it takes parameters and uses them to pass in and out the global state. If you need to start big and a little dirtier, using adapter functions is a good way to go. You can wrap the entire function and then start breaking it down into smaller pieces. This is a good strategy if you're trying to get a large codebase under test quickly. You will of course need to watch out for any unknown side effects for routines called by the function you're wrapping. In our example we had already learned that fld1 was being modified by another routine so we knew we needed to pass it in and out of our wrapper function. If we hadn't known that, we would have had to discover it by testing the function and then refactoring it to use parameters.
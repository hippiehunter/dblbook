# Preprocessor
The preprocessor handles several compiler directives that begin with a period (.) and processes them before the actual compilation occurs. Let's take a look at the tasks that can be performed using the preprocessor:

## Text replacement with .DEFINE
Think of .DEFINE as creating a search-and-replace rule for your code. It comes in two forms:

### Simple replacement:
```dbl
.DEFINE TTCHN, 1
```
This tells the compiler "whenever you see TTCHN, replace it with 1." It's similar to creating a constant but happens during preprocessing. A practical use would be

```dbl
.DEFINE MAX_USERS, 100
.DEFINE DATABASE_PATH, "data/users.dat"
```

### Parameterized macros:
```dbl
.DEFINE SUBTOTAL(desc, amount) writes(pchan, "Subtotal for ''desc': "+%string(amount))
```
This creates a more sophisticated replacement pattern that accepts parameters. It's like creating a template that can be filled in with different values. When you write

```dbl
SUBTOTAL(Apples, apples_total)
```

the preprocessor expands it to
```dbl
writes(pchan, "Subtotal for Apples: "+%string(apples_total))
```

> #### Limitations
> Preprocessor expansion in DBL is limited to a single statement (i.e., a single line). This means that the following attempt to expand three statements is not possible with a single macro:
>
> ```dbl
> .DEFINE SUBTOTAL(desc, amount) open(pchan, O, "TT:")
> & writes(pchan, "Subtotal for ''desc': "+%string(amount)) 
> & close(pchan)
> ```

## Conditional compilation
DBL offers three main ways to conditionally include or exclude code during compilation:

### .IF for expression-based decisions:
```dbl
.IF user_count > 100
    writes(1, "Large user base detected")
.ELSE
    writes(1, "Small user base")
.ENDC
```

### .IFDEF for checking definitions:
```dbl
.DEFINE DEBUG_MODE
.IFDEF DEBUG_MODE
    writes(1, "Debug: Entering main routine")
.ENDC
```

### .IFNDEF for checking missing definitions:
```dbl
.IFNDEF ERROR_HANDLER
    .DEFINE ERROR_HANDLER
    ; Define default error handling
.ENDC
```

## Including external code
The .INCLUDE directive helps organize code by allowing you to split it across multiple files:

```dbl
.INCLUDE "database_config.dbl"
.INCLUDE "LIBRARY:common_routines"
```

Best practices:
1. Use UPPERCASE for defined constants to distinguish them from regular variables.
2. Define constants at the beginning of your code or in a separate include file.
3. Use parameterized macros for repeated code patterns that need slight variations.
4. Use conditional compilation to handle different environments (development, production) or feature flags.
5. Structure your includes to avoid circular dependencies.

For example, a well-organized DBL program might look like this:

```dbl
.DEFINE VERSION, "1.0.0"
.DEFINE MAX_CONNECTIONS, 50
.DEFINE LOG(message) writes(log_channel, "["+ %date() +"] "+ message)

.IFNDEF PRODUCTION
    .DEFINE DEBUG_MODE
.ENDC

.INCLUDE "config.dbl"

main
record
    connection_count, i4
proc
    .IFDEF DEBUG_MODE
        LOG("Application starting in debug mode")
    .ENDC

    if connection_count > MAX_CONNECTIONS
        LOG("Connection limit exceeded")
    .ENDC
end
```
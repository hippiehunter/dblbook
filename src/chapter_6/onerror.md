# OnError

`ONERROR` is an error handling mechanism that allows a program to specify how to proceed when a runtime error occurs. When an `ONERROR` statement is active, if the program encounters an error, instead of halting execution, it redirects flow to a predefined label, effectively a section of code designated to handle errors. This mechanism is a form of exception handling prior to the adoption of `try-catch` blocks. It allows the programmer to maintain control over the program's behavior in the face of errors, typically by logging the error, cleaning up resources, or attempting to recover gracefully. However, it is considered less sophisticated than modern exception handling techniques because it can lead to complex error control flows and difficulty in maintaining code, especially in larger systems.

### Syntax

```dbl
ONERROR label
```

Or, for more specific error handling:

```dbl
ONERROR(error_list) label[, label2, ...][, catch_all]
```

For clearing error traps:

```dbl
OFFERROR
```

#### Components:

- `label`: This is a statement label that acts as a goto target when an error occurs. The program execution jumps to the code under this label.

- `error_list`: A comma-separated list of error codes. If one of these errors occurs, the execution will jump to the associated label. These must be numerical values that the compiler can resolve at compile time.

- `catch_all`: An optional label that serves as a catch-all error handler if an error occurs that is not included in the specified `error_list`.

#### Behavior:

- The unqualified form (`ONERROR label`) sets a trap for any runtime error within the current subroutine/function/method, redirecting execution to the specified `label` regardless of the error type. This has historically been described as the "global" error trap but it is not truly global, it is only scoped to the current subroutine/function/method.

- The qualified form (`ONERROR(error_list) label`) allows for finer control, specifying particular errors to catch and where to jump in those cases.

#### Notes:

- When an `ONERROR` statement is set, it overrides any previous `ONERROR` traps.

- `ONERROR` traps remain active until one of the following occurs:
  - An `OFFERROR` statement is executed to clear error traps.
  - A new `ONERROR` statement is executed, replacing the current traps.
  - The execution of an external subroutine or function suspends the traps, which are reinstated upon return (in traditional DBL environments).
  - The program ends.

- If an error occurs that is not specified in any `ONERROR` statement or an error list and no `TRY-CATCH` block is present to handle it, the program will generate a fatal traceback.

- `ONERROR` provides a way to implement error handling in environments that do not support structured exception handling, but it must be used with caution to maintain clear and maintainable error-handling logic.

### Difficulties with ONERROR
`ONERROR` can complicate code maintenance for several reasons, especially in codebases that have large subroutines with that themselves contain many local routines. Here are some of the common issues:

1. **Complex Control Flow**: `ONERROR` redirects the program's execution to a specified label upon encountering an error. This can make it difficult to trace the flow of execution, because it's not always clear where an error will transfer control, especially if there are multiple `ONERROR` statements and you arent sure which one was last executed.

2. **Error Context Loss**: When an error occurs and `ONERROR` triggers a jump to a label, the context in which the error happened may be lost unless explicitly preserved. This loss of context makes debugging harder, as the original state of the program at the time of the error (such as variable values and the call stack) may not be readily available.

3. **Scattered Error Handling**: `ONERROR` tends to encourage scattered error handling logic, because each error case may jump to a different part of the code. This scattered approach makes understanding and revising error handling more cumbersome since the logic is not centralized.

4. **Inadvertent Error Masking**: If the error handling code does not address the error adequately or fails to re-raise the error for upper levels to handle, it can inadvertently mask errors. This may lead to scenarios where errors go unnoticed or are incorrectly logged, making debugging and maintenance challenging.

5. **Lack of Structured Cleanup**: Modern exception handling with `try-catch` often comes with a `finally` block, which is guaranteed to run for resource cleanup. `ONERROR` lacks this structured approach to cleanup, leading to potential resource leaks if the error handling code does not manually clean up every resource which may have been in use at the time of the error.

6. **Maintenance Overhead**: New developers or even experienced developers not familiar with the original design might find it hard to add new features or fix bugs without introducing new bugs because understanding the existing `ONERROR` implementations requires a significant amount of time and effort.

7. **Difficulty in Refactoring**: Because of the interwoven nature of error handling with `ONERROR`, refactoring—such as breaking down large subroutines into smaller, more manageable pieces—becomes much harder. There's a risk of altering the error handling behavior inadvertently, which can introduce new bugs into the system.

### Refactoring ONERROR

If you're thinking about refactoring to remove `ONERROR` here's a few strategies to consider:

1. **Assess the Error Handling Scope**: Review the existing `ONERROR` implementation to determine the scope of errors being handled. Identify the types of errors, especially those related to I/O operations, since these are candidates for I/O error lists.

2. **Implement I/O Error Lists**: For file I/O operations, replace `ONERROR` with specific I/O error lists. This means that within each I/O statement (like `READ`, `WRITE`, `OPEN`, etc.), define an error list to handle anticipated I/O errors directly. This approach has the benefit of localized error handling which makes the code easier to understand and debug.

3. **Use Structured Exception Handling**: For errors beyond I/O, or where a more sophisticated error recovery is required, replace `ONERROR` with `TRY-CATCH` blocks. This is a more modern approach to error handling that provides a clear structure for handling exceptions and can make the code more robust and maintainable.

4. **Selective Refactoring**: Decide when to use I/O error lists or exceptions based on the context. For example, if the error handling is complex or needs to account for many different kinds of errors, exceptions might be more appropriate. Conversely, for simple, I/O-specific errors, I/O error lists are simpler and more efficient.

5. **Consistency and Conventions**: Apply consistent error handling conventions across the codebase. This might involve setting up a standard way of dealing with certain types of errors or creating utility routines for common error handling patterns.

6. **Test Error Conditions**: After refactoring, rigorously test all error conditions to ensure that the new error handling works as expected. Automated tests can be particularly useful here to simulate various error conditions.

7. **Documentation and Comments**: Update code comments and documentation to reflect the new error handling mechanisms. This is crucial for maintaining the code in the future and for aiding other developers in understanding the error handling approach.

8. **Review Performance Implications**: Evaluate the performance implications of using exceptions versus I/O error lists. Exceptions can be more costly in terms of performance, so for critical sections of code where performance is key, and error conditions are well-understood and limited, I/O error lists might be preferred.

9. **Educate the Team**: If the refactoring is part of a team effort, ensure all team members understand the changes and the reasons behind them. Training sessions or code reviews can be helpful to disseminate the knowledge.

10. **Deprecate ONERROR Gradually**: In a large codebase, it may not be practical to eliminate all `ONERROR` usages at once. Instead, deprecate it gradually, starting with the most critical or easiest to refactor parts of the application.

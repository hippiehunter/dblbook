# Error Handling
This chapter will introduce a few different ways to handle errors in DBL. Before we get into the specifics of how to handle errors, I think there is value in understanding a few higher level concepts around error handling.

## Traceback

**Definition and use**: Traceback refers to the report of the active stack frames at a certain point in time during the execution of a program. In DBL, a traceback is available when an exception occurs. You can also manually build a traceback using the %ERR_TRACEBACK and %TRACEBACK routines. A traceback provides a snapshot of the call stack, showing the sequence of method or function calls that led to the error. This is crucial for identifying the point of failure in the code.

**Applicability**: Traceback is most useful immediately after an error has occurred, especially for unhandled exceptions. It helps in pinpointing the exact location and state of the program when the error happened.

### Understanding what you can do with a traceback

1. **Identify the error location**: The traceback typically starts with the most recent function call and traces back to the origin of the error. The first task is to identify where in the code the error occurred. Look for filenames, line numbers, and function names.

2. **Analyze call stack**: Examine the sequence of function or method calls that led to the error. This can help understand the flow of execution and how different parts of the code interact.

3. **Error message and type**: Pay close attention to the error message and the type of exception. This can provide immediate insight into what went wrong (e.g., `IndexError`, `NullReferenceException`).

### Diagnostic techniques using traceback

1. **Recreating the scenario**: Try to recreate the exact conditions under which the error occurred. This involves understanding the state of the application when the error was thrown.

2. **Variable state inspection**: If possible, inspect the state of variables at various points in the call stack. This might require rerunning the program in a debugger or adding logging statements if the issue isn’t reproducible in a debugging environment.

3. **Cross-referencing with source code**: Align the traceback with the actual source code. This step is crucial for understanding why the error occurred at a specific line or in a particular function.

4. **Checking function inputs**: Errors often occur because of unexpected or invalid input to functions. Verify that the functions in the traceback are receiving the correct parameters.

5. **Looking for patterns**: If similar tracebacks have been encountered before, compare them to identify common patterns or repeated errors in certain parts of the code.

6. **Analyzing third-party libraries**: If the traceback involves third-party libraries, check their documentation or source code (if available) to understand how they are expected to behave in the scenario leading up to the error.

7. **Consulting logs**: If your application maintains logs, consult them to gather more context around the error. Logs can provide information on the application's state leading up to the error.

### Advanced techniques

1. **Memory state**: In some cases, particularly with segmentation faults or memory leaks, examining the memory state of the application at the time of the error can be informative.

2. **Thread and concurrency analysis**: For multi-threaded applications, determine if the error is related to concurrency issues (like deadlocks or race conditions) by analyzing the state of different threads in the traceback.

3. **Historical comparison**: Compare the current traceback with historical data (if available) to identify whether recent changes in the codebase might have introduced the error.

In conclusion, effectively using a traceback for diagnostics requires a systematic approach to understand the execution flow and the state of the application. By carefully analyzing the traceback and correlating it with the source code and application state, developers can pinpoint the root cause of errors and apply appropriate fixes.

## Debugging

**Definition and use**: Debugging is a more interactive process, where the developer inspects, steps through, and modifies code to diagnose and fix errors. In DBL, debugging tools and environments allow you to set breakpoints, examine variable values, and step through code line by line.

**Applicability**: Debugging is applicable throughout the development process, but it is particularly valuable when you need to understand the behavior of your code or when you are trying to isolate and fix bugs that are not immediately apparent from the traceback alone.

> TODO: this might need its own chapter, if it does... include dump debugging rather than just interactive debugging

## Logging

**Definition and use**: Logging involves recording events and data outputs during the execution of a program. These logs can include errors, warnings, and informational messages that detail the application's runtime behavior. In DBL, logging can be implemented at various levels of detail, from critical errors to verbose diagnostic information.

**Applicability**: Logging is essential for ongoing monitoring and post-mortem analysis. It is useful both during development for tracking down elusive bugs and in production environments to monitor the health and performance of applications.

### Practical logging tips

#### Choosing the right logging level
- **Debug**: Provides detailed information, typically of interest only when diagnosing problems.
- **Info**: Confirms that things are working as expected.
- **Warn**: Indicates that something unexpected happened or a problem may occur in the near future (e.g., "disk space low"). The software is still working as expected.
- **Error**: Due to a more serious problem, the software has not been able to perform some function.
- **Fatal**: A severe error that has caused or is about to cause the application to stop has occurred.

#### Structured logging
Use structured logs (JSON, XML) over plain text. Structured logs can easily be parsed and analyzed by log management systems. Make sure you include key information like timestamp, log level, message, and context-specific data like user ID, session ID, etc. The exact fields you choose to include will depend on your application and use case, but consider how you will use that information in the event of an error.

#### Contextual logging
Provide enough context in your logs to understand the state of the application. This might include user data, current application state, or recent actions. When grabbing context for a log, it can be easy to accidentally pick up sensitive information (passwords, PII, etc.). Keep this in mind when performing code review for your colleagues.

#### Correlation IDs
Correlation IDs are especially important in a distributed system or microservices architecture. A correlation ID is just an identifier (usually a GUID) that follows your request around through the system to be output in all of your log messages. Use correlation IDs to track and correlate logs across services for a single-user request or transaction. Even if you don't think you have a distributed system, you will likely still derive a lot of value from this technique when working through a large volume of logs.

#### Performance considerations
Be mindful of the performance impact of logging. Excessive logging, especially at a finer granularity like debug level, can slow down the application.

#### Log rotation and retention
Implement log rotation to prevent logs from consuming too much disk space. Sometimes this is as simple as a script that renames the log file every night. If you need something a little more industrial strength, you would be well served by adopting a retention policy based on the importance and relevance of logs.

#### Standardize logging across the application
Use a consistent format and standard practices for logging across the entire application. This standardization makes log analysis more straightforward.

#### Security and compliance
- Ensure that logging practices comply with data protection regulations.
- Protect log data against unauthorized access.
- Be mindful of the security implications of logging sensitive information. Whenever possible, avoid logging sensitive information like passwords, PII, etc.

## Choosing the best method of attack

-   **Nature of the problem**: For immediate errors or crashes, a traceback provides the first clue. For more complex issues, particularly those related to logic or state, debugging is more appropriate. Logging is key for issues that manifest over time or in specific environments.
-   **Development vs. production**: While debugging is often a development-phase activity, logging is crucial in both development and production. Tracebacks are useful in both phases but are more often associated with unexpected errors in production.
-   **Performance considerations**: Extensive logging can impact performance, so it's important to balance the level of detail with the performance overhead. Debugging, being an interactive process, is not typically a concern in production environments.
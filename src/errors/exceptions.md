# Exceptions

1. **Understanding Exceptions:**
Exceptions are runtime errors or unexpected conditions encountered by the program. In structured languages like DBL or C#, exceptions disrupt the normal flow of an application. This could be anything from attempting to open a file that doesn't exist, to trying to read beyond the end of an array.

1. **The Try-Catch-Finally Structure:**
Exception handling is typically implemented using a try-catch-finally block. The "try" block contains code that might potentially cause an exception. The "catch" block is where you handle the exception, and the "finally" block contains code that is always executed, whether an exception occurs or not.
Here's how it's typically structured:
```
try
begin
  ;; Code that may cause an exception
end
catch (ex, @ExceptionType)
begin
  ;; Code to handle the exception
  ;; 'ex' is the caught exception object
end
finally
begin
  ;; Cleanup code that always runs
end
endtry
```

1. **Catching Specific Exceptions:**
It's considered good practice to catch specific exceptions rather than a general exception, as this allows more fine-grained error handling. You can have multiple "catch" blocks to handle different types of exceptions specifically.

1. **The Exception Object:**
When an exception is caught, information about the nature of the error is encapsulated in an exception object (denoted as `ex` in the catch block above). This object can contain several properties that you can use to understand the error better, such as the cause of the exception, the stack trace, and more.

1. **The Finally Block:**
The "finally" block is optional but important for resource management. Code within this block will always run, regardless of whether an exception was thrown. This characteristic makes it the ideal location for cleanup code, such as closing file streams or releasing any resources that were locked.

1. **Throwing Exceptions:**
Apart from handling exceptions, you can also throw your own exceptions using the `throw` keyword when you detect an unrecoverable state in your program. You can either throw a pre-defined system exception or a custom exception that you define.

1. **Custom Exceptions:**
If the predefined system exceptions aren't sufficient, you can define your own exception classes. These are particularly useful to indicate specific errors that are unique to your application's business logic.

Exception handling is about maintaining control over an application when abnormal conditions occur. It prevents the application from terminating abruptly, allowing it to handle the error gracefully, which might include informing the user, logging the error for future analysis, or gently shutting down the process. Remember, while it's important to catch exceptions, overuse of exception handling can lead to code that's difficult to understand and maintain. As a rule of thumb, use exceptions for exceptional conditions, not the normal flow of control. Below, I'll outline two scenarios: one where using exceptions is almost universally considered appropriate, and another where avoiding exceptions is a better choice.

### Scenario 1: When to Use Exceptions - Handling File Operations

**Context:**
Imagine a scenario where your application needs to read data from a configuration file that might not exist or could be inaccessible due to permission issues or runtime changes.

**Use of Exceptions:**
In this scenario, using exceptions is appropriate. File operations are inherently prone to numerous unexpected issues:

1. **File Doesn't Exist:** The file you're trying to read doesn’t exist.
2. **Permission Issues:** The application doesn't have the necessary permissions to read the file.
3. **File Locked:** The file is being used by another process.
4. **I/O Errors:** There's an issue with the disk, leading to read/write failures.

Attempting to handle all these conditions using return codes and conditional logic would make the code cumbersome, less readable, and hard to maintain. An exception handling mechanism shines here, as it centralizes the error-handling code and cleanly separates it from the main business logic:

```dbl,ignore,does_not_compile
try
begin
    ;; Attempt to read the file
    data fileContent = File.ReadAllText("config.txt")
end
catch (ex, @FileNotFoundException)
begin
    ;; Handle case when file doesn't exist
end
catch (ex, @UnauthorizedAccessException)
begin
    ;; Handle permission issues
end
catch (ex, @IOException)
begin
    ;; Handle other I/O errors
end
```

In this case, exceptions allow you to handle each error type explicitly and provide a clear, high-level view of the different error types you're anticipating.

### Scenario 2: When to Avoid Exceptions - Validating User Input

**Context:**
Consider an application form where users input their age. The age must be a positive integer and less than 120.

**Avoiding Exceptions:**
Using exceptions to handle basic validation checks is generally frowned upon. For example, throwing an exception if a user inputs a non-numeric value, or a number that's out of the expected range, is considered poor practice. This isn't an "exceptional" scenario; users often make input mistakes, and these are predictable and expected situations.

Instead, you should use standard conditional logic to handle these cases because they are part of the normal flow of the application:

```dbl,ignore,does_not_compile
data age, int
data userInput = GetAgeInput()
if (!int.TryParse(userInput, age) || age < 0 || age >= 120) then
begin
    ;; Display an error message to the user
end
else
begin
    ;; Proceed with the age value
end
```

In this scenario, using exceptions would complicate the code without providing any clear benefit. It can also degrade performance, as exceptions are resource-intensive to handle and should be reserved for truly exceptional conditions that the application cannot easily predict or prevent.


## Stack Unwinding
Regardless of using .NET or Traditional DBL, when an exception is thrown the runtime will start the process of stack unwinding. This involves stepping back through the call stack — the record of nested method calls — to find the appropriate exception handler (catch block). When an exception is thrown, the runtime begins to search for a method in the call stack with a catch block that can handle the exception. This search moves from the point where the exception was thrown, up through the call stack, effectively "unwinding" it. Each method in the call stack (from the point where the exception was thrown to the method that handles it) is exited in a controlled manner, and any finally blocks in these methods are executed. If the runtime reaches the top of the call stack without finding an appropriate exception handler, the program terminates. This mechanism ensures that resources are cleanly released and that the system remains in a known state, even when unexpected scenarios occur. To illustrate we'll use a scenario where we're trying to process a file:

```dbl
namespace UnwindingExample

    public class FileProcessor
    
        public method ProcessFile, void
            filePath, string 
            endparams
        proc
            try
            begin
                data fileContent, string
                ValidateFilePath(filePath)
                fileContent = DoSomeStuff(filePath)
                ProcessContent(fileContent)
            end
            catch (ex, @Exception)
            begin
                Console.WriteLine("An error occurred: " + ex.Message)
            end
            endtry
        endmethod
    
        private method ValidateFilePath, void
            filePath, string 
            endparams
        proc
            if (string.IsNullOrEmpty(filePath))
            begin
                throw new ArgumentException("File path cannot be null or empty.")
            end
        endmethod
    
        private method DoSomeStuff, string
            filePath, string
            endparams
        proc
            try
            begin
                mreturn ReadFile(filePath)
            end
            finally
            begin
                ;;  Do some cleanup
                Console.WriteLine("Cleaning up in DoSomeStuff")
            end
            endtry
        endmethod


        private method ReadFile, string
            filePath, string 
            endparams
        proc
            ;;  Let's assume an exception is thrown here because the file doesn't exist.
            throw new NoFileFoundException()
        endmethod
    
        private method ProcessContent, void
            content, string 
            endparams
        proc
        
            ;;  Process the file content
        endmethod
    endclass

endnamespace
```

In this code:

```svgbob
Before Exception:
+-----------------+
| Main            | <- Root of the stack
+-----------------+
| ProcessFile     | 
+-----------------+
| DoSomeStuff     |
+-----------------+
| ReadFile        | <- Execution is here
+-----------------+

During Stack Unwinding:
+-----------------+
| Main            | <- Root of the stack, unaffected
+-----------------+
| ProcessFile     | <- Exception caught here, unwinding stops, execution continues inside the catch block
+-----------------+
| DoSomeStuff     | <- Stack is unwinding, executing code in the finally block
+-----------------+
| ReadFile        | <- NoFileFoundException thrown here, unwinding begins
+-----------------+
```


1. `ProcessFile` method is called to start the file processing.
2. Inside `ProcessFile`, `ValidateFilePath` is first called to check the validity of the file path.
3. Next, `DoSomeStuff` is called to perform some operations on the file.
4. Inside `DoSomeStuff`, `ReadFile` is called to read the file content, but let's assume the file doesn't exist, so a `NoFileFoundException` is thrown.

At this point, stack unwinding begins due to the unhandled `NoFileFoundException`:

1. The runtime starts the unwinding process, looking for a catch block that can handle a `NoFileFoundException`. 
2. It first checks the current method, `ReadFile`, but doesn't find a suitable exception handler.
3. The runtime then leaves the `ReadFile` method and moves to the previous method in the call stack, which is `DoSomeStuff`.
4. In `DoSomeStuff`, it finds no catch block to handle the `NoFileFoundException`, but it does find a `finally` block. The `finally` block is executed, and the runtime continues unwinding in `ProcessFile`
5. In `ProcessFile`, it finds a catch block that handles the base `Exception` type (which can catch any exception). The `NoFileFoundException` is successfully caught here, and the error message is printed to the console.
6. The `finally` block, if it existed within the `try-catch` block of `ProcessFile`, would execute after the catch block, regardless of whether an exception was caught.

During this unwinding process, the stack is effectively being "unwound" from the point of the exception (inside `ReadFile`) back up through the nested method calls (`DoSomeStuff` and `ProcessFile`) until it finds an appropriate handler. If there were more method calls nested, the runtime would continue unwinding through them in the same manner.

> #### Note about local calls
> There's really no interaction with local calls during stack unwinding. The compiler will not allow you to call a local procedure within a try-catch block. The specific error is `DBL-E-LBLSCOPE` telling you that the label is out of scope. The error is technically correct though it could be more informative.

## Exceptions in legacy code
Error list is more efficient and can be easier to fully understand when working with synergy io statements
onerror is nasty business and you should avoid or rework it when possible to do so safely.
TODO: explain that all error handling can be converted to exceptions. Give a good example of onerror -> exception and error list -> exception
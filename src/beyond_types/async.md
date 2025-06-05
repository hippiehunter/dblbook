# Async
Think about making breakfast. In a traditional single-threaded approach, you might heat up the pan, wait for it to get hot, crack an egg, wait for it to cook, then start toasting bread, wait for it to brown, and finally plate everything. Each step happens one after another, and you're basically standing there waiting at each step.

Async programming is more like how you'd actually cook breakfast: you put the pan on to heat while you get out the eggs and bread. While the egg is cooking, you start the toast. You're not doing multiple things simultaneously yourself (that's what true multi-threading would be), but you're able to start one task and work on something else while waiting for it to complete.

The `async` modifier on a method or lambda tells the compiler "this code will start some tasks and continue working while waiting for them to finish." However, just marking something as `async` doesn't make it asynchronous by itself - you need to use the `await` keyword to identify points where you're waiting for something to finish.

Here's a set of somewhat simple examples that uses an HttpClient to make a request and then waits for that request to finish before continuing:
```dbl
public async method MakeTheCall, @Task
proc
    
endmethod
```

When this code hits the `await` keyword, it's like putting something in the microwave and stepping away - the method returns control to whatever called it, allowing other code to run while waiting for the download to complete. Once the download finishes, the code after the `await` continues executing.

One important detail: an async method can only "pause" at an await statement. Before the first await (or if there are no awaits at all), the method runs synchronously just like any other code. That's why you may not want to be doing time-consuming work before your first await statement - you're still blocking the calling code during that time.

The return type of an async method is important too. In DBL, async methods must return either `void`, `@Task`, or `@Task<>`. You can think of a `Task` as a promise that work will be completed. If your method needs to return a value, you'll use `Task<YourReturnType>`, and the caller will need to await your method to get the actual value. Async methods that return `void` 

Async void methods should generally be avoided except for one specific use case: event handlers. The reason comes down to error handling and how these methods interact with the rest of your async code.

When you have an async method that returns a Task, the caller can await that Task and catch any exceptions that occur during its execution. However, with async void methods, there's no Task to await - meaning exceptions can't be caught by the calling code. Instead, these exceptions end up going directly to your application's global exception handler, which can make debugging and error handling much more difficult.

Think of it like the difference between having a safety net (async Task) versus not having one (async void). With async Task, if something goes wrong, the exception gets caught in the net and you can handle it gracefully. With async void, it's like removing the safety net - any errors that occur will propagate up through your application in ways that are hard to predict and control.

The reason async void exists at all is historical: when async/await was added to .NET, existing event handler signatures like `button1.Click += async lambda(sender, e) { ... }` needed to work with async code. Since these event handlers return void by convention, async void was created to support this specific scenario. For any other async method you write, returning a Task is almost always the better choice as it gives you proper error handling and the ability to await the operation's completion.

The same principles apply to async lambdas - they're just compact methods that can capture variables from their surrounding scope. They need to be marked with the `async` modifier if they're going to use `await`, and they have the same restrictions on return types and what variables they can access.

The key to understanding async/await is remembering that it's not about doing multiple things at once - it's about being able to start something that might take a while and do other useful work while you wait for it to complete, just like an efficient cook in the kitchen.

## Blocking with Wait() or Result
When you call .Result or .Wait() on a Task, you're essentially telling your code "stop everything and wait right here until this completes." This might seem like a simple way to get synchronous code to work with async code, but it can lead to deadlocks in certain contexts, particularly in UI applications or ASP.NET.
Here's why: Many modern frameworks, including Windows Forms, WPF, and ASP.NET, use a synchronization context to ensure certain code runs on the right thread. When you await an async method, the framework captures this context and uses it to resume the method on the correct thread. However, when you block with .Result, you're holding onto that thread while waiting for code that might need that same thread to complete.

Let's make this concrete with an example:
```dbl
; UI Button Click Handler
public method button1_Click, void
    tkSource, @object 
    tkArgs, @EventArgs 
proc
    ; This is running on the UI thread
    ; DANGEROUS! Could deadlock because we're blocking the UI thread
    data result, string
    result = GetDataAsync().Result
endmethod

public async method GetDataAsync, @Task<String>
proc
    ; This will try to resume on the UI thread
    await Task.Delay(1000) ; Simulating work
    mreturn "Hello"
endmethod
```

This can deadlock because:

1. The UI thread calls GetDataAsync().Result and blocks
1. GetDataAsync tries to resume on the UI thread after the delay
1. But the UI thread is blocked waiting for the Result
1. Neither piece can proceed - classic deadlock

There are several ways people try to work around this we'll start with using `.ConfigureAwait(false)` in the async method:

```dbl
public async method GetDataAsync, @Task<String>
proc
    ; Tell the runtime we don't need to resume on the original thread
    await Task.Delay(1000).ConfigureAwait(false)
    mreturn "Hello"
endmethod
```

This tells the async method "don't try to resume on the original thread." It can prevent the deadlock, but it's a band-aid that can cause other issues if you actually needed that context. Next we'll look at using `GetAwaiter().GetResult()`

```dbl
public method button1_Click, void
    tkSource, @object 
    tkArgs, @EventArgs 
proc
    ; Still dangerous! Just changes how the exception is wrapped
    data result, string
    result = GetDataAsync().GetAwaiter().GetResult()
endmethod
```

This is functionally similar to `.Result` but throws the original exception rather than wrapping it in an `AggregateException`. It still has the same deadlock risks though. For our third option we'll use `Task.Run` to escape the current context and then block using `.Result`.

If you absolutely must call async code from synchronous code (like in a console app's Main method), you can use a dedicated async entry point or create a new context

```dbl
main
proc
    ; Create a dedicated context for async operations
    data context, @AsyncContext
    context = new AsyncContext()
    
    ; Run our async code in this context
    async lambda myAsync()
    begin
        data result, string
        result = await GetDataAsync()
        writes(1, result)
    end
    context.Run(myAsync)
endmain
```

> ### Remember
> Trying to force async code to be synchronous is like trying to push water uphill - you can do it, but it's fighting against the natural flow and will likely cause problems. Instead, design your code to work with the async nature of modern programming as early as possible.
> The performance implications are worth mentioning too - blocking a thread, especially on a web server, means that thread can't be used to handle other requests. In high-throughput scenarios, this can significantly reduce your application's ability to scale
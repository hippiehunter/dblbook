# Async
Think about making breakfast. In a traditional single-threaded approach, you might heat up the pan, wait for it to get hot, crack an egg, wait for it to cook, then start toasting bread, wait for it to brown, and finally plate everything. Each step happens one after another, and you're basically standing there waiting at each step.

Async programming is more like how you'd actually cook breakfast: you put the pan on to heat while you get out the eggs and bread. While the egg is cooking, you start the toast. You're not doing multiple things simultaneously yourself (that's what true multi-threading would be), but you're able to start one task and work on something else while waiting for it to complete.

The `async` modifier on a method or lambda tells the compiler "this code will start some tasks and continue working while waiting for them to finish." However, just marking something as `async` doesn't make it asynchronous by itself - you need to use the `await` keyword to identify points where you're waiting for something to finish.

Here's a somewhat simple example that uses an HttpClient to make a request and then waits for that request to finish before continuing:
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
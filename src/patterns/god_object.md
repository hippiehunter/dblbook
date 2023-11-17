# God Object
TODO: Sub type super routines but avoid the larger tendency to create god objects.
### Super routines

At times, developers tackling a set of closely intertwined tasks might create a super routine, wherein the sub function executed hinges on a provided parameter. Instead of having distinct routines for each task, these "multi-task" routines use the parameter to branch internally and either execute large blocks of code directly or `call` the appropriate local routine. While this approach can sometimes simplify the calling interface or reduce code repetition, it can also lead to routines that are harder to maintain and understand. Such routines can inadvertently violate the Single-Responsibility Principle, as they take on multiple responsibilities based on parameter values. Over time, as more sub-functions are added or existing ones are modified, the complexity of these parameter-driven routines can increase, posing challenges in testing, debugging, and ensuring consistent behavior across all parameter values. Instead of a single routine handling multiple tasks based on a parameter, developers can create separate, well-named functions for each task. This promotes clarity and makes it easier to test and maintain each function independently. Here are some alternatives to consider if just using separate functions wont do the trick.

1.  **Strategy Pattern**: This is an object-oriented design pattern that can be used to switch between different algorithms or strategies at runtime. Rather than using a parameter to select a branch of code, different strategies are encapsulated as separate objects that can be plugged into a context object as needed.

2.  **Command Pattern**: Instead of determining the function to run based on parameters, you can encapsulate each function within a command object and invoke them using a consistent interface. This is particularly useful when you need to queue operations, support undo/redo operations, or decouple the requester from the operation itself.

3.  **Lookup Tables or Dictionaries**: Instead of branching logic based on parameters, map functions or methods to keys in a lookup table or dictionary. This way, the desired function can be retrieved and executed dynamically based on the provided key, ensuring a cleaner and more efficient structure.

4.  **Higher-Order Functions**: In .NET, functions can be treated as first-class citizens, allowing them to be passed as arguments or returned as values from other functions. Instead of relying on parameters for branching, functions can be passed around to achieve the desired behavior.

5.  **Factory Pattern**: For situations where you need to instantiate one of several possible classes based on input, the Factory design pattern provides a mechanism to create objects without specifying the exact class to be created. This encapsulates the instantiation logic and can replace the need for super routines in object creation scenarios.

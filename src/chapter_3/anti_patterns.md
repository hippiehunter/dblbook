# Anti-Patterns


### Big Ball Of Mud
The "big ball of mud" is a term used in software engineering to describe a system that lacks a discernible architecture. It is often considered an anti-pattern, representing a codebase that has grown haphazardly and chaotically over time, resulting in a monolithic structure where everything is entwined into a single, tightly-coupled entity. This often occurs when rapid growth and immediate functionality are prioritized over sustainable design and future scalability.

In the DBL environment, this pattern is frequently exhibited through extensive use of `GLOBAL` and `COMMON`. These large collections of global data variables can become sprawling, where understanding the interplay and dependencies among the variables can be a daunting task. As a result, the system becomes progressively more entangled, complex, and interconnected, resembling a 'big ball of mud'.

This approach results in an overly coupled system where modules depend on shared state, and changes in one module can lead to unpredictable effects in other modules due to this shared global state. This makes maintenance and updates increasingly difficult due to the extensive interconnectedness of components. The challenge is further amplified when the system needs to scale, as the tightly-coupled nature of the system and its global state can hinder concurrency and parallel processing. Thus, the "big ball of mud" pattern, with its reliance on global data, often leads to a stifling of innovation, slow development speed, and an increase in the risk of bugs and system failures.

Refactoring a "ball of mud" into a better-organized codebase is a significant effort, but it can pay off in the long run with increased maintainability and scalability. Here are some steps that can be taken:

1.  **Identify and isolate business logic:** The first step in refactoring is to identify key business processes in your code. Once these are found, they should be isolated and separated from unrelated components. This helps reduce dependencies between different parts of your code.

2.  **Adopt modular design principles:** Aim for a design where each module has a single, well-defined responsibility. Such a modular design makes it easier to modify or replace individual modules without affecting the rest of the codebase.

3.  **Enforce strict boundaries between components:** Use interfaces, abstract classes, or similar constructs to enforce boundaries between different components. This ensures that each component communicates with others only through well-defined channels.

4.  **Incremental refactoring:** Instead of trying to refactor the entire codebase at once, prioritize the most problematic areas and tackle them first. Then gradually refactor other areas as and when required. This ensures the refactoring process doesn't disrupt regular development work too much.

5.  **Automate testing:** Automated testing is crucial when refactoring. This ensures that you haven't introduced any regressions in the process. It also enables you to make future changes with confidence.

6.  **Continuous education:** Ensure your development team is educated about good design principles and the value of a clean, well-structured codebase. This encourages everyone to maintain good practices as they continue to develop new features.

Moving from a monolithic "ball of mud" structure to a more modular and maintainable architecture is not an easy task. However, with a thoughtful approach and dedication to good design principles, it is certainly achievable and worthwhile in the long run.

### Circular References

Circular references manifest when two or more entities, be they functions, modules, or data structures, depend on each other either directly or indirectly, thus creating a loop of dependencies. This phenomenon is particularly noticeable in procedural programming, where the flow of execution revolves around procedures or functions, and modules that contain related functions. At a higher level, they can manifest between libraries or projects, where one library depends on another, which in turn depends on the first. This kind of circularity at the library or project level can pose significant challenges for building, maintaining, and deploying software.

**Why They Should Be Avoided**

1.  **Initialization Issues**: In procedural systems, the sequence of operations matters immensely. Circular dependencies can create ambiguity in the sequence in which functions or modules should be executed. If function A relies on function B, but function B in some way relies on A, then deciding which to run first becomes a challenge.

2.  **Maintenance Difficulty**: Code with circular references tends to be more intricate and harder to decipher. If you change one function that's part of a circular dependency, it's easy to inadvertently affect others in the loop, potentially introducing unforeseen bugs.
   
3.   **Build and Deployment Challenges**: When two libraries reference each other, building or deploying them in isolation becomes problematic. Determining which library should be built, tested, or deployed first is ambiguous when each relies on the other.

4.  **Tight Coupling**: This is a core concern with circular references. Tight coupling refers to when different parts of a program are interdependent, making them reliant on each other's detailed implementation. The dangers of tight coupling include:

    -   **Reduced Flexibility**: Changes in one function might necessitate changes in another due to their close interdependence.
    -   **Reduced Readability**: New developers or even the original programmers might find it difficult to understand the flow and logic of tightly coupled code, especially after some time has passed.
    -   **Reduced Reusability**: When functions are tightly coupled, extracting one for use in a different context becomes challenging since it's intertwined with other functions.
    -   **Increased Fragility**: A change or a bug in one function can have cascading effects throughout the system, making the program more prone to errors and harder to debug.

**Techniques to Refactor Circular References in Procedural Code**

1.  **Reorder Operations**: Evaluate if the operations in the dependent functions can be reordered to break the cycle. Sometimes, resequencing tasks within functions can prevent the need for cyclic calls.

2.  **Pass Data Instead of Making Calls**: If one function is calling another just to get some data, consider if that data can be calculated beforehand and passed as an argument instead.

3.  **Restructure Libraries or Modules**: Whether dealing with intricate operations in procedural code or dependencies between libraries, extracting the common elements causing circularity can be a strategic move. By isolating these elements into separate helper functions, utility modules, or shared components, multiple functions or libraries can then reference this central entity. This approach not only breaks the circular dependencies but also emphasizes a reliance on shared abstractions or utilities rather than direct, intertwined dependencies.

4.  **Dependency Inversion**: Instead of one library depending on another, both libraries can be made to depend on a third abstraction or a shared component. This inverts the dependencies, ensuring that higher-level libraries are not dependent on lower-level details, but rather on abstractions.

5.  **Reevaluate Design Decisions**: At both the procedural and library levels, circular references often hint at underlying design issues. Whether it's the architecture, responsibilities, relationships of components, or the logic flow within a program, such circularities may indicate the need for a design review. Revisiting the overall structure, architecture, and design of the system can help identify and implement more linear or hierarchical structures, ensuring smoother operations and reducing dependencies.


### Additive Library Builds
The ability to additively link or replace routines over time using DBO's, ELB's and OLB's has historically facilitated quick debugging and streamlined deployment. In more recent times with higher speed internet connections and more powerful systems these benefits have ceased to outweigh the considerable downsides.

1.  **State Accumulation in ELBs**: As ELB/OLB's evolve with routines being linked in or replaced, they can accumulate states, including outdated configurations or remnants of previous routines. This leftover state can introduce unexpected behaviors or bugs that might not manifest in a cleanly built library.

2.  **Version Control Difficulties**: While traditional version control systems track changes in source files, the additive model of ELBs might complicate versioning. It can become challenging to discern specific changes between ELB versions or to merge alterations from different developers.

3.  **Dependency Challenges**: Over time, as more routines are added or replaced in an ELB, it can be difficult to discern which parts are actively used and which have become obsolete or redundant. While not directly causing memory overhead, these lingering routines can introduce potential conflicts, bugs, or increased complexity.

4.  **Onboarding New Developers**: For developers unfamiliar with the DBL environment and the specific history of an additively built library, there can be a learning curve. Comprehending the intricacies and evolution of an library, developed over an extended period, can be challenging.

5.  **Deployment Variability**: While some users argue that the DBL system simplifies deployment by allowing for tiny patches, this approach can lead to environments with slightly different library states, especially if patches are applied inconsistently. This variability can make debugging and troubleshooting harder since not all environments are guaranteed to be identical.

Today you can use MSBuild based projects to get highly controlled well structured builds that produce the same artifact for a clean build or an incremental one. Depending on your project structure this process can actually be even faster than manual additive builds using custom scripts.

### Super routines

At times, developers tackling a set of closely intertwined tasks might create a super routine, wherein the sub function executed hinges on a provided parameter. Instead of having distinct routines for each task, these "multi-task" routines use the parameter to branch internally and either execute large blocks of code directly or `call` the appropriate local routine. While this approach can sometimes simplify the calling interface or reduce code repetition, it can also lead to routines that are harder to maintain and understand. Such routines can inadvertently violate the Single-Responsibility Principle, as they take on multiple responsibilities based on parameter values. Over time, as more sub-functions are added or existing ones are modified, the complexity of these parameter-driven routines can increase, posing challenges in testing, debugging, and ensuring consistent behavior across all parameter values. Instead of a single routine handling multiple tasks based on a parameter, developers can create separate, well-named functions for each task. This promotes clarity and makes it easier to test and maintain each function independently. Here are some alternatives to consider if just using separate functions wont do the trick.

1.  **Strategy Pattern**: This is an object-oriented design pattern that can be used to switch between different algorithms or strategies at runtime. Rather than using a parameter to select a branch of code, different strategies are encapsulated as separate objects that can be plugged into a context object as needed.

2.  **Command Pattern**: Instead of determining the function to run based on parameters, you can encapsulate each function within a command object and invoke them using a consistent interface. This is particularly useful when you need to queue operations, support undo/redo operations, or decouple the requester from the operation itself.

3.  **Lookup Tables or Dictionaries**: Instead of branching logic based on parameters, map functions or methods to keys in a lookup table or dictionary. This way, the desired function can be retrieved and executed dynamically based on the provided key, ensuring a cleaner and more efficient structure.

4.  **Higher-Order Functions**: In .NET, functions can be treated as first-class citizens, allowing them to be passed as arguments or returned as values from other functions. Instead of relying on parameters for branching, functions can be passed around to achieve the desired behavior.

5.  **Factory Pattern**: For situations where you need to instantiate one of several possible classes based on input, the Factory design pattern provides a mechanism to create objects without specifying the exact class to be created. This encapsulates the instantiation logic and can replace the need for super routines in object creation scenarios.

### Inconsistent Globals

### Multiple Definitions Of The Same Routine
# Libraries
This section is something of a description of how things work, what tools are available to you and a choose your own adventure game. You very likely have or have had at your workplace a script driven manual traditional dbl build system that contains `.dbo`, `.olb` and `.elb` files in some form. Outside of developers that need to deploy to OpenVMS you can make use of MSBuild to reproduce this script based environment to allow your IDE or CI/CD pipeline to build your projects.

## Traditional DBL (Manual)

**Creating Object Libraries**

*Historical Context:* In the past, developers often created object libraries to manage multiple related external subroutines efficiently. These OLB's were a collection of compiled object files consolidated into a single special file for easier reference during the linking process. By using an object library, developers could avoid managing and distributing numerous `.dbo` files, opting for a single `.olb` file. Once they had the `.olb` file, they could link it into their mainline program using the linker. In terms of distribution they would only have the mainline `.dbr` to distribute and the referenced contents of the `.olb` would be injected into the `.dbr`. This was an especially common practice on OpenVMS where `.elb` files were not supported and shared images had noticeable limitations. Because linking an `.olb` into a `.dbr` will only include explicitly referenced routines they are inappropriate for routines that are called dynamically at runtime such as a global io hook.

**Linking Your Program**

*Modern Perspective:* In modern development, linkers are integrated into most IDEs and build tools, making their operations more transparent to developers.

*Description:* The Synergy linker consolidates compiled object files into a cohesive executable program. By default, it expects object files to have the `.dbo` extension. The linker produces:

-   `.dbr` for Executable files
-   `.map` for Map files
-   `.elb` for Executable libraries

*Note to Developers:* Always recompile changed object files before relinking to ensure the updated code is incorporated into the new executable.

**Creating Executable Libraries**

*Description:* Unlike object libraries, executable libraries don't include the object file in the final executable. Instead, they contain pointers to routines within the executable library. At runtime, the Synergy runtime uses these pointers to execute code from the library. This approach offers several advantages:

1.  **Reduced Program Size:** Executable libraries can decrease the size of your programs since they avoid duplicating compiled subroutines in every program.
2.  **Time Savings:** If an object file within the executable library changes, there's no need to relink all the dependent programs. The runtime uses the updated routine from the library directly.
3.  **Efficient Distribution:** Updates to an application can be distributed by just replacing the executable library, eliminating the need for relinking.
4.  **Synergy Linker Creation:** Developers can either convert existing object libraries into executable libraries or directly compile object files into an executable library.

*Modern Perspective:* The distinction between object libraries and executable libraries might feel odd for developers familiar with dynamic (shared) libraries in languages like C or C++. However, the principle is somewhat similar to using shared libraries in other programming environments.

* * * * *

*General Annotation:* This document seems to stem from a time when manual linking and object file management were more prevalent in development workflows. Modern development environments often abstract these details, offering automated building, linking, and deployment. Developers new to the Synergy Language might find the distinction between object and executable libraries somewhat arcane, but understanding the historical context can provide insight into the evolution of programming practices.

## Traditional DBL (MSBuild)
## .NET

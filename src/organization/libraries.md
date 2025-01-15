# Libraries
This section essentially combines a description of how things work and what tools are available to you with a developer version of a "choose your own adventure" story. At some point, you very likely have had at your workplace a script-driven manual traditional dbl build system that contains `.dbo`, `.olb`, and `.elb` files in some form. Unless you are a developer who needs to deploy to OpenVMS or AIX, you can make use of MSBuild to reproduce this script-based environment to allow your IDE or CI/CD pipeline to build your projects.

## Traditional DBL (manual)

**Creating object libraries**

*Historical context:* In the past, developers often created object libraries to manage multiple related external subroutines efficiently. These OLBs were a collection of compiled object files consolidated into a single, special file for easier reference during the linking process. By using an object library, developers could avoid managing and distributing numerous `.dbo` files, opting instead for a single `.olb` file. Once they had the `.olb` file, they could link it into their mainline program using the linker. They only had to distribute the mainline `.dbr`, and the referenced contents of the `.olb` would be injected into the `.dbr`. This was an especially common practice on OpenVMS. where `.elb` files were not supported and shared images had noticeable limitations. Because linking an `.olb` into a `.dbr` will only include explicitly referenced routines, they are inappropriate for routines that are called dynamically at runtime, such as a global I/O hook.

**Linking your program**

*Modern perspective:* In modern development, linkers are integrated into most IDEs and build tools, making their operations more transparent to developers.

*Description:* The DBL linker consolidates compiled object files into a cohesive executable program. By default, it expects object files to have the `.dbo` extension. The linker produces

-   `.dbr` for executable files
-   `.map` for map files
-   `.elb` for executable libraries

*Note to developers:* Always recompile changed object files before relinking to ensure the updated code is incorporated into the new executable.

**Creating executable libraries**

*Description:* Unlike object libraries, executable libraries don't include the object file in the final executable. Instead, they contain pointers to routines within the executable library. The DBL runtime uses these pointers to execute code from the library. This approach offers several advantages:

1.  **Reduced program size:** Executable libraries can decrease the size of your programs since they avoid duplicating compiled subroutines in every program.
2.  **Time savings:** If an object file within the executable library changes, there's no need to relink all the dependent programs. The runtime uses the updated routine from the library directly.
3.  **Efficient distribution:** Updates to an application can be distributed by just replacing the executable library, eliminating the need for relinking.
4.  **DBL linker creation:** Developers can either convert existing object libraries into executable libraries or directly compile object files into an executable library.

*Modern perspective:* The distinction between object libraries and executable libraries might feel odd for developers familiar with dynamic (shared) libraries in languages like C or C++. However, the principle is somewhat similar to using shared libraries in other programming environments. Modern development environments often abstract these details, offering automated building, linking, and deployment. Developers new to DBL might find the distinction between object and executable libraries somewhat arcane, but understanding the historical context can provide insight into the evolution of your codebase. Next, we'll look at how to use MSBuild to automate complex build processes, manage dependencies more efficiently, and integrate seamlessly with various development tools, providing a more scalable and maintainable approach to building software.

## Traditional DBL (MSBuild)
TODO: Write this section
## .NET
TODO: Write this section

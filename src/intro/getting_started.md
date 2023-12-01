# Getting Started
In this section, we'll walk through the steps to create a new "Hello World" project using the .NET Command Line Interface (CLI) and a custom template named "SynNetApp". The .NET CLI is a powerful tool that allows you to create, build, and run .NET projects from the command line. Don't worry if your company isn't currently targeting .NET, the important parts of this walkthrough are showing how projects can be built and what sort of artifacts you can expect as output.

#### Step 1: Open Your Command Line Interface

First, open a Windows Command Prompt or Terminal window. You'll use this to run the .NET CLI commands.

#### Step 2: Install the SynNetApp Template

Before creating a project, you need to ensure that the "Synergex.Projects.Templates" package is installed on your system. If you haven't installed it yet, you can do so by running the following command:

```bash
dotnet new install Synergex.Projects.Templates
```

This command will download and install all of the project templates you might want to use, making it available for use.

#### Step 3: Create a New Project

Now, you're ready to create a new project. Navigate to the directory where you want to create your project and run:

```bash
dotnet new synNETApp -n HelloWorld
```

Here, `SynNetApp` is the template name, and `-n HelloWorld` specifies the name of your new project. You can replace `HelloWorld` with whatever project name you prefer.

#### Step 4: Navigate into Your Project Directory

Once the project is created, navigate into the project directory:

```bash
cd HelloWorld
```

Replace `HelloWorld` with your project name if you chose a different one.

#### Step 5: Explore the Project Structure

Take a moment to explore the newly created project structure. You should see two files:
- `Program.dbl`: contains the entry point of the application. This is where the application starts executing.
- `HelloWorld.synproj`: contains the project definition in an MSBuild-compatible XML format. This file defines the project's name, source files, options and dependencies.

#### Step 6: Customize the Hello World Message

Open `Program.dbl` in your favorite text editor. You'll see a line of code that looks something like this:

```dbl
import System

main
proc
    Console.WriteLine("Hello World")
endmain
```

Feel free to customize this message to something else, like:

```dbl
Console.WriteLine("Hello from the DBL book!")
```

#### Step 7: Run Your Application

Finally, it's time to run your application. Go back to your command line interface and execute:

```bash
dotnet run
```

This command will compile and execute your application. You should see your custom "Hello World" message displayed in the console.

Congratulations! You've successfully created and run a new .NET application using the synNETApp template. Lets scrape away the very helpful dotnet cli and try the build and run steps manually.

#### Step 8: Build Your Application with MSBuild
So far you've seen dotnet cli act as a wrapper around MSBuild. Lets try to build the project using MSBuild directly. From the command line, run:

```bash
msbuild HelloWorld.synproj
```

This command will compile your application and produce an executable file named `HelloWorld.exe` under the `bin/Debug` directory. Depending on the version of .NET you're running you should see something like a `net6.0` or `net8.0` folder. Once you cd to that inner directory You can run the executable by executing:

This command compiles your application and generates an output folder, located in the `bin/Debug` directory. Within this directory, based on your .NET version, you will find a subdirectory named `net6.0`, `net8.0`, or similar. 

#### Step 9: Run Your Application Directly

Navigate (`cd`) to this subdirectory (`bin/Debug/net6.0`), and you can run the built executable file named `HelloWorld.exe` with the following command:

```bash
HelloWorld.exe
```

You should see your custom "Hello World" message displayed in the console just as before. We'll tend to prefer the dotnet cli in this book, but it's good to know what's going on under the hood. Next, we'll try to build the project using the traditional Synergy DBL build tools.

#### Step 10: Get Traditional DBL tools in your PATH

Depending on if you want to build/run 32bit or 64bit applications, you'll need to add the appropriate Synergy DBL runtime to your PATH. For example, if you want to build/run 32bit applications, you'll want to run `"%SYNERGYDE32%\dbl\dblvars32.bat"` in your command prompt. If you want to build/run 64bit applications, you'll need to run `"%SYNERGYDE64%\dbl\dblvars64.bat"` in your command prompt.

#### Step 11: Build Your Application Manually for Traditional DBL

Its possible to build Traditional DBL applications using MSBuild, it looks very similar to what we've done above. But to show you the nitty gritty details under the hood we'll to build the source from the original project using the traditional Synergy DBL build tools. From the command line in your HelloWorld directory, run:

```bash
dbl HelloWorld.dbl
dblink HelloWorld.dbo
```

#### Step 12: Run Your Application with the Traditional DBL Runtime
You can run the `.dbr` file on windows in a semi-gui fashion by executing:

```bash
dbr HelloWorld.dbr
```

or directly in the console by executing:

```bash
dbs HelloWorld.dbr
```

Alright, thats enough ways to build and run DBL applications. Lets move on to a slightly bigger example.
# Hello World
##Creating a Project Directory

You’ll start by making a directory to store your DBL code. It doesn’t matter to DBL where your code lives, but for the exercises and projects in this book, we suggest making a projects directory in your home directory and keeping all your projects there.

Open a terminal and enter the following commands to make a projects directory and a directory for the “Hello, world!” project within the projects directory.

```bash
mkdir "%USERPROFILE%\projects"
cd /d "%USERPROFILE%\projects"
mkdir HelloWorld
cd HelloWorld
```

#### Get Traditional DBL tools in your PATH

Depending on if you want to build/run 32bit or 64bit applications, you'll need to add the appropriate Synergy DBL runtime to your PATH. For example, if you want to build/run 32bit applications, you'll want to run `"%SYNERGYDE32%\dbl\dblvars32.bat"` in your command prompt. If you want to build/run 64bit applications, you'll need to run `"%SYNERGYDE64%\dbl\dblvars64.bat"` in your command prompt.

#### Writing the Program

Next, make a new source file and call it main.dbl. DBL files don't need to end with `.dbl` and your company may have a different standard file extension, but I find it much easier to just always use `.dbl`. If you’re using more than one word in your filename, the convention in this book will be to use capital letters to separate them. For example, use HelloWorld.dbl rather than helloworld.dbl.

Now open the main.dbl file you just created and enter the code below.

```dbl
proc
    Console.WriteLine("Hello World")
```

#### Anatomy of a DBL Program

Let’s take a closer look at the code you entered. The first line of the code, `proc`, is an implicit main declaration. It tells DBL that you’re defining a main routine, which is your entry point and a collection of statements that performs a task. The second line, `Console.WriteLine("Hello World")`, is an expression that prints the string Hello World to the screen. The `Console` part of the expression indicates that you want to use the `WriteLine` method defined in the `Console` class. You’ll learn more about classes, methods, and how to define your own methods later in this book. In the later examples we'll sometimes use implicit main, and sometimes fully declare it. For now this is shorter, your codebase likely has it both ways and you can use either. 

#### Compiling, Linking, and Running the Program are separate steps
Before running a DBL program, you must compile and link it using the DBL compiler and linker. First up we'll compile by entering the `dbl` command and passing it the name of your source file, like this:

```bash
dbl HelloWorld.dbl
```

#### Link Your Application for Traditional DBL
The compile step above will produce an `.obj` file. This is an object file that contains the compiled code for your application. You'll need to link this object file produce a runnable `.dbr` file. You can do this by executing:

```bash
dblink HelloWorld
```

#### Run Your Application with the Traditional DBL Runtime
You can run the `.dbr` file on windows in a semi-gui fashion by executing:

```bash
dbr HelloWorld.dbr
```

or directly in the console by executing:

```bash
dbs HelloWorld.dbr
```

Alright, thats enough manual building and running DBL applications. Lets move up to a build system with MSBuild.
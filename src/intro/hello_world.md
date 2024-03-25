# Hello World

`**Might want to say something brief about how they're now ready to write DBL code, create a program, etc. (E.g., "Now that you've installed Visual Studio, Synergy/DE, and SDI, you're ready to create your first program in DBL" or something like the intro in hello_msbuild.md.) This will give the next sentence ("You'll start by...") and the section the context it needs.`

## Creating a project directory

`**Should these headings start with "Step 1:" etc.? We use this type of heading in the installation section and hello_msbuild, but not here and not in the guessing game section.`

You’ll start by making a directory to store your DBL code. It doesn’t matter where your code lives, but for the exercises and projects in this book, we suggest making a "projects" directory in your home directory and keeping all your projects there.

Open a terminal, move to your home directory, and enter the following commands to make a "projects" directory and a directory within that for the project you'll create for your first DBL program, “Hello, world!”

```bash
mkdir "%USERPROFILE%\projects"
cd /d "%USERPROFILE%\projects"
mkdir HelloWorld
cd HelloWorld
```

## Add Traditional DBL tools to PATH

Whether you want to build and run 32-bit applications or 64-bit applications, you'll need to run one of the following commands  at a Windows command prompt to add the appropriate Synergy DBL runtime to your PATH. If you want to build and run 32-bit applications, run `"%SYNERGYDE32%\dbl\dblvars32.bat"`. If you want to build and run 64-bit applications, run `"%SYNERGYDE64%\dbl\dblvars64.bat"`.

## Write the "Hello World" program

Next, make a new source file and call it HelloWorld.dbl. DBL files don't need to end with `.dbl`, and your company may have a different standard file extension, but it's generally easier just to use `.dbl`. If your filename contains more than one word, the convention in this book will be to begin each word with a capital letter (i.e., PascalCase). For example, use HelloWorld.dbl rather than helloworld.dbl for the source file you are creating here.

Now open the HelloWorld.dbl file you just created and enter the following code:

```dbl
proc
    Console.WriteLine("Hello World")
```

`**Should we explain why Console.Writeline() works in this context (i.e., that DBL has its own implementation of System.Console)?`

## Anatomy of a DBL program

Let’s take a closer look at the code you just entered. Because the first line of the code is `proc`, this line serves as an implicit main declaration. It tells DBL that you’re defining a main routine, which is the program's entry point, as well as a collection of statements that performs a task. The second line, `Console.WriteLine("Hello World")`, is an expression that prints the string "Hello World" to the screen. The `Console` part of the expression indicates that you want to use the `WriteLine` method defined in the `Console` class. 

You’ll learn more about classes, methods, and how to define your own methods later in this book. In later examples we'll sometimes use implicit main, as we did here, and we'll sometimes explicitly declare "main". For now, an implicit declaration is shorter. Your codebase likely has it both ways, and you can use either. 

## Compile the program
Before running a DBL program, you must compile and link it using the DBL compiler and linker. First, we'll compile by entering the `dbl` command and passing it the name of your source file, like this:

```bash
dbl HelloWorld.dbl
```

`**Seems that here we might want to point out that "dbl" runs the Traditional DBL compiler. We haven't mentioned up front that we'll be creating a Traditional DBL program, so this section has to also introduce that, or we could mention this earlier in this file.`

## Link your application for Traditional DBL
The compile step above will produce an `.obj` file. This is an object file that contains the compiled code for your application. You'll need to link this object file to produce a runnable `.dbr` file. You can do this by executing

```bash
dblink HelloWorld
```
`**Is this the place to mention that Traditional DBL files need to be linked, but .NET files aren't linked because the CLR takes care of linking tasks (or something like that)? Also, should this explain why HelloWorld doesn't need an extension in this command?`

## Run your application with the Traditional DBL runtime
You can run the `.dbr` file on windows in a semi-GUI fashion by executing this:

```bash
dbr HelloWorld.dbr
```

Or run it directly in the console by executing this:

```bash
dbs HelloWorld.dbr
```

In either case, you should see the message "Hello World" as well as the following, which indicates that the program completed without error:

>```
>%DBR-S-STPMSG, STOP
>%DBR-I-ATLINE, At line 2 in routine HELLOWORLD (HelloWorld.dbl)
>```

That's enough building and running DBL applications manually. Let's move on to building a system with MSBuild.
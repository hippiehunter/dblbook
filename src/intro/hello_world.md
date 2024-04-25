# Hello World

<!--Might want to say something brief about how they're now ready to write DBL code, create a program, etc. (E.g., "Now that you've installed Visual Studio, Synergy/DE, and SDI, you're ready to create your first program in DBL" or something like the intro in hello_msbuild.md.) This will give the next sentence ("You'll start by...") and the section the context it needs.-->

## Creating a project directory

Start by making a directory to store your DBL code. It doesn’t matter where your code resides, but for the exercises and projects in this book, we suggest making a "projects" directory in your home directory and keeping all your projects there.

Open a terminal, move to your home directory, and enter the following commands to make a "projects" directory and a directory within that for the project you'll create for your first DBL program, “Hello World!”

```console
mkdir "%USERPROFILE%\projects"
cd /d "%USERPROFILE%\projects"
mkdir HelloWorld
cd HelloWorld
```

## Add Traditional DBL tools to PATH
Whether you want to build and run 32-bit applications or 64-bit applications, you'll need to run one of the following commands at a Windows command prompt to add the appropriate Synergy DBL runtime to your PATH. If you want to build and run 32-bit applications, run `"%SYNERGYDE32%\dbl\dblvars32.bat"`. If you want to build and run 64-bit applications, run `"%SYNERGYDE64%\dbl\dblvars64.bat"`.

## Write the "Hello World" program

Next, make a new source file and call it HelloWorld.dbl. DBL files don't need to end with `.dbl`, and your company may have a different standard file extension, but it's generally easier just to use `.dbl`. If your filename contains more than one word, the convention in this book will be to begin each word with a capital letter (i.e., PascalCase). For example, use HelloWorld.dbl rather than helloworld.dbl for the source file you are creating here.

Now open the HelloWorld.dbl file you just created and add the following code:

```dbl
proc
    Console.WriteLine("Hello World")
```

## Anatomy of a DBL program

Let’s take a closer look at the code you just entered. Because the first line of the code is `proc`, this line serves as an implicit `main` declaration. It tells DBL that you’re defining a `main` routine, which is the program's entry point and a collection of statements that perform tasks. The second line, `Console.WriteLine("Hello World")`, is an expression that prints the string "Hello World" to the screen. The `Console.WriteLine` part of the expression indicates that you want to use the `WriteLine` method defined in the `Console` class. 

You’ll learn more about classes, methods, and how to define your own methods later in this book. In later examples we'll sometimes use an implicit `main`, as we did here, and we'll sometimes explicitly declare `main`. For now, an implicit declaration is more convenient. Your codebase likely has it both ways, and you can use either. 

## Compile the program
Before running a DBL program, you must compile and link it using the DBL compiler and linker. First, we'll compile by entering the `dbl` command and passing it the name of your source file, like this:

```console
dbl HelloWorld.dbl
```

>If you get a message indicating that “dbl isn’t recognized," run dblbvars64, as instructed in [Add Traditional DBL tools to PATH](#add-traditional-dbl-tools-to-path) (above), and then run `dbl HelloWorld.dbl`.

## Link your application for Traditional DBL
The compile step above will produce an `.obj` file. This is an object file that contains the compiled code for your application. You'll need to link this object file to produce a runnable `.dbr` file. You can do this by executing

```console
dblink HelloWorld
```

## Run your application with the Traditional DBL runtime
You can run the `.dbr` file on Windows in a semi-GUI fashion by executing this:

```console
dbr HelloWorld.dbr
```

Or run it directly in the console by executing this:

```console
dbs HelloWorld.dbr
```

In either case, you should see the message "Hello World" as well as the following, which indicates that the program completed without error:

>```
>%DBR-S-STPMSG, STOP
>%DBR-I-ATLINE, At line 2 in routine HELLOWORLD (HelloWorld.dbl)
>```

When you click the OK button in the pop-up window that opens at this point, the message window and the program close.

That's enough manual building and running of DBL applications. Let's move on to building a system with MSBuild.
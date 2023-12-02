# Programming a Guessing Game

Let’s jump into DBL by working through a hands-on project together! This
chapter introduces you to a few common concepts by showing you how to use
them in a real program. You’ll learn about variables, main functions, terminal I/O, and more! In the following chapters, we’ll explore
these ideas in more detail. In this chapter, you’ll just practice the
fundamentals.

We’ll implement a classic beginner programming problem: a guessing game. Here’s
how it works: the program will generate a random integer between 1 and 100. It
will then prompt the player to enter a guess. After a guess is entered, the
program will indicate whether the guess is too low or too high. If the guess is
correct, the game will print a congratulatory message and exit.

## Setting Up a New Project

To set up a new .NET project, go to the *projects* directory that you created in
Chapter 1 and make a new project using dotnet, like so:

```console
$ dotnet new SynNetApp -n GuessingGame
$ cd GuessingGame
```

The first command, `dotnet new`, takes the template name (`SynNetApp`) as the first argument and takes the name of the project (`GuessingGame`) as the second argument. The second command changes to the new project’s directory.

As you saw in the intro, `dotnet new` generates a "Hello world" program for
you. Check out the *Program.dbl* file:

```dbl
import System

main
proc
    Console.WriteLine("Hello World")
endmain
```

Now let’s compile this “Hello World” program and run it in the same step
using the `dotnet run` command:

```console
Hello World
```

The `run` command comes in handy when you need to rapidly iterate on a project,
as we’ll do in this game, quickly testing each iteration before moving on to
the next one.

Reopen the *Program.dbl* file. You’ll be writing all the code in this file.

## Processing a Guess

The first part of the guessing game program will ask for user input, process
that input, and check that the input is in the expected form. To start, we’ll
allow the player to input a guess. Replace the contents of *Program.dbl* with the following:
    
```dbl
import System

main
proc
    Console.WriteLine("Guess the number!")

    Console.WriteLine("Please input your guess.")

    data guess = Console.ReadLine()

    Console.WriteLine("You guessed: " + guess)
endmain
```
Let's break down each part of the code:

### Code Breakdown

```dbl
import System
```

`import System` tells the compiler to make the contents of the `System` namespace implicitly available in this source file. For our use case `System` provides access to fundamental classes for managing input and output (I/O), basic data types, and other essential services. This is necessary to use the `Console` class in the program.

```dbl
main
proc
```

`main` indicates the starting point of the program. In DBL, `main` is a special keyword used to define the entry point of the application. As you've seen in the hello world example, you can skip the `main` keyword and just start with a `proc`. In this example we're using a fully declared main. `proc` signals the transition from the data division to the procedure division. The procedure division contains the statements that perform the tasks of the program.


```dbl
Console.WriteLine("Guess the number!")
```

`Console.WriteLine("Guess the number!")` outputs the string "Guess the number!" to the console. `Console.WriteLine` is a method from the `Console` class that writes a line of text to the standard output stream (in this case, the console).

```dbl
Console.WriteLine("Please input your guess.")
```

Similar to the previous line, this outputs "Please input your guess." to the console. It's a prompt for the user to enter their guess.

##### Storing values with variables

```dbl
data guess = Console.ReadLine()
```

`data guess` declares a variable named `guess`. It has not specified the type of the variable, so it will be inferred from the value assigned to it. In this case it will be a `String`. The `=` followed by a call to `Console.ReadLine()` reads the next line of characters from the standard input stream (the console input in this case) then stores the result in the variable `guess`.

```dbl
Console.WriteLine("You guessed: " + guess)
```

This line outputs a concatenated string to the console. It combines "You guessed: " with the value stored in `guess`, displaying the user's input back to them.

```dbl
endmain
```

The `endmain` is optional and it marks the end of the `main` procedure.

### Testing the First Part

Let’s test the first part of the guessing game. Run it using `dotnet run`:

```console
dotnet run
Guess the number!
Please input your guess.
5
You guessed: 5
```

At this point, the first part of the game is done: we’re getting input from the keyboard and then printing it.
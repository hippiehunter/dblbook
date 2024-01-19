# Programming a Guessing Game

Let’s jump into DBL by working through a hands-on project together! This
chapter introduces a few common concepts by showing you how to use
them in a real program. You’ll learn about variables, main functions, terminal
I/O, and more. In subsequent chapters, we’ll explore these ideas in more 
detail. In this chapter, you’ll practice the fundamentals by implementing a 
classic beginner programming project: a guessing game. Here’s
how it works: The program will generate a random integer between 1 and 100. It
will then prompt the player to enter a guess. When an incorrect guess is entered, the
program will indicate whether the guess is too low or too high. When the guess is
correct, the game will print a congratulatory message and exit.

## Setting up a new project

To set up a new .NET project, go to the *projects* directory that you created in
Chapter 1 and make a new project using dotnet, like so:

`** Why is the following ```console, rather than ```bash?`
```console
dotnet new SynNETApp -n GuessingGame
cd GuessingGame
```

The first command, `dotnet new`, takes the template name (`SynNETApp`) as the first argument and takes the name of the project (`GuessingGame`) as the second argument. The second command changes to the new project’s directory.

As you saw in the intro, `dotnet new SynNETApp` generates a "Hello world" program for
you. Take a look at the *Program.dbl*`**Why italicized? Should this be the style?` file:

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
dotnet run Program.dbl
```

You should see the message "Hello World" below the `run` command.

The `run` command comes in handy when you need to rapidly iterate on a project
(as we’ll do in this game), quickly testing each iteration before moving on to
the next one.

Open the *Program.dbl* file. You’ll be writing all the code in this file.

## Processing a guess

The first part of the guessing game program will ask for user input, process
that input, and check that the input is in the expected form. To start, we’ll
allow the player to input a guess. Replace the contents of *Program.dbl* with the following:
    
```dbl
import System

main
proc
    stty(0) ; Enable .NET console input
    Console.WriteLine("Guess the number!")

    Console.WriteLine("Please input your guess.")

    data guess = Console.ReadLine()

    Console.WriteLine("You guessed: " + guess)
endmain
```

### Code breakdown

Let's break down each part of the code, starting with the first line:

```dbl
import System
```

This first line tells the compiler to make the contents of the `System` namespace implicitly available in this source file`**does this import our System namespace, .NET's, or both?`. For our use case, `System` provides access to fundamental classes for managing input and output (I/O), basic data types, and other essential services. This is necessary to use the `Console` class in the program.

The next two lines are

```dbl
main
proc
```

In the first of these lines, `main` indicates the starting point of the program. In DBL, `main` is a special keyword used to define the entry point of the application. As you've seen in the "Hello World" example, you can skip the `main` keyword and just start with a `proc`. In this example, we're using a fully declared main`**Why? Any reason?`. The next line, `proc`, signals the transition from the data division to the procedure division of the main routine. The procedure division contains the statements that perform the tasks of the program.

The first line under `proc` is this:

```dbl
stty(0) ; Enable .NET console input
```

DBL has multiple ways to read input from the console. The `stty(0)` statement is used to enable the .NET console input. The `0` setting for `stty` is necessary to use the `Console.ReadLine()` method in the program and it's mutually exclusive with other console input methods`**e.g., the DBL ACCEPT or READS statements?`.


The next line outputs the string "Guess the number!" to the console:

```dbl
Console.WriteLine("Guess the number!")
```

`Console.WriteLine` is a method from the .NET `Console` class that writes a line of text to the standard output stream (in this case, the console).

Similar to the previous line, this line at the end of the procedure section outputs the text to prompt the user for a guess:

```dbl
Console.WriteLine("Please input your guess.")
```

##### Storing values with variables

The next line declares a variable named `guess`.

```dbl
data guess = Console.ReadLine()
```

This statement does not specified the type of the variable, so the type will be determined based on the value assigned to it. In this case it will be a `String`. The `=` followed by a call to `Console.ReadLine()` reads the next line of characters from the standard input stream (the console input in this case), and then stores the result in the variable `guess`.

Then, the next line outputs a concatenated string to the console:

```dbl
Console.WriteLine("You guessed: " + guess)
```

This combines "You guessed: " with the value stored in `guess`, displaying the user's input back to them on the console.

Finally, the `endmain` is optional and it marks the end of the `main` procedure:

```dbl
endmain
```



### Testing the first part

Let’s test the first part of the guessing game. Run it using `dotnet run`:

```console
dotnet run
Guess the number!
Please input your guess.
5
You guessed: 5
```

At this point, the first part of the game is done: we’re getting input from the keyboard and then printing it.

## Generating a secret number

Next, we need to generate a secret number that the user will try to guess. The secret number should be different every time so the game is fun to play more than once. We’ll use a random number between 1 and 100 so the game isn’t too difficult. DBL has a built in random number facility, `RANDM` but it's not a super ergonomic`**word choice` function so we're going to use the Random class from the `System` namespace in .NET. Let's start using Random to generate a random number between 1 and 100. 

Replace the contents of *Program.dbl* with the following:

```dbl
import System

main
proc
    stty(0) ; Enable .NET console input
    Console.WriteLine("Guess the number!")

    data random = new Random()
    data randomNumber = random.Next(1, 101) ; Generates a random number between 1 and 100

    Console.WriteLine("Please input your guess.")

    data guess = Console.ReadLine()

    Console.WriteLine("You guessed: " + guess)
    Console.WriteLine("The secret number was " + %string(randomNumber))
endmain
```

Notice that we've added a variable named `random` and assigned it a new instance of the Random class, which provides a convenient way to generate random numbers. We've also added a variable named `randomNumber` and assigned it the result of calling the Next method on the `random` variable. The Next method takes two arguments: the first is the inclusive lower bound of the random number, and the second is the exclusive upper bound. In this case, we're passing 1 and 101, so the random number will be between 1 and 100. Finally, we've added a line to convert our secret number to be a string and then output it to the console.

Try running the program a few times:

```console
dotnet run
Guess the number!
Please input your guess.
You guessed: 50
The secret number was 37
```

You should get different random numbers, and they should all be numbers between 1 and 100. Great job!

## Comparing the guess to the secret number

Now that we have user input and a random number, we can compare them.

```dbl
import System

main
proc
    stty(0)
    Console.WriteLine("Guess the number!")

    data random = new Random()
    data randomNumber = random.Next(1, 101) ; Generates a random number between 1 and 100

    Console.WriteLine("Please input your guess.")

    data guess = Console.ReadLine()

    data guessNumber, int, %integer(guess)
    if(guessNumber > randomNumber) then
        Console.WriteLine("Too big!")
    else if(guessNumber < randomNumber) then
        Console.WriteLine("Too small!")
    else
        Console.WriteLine("Correct!")
endmain
```

First up, we're calling `integer` and passing it the string we read off the console in order to convert it from a `String` into an `int`. In that same line, we've added a new variable `guessNumber`, declared that its an int and assigned its initial value. Next up we have a block of if/else if/else statements. The `if` statement checks if the `guessNumber` is greater than the `randomNumber`. If it is, it prints "Too big!". The `else if` statement checks if the `guessNumber` is less than the `randomNumber`. If it is, it prints "Too small!". Finally, the `else` statement is a catch-all that prints "Correct!" if the `guessNumber` is neither greater than nor less than the `randomNumber`.  If we hadnt converted the `guess` to an `int` the compiler wouldn't allow us to compare with the `randomNumber` because they would be different types.

If you run the program now you'll see something like the following:

```console
dotnet run
Guess the number!
Please input your guess.
55
Too small!
```

You might be tempted to try inputting something other than a number to see what happens. Go ahead and try it. You'll see something like the following:

```console
dotnet run
Guess the number!
Please input your guess.
dd
Unhandled exception. Synergex.SynergyDE.BadDigitException: Bad digit encountered
   at Synergex.SynergyDE.VariantDesc.ToLong()
   at Synergex.SynergyDE.SysRoutines.f_integer(VariantDesc parm1, NumericParam parm2)
   at _NS_GuessingGame._CL.MAIN$PROGRAM(String[] args)
```

Not very user friendly! Lets add some error handling to make this a better experience for the user. Replace the contents of *Program.dbl* with the following:

```dbl
import System

main
proc
    stty(0)
    Console.WriteLine("Guess the number!")

    data random = new Random()
    data randomNumber = random.Next(1, 101) ; Generates a random number between 1 and 100

    Console.WriteLine("Please input your guess.")

    data guess = Console.ReadLine()

    try
    begin
        data guessNumber, i4, %integer(guess)
        if(guessNumber > randomNumber) then
            Console.WriteLine("Too big!")
        else if(guessNumber < randomNumber) then
            Console.WriteLine("Too small!")
        else
            Console.WriteLine("Correct!")
    end
    catch(ex, @Synergex.SynergyDE.BadDigitException)
    begin
        Console.WriteLine("Please type a number!")
    end
    endtry
endmain
```

We've added a `try` block around the code that converts the `guess` to an `int` and compares it to the `randomNumber`. We've also added a `catch` block that catches the `BadDigitException` and prints a more user friendly message. We could have also used `Int.TryParse` from .NET but this way you can see some explicit error handling. Now if you run the program and enter something other than a number you'll see something like the following:

```console
dotnet run
Guess the number!
Please input your guess.
dd
Please type a number!
```

## Allowing multiple guesses with looping

Now that we have the basic game working, we can make it more interesting by allowing multiple guesses. To do this, we’ll use a repeat loop. The repeat loop continues until `exitloop` is executed. Replace the contents of *Program.dbl* with the following:

```dbl
import System

main
proc
    stty(0)
    Console.WriteLine("Guess the number!")

    data random = new Random()
    data randomNumber = random.Next(1, 101) ; Generates a random number between 1 and 100

    repeat
    begin
        Console.WriteLine("Please input your guess.")

        data guess = Console.ReadLine()

        try
        begin
            data guessNumber, i4, %integer(guess)
            if(guessNumber > randomNumber) then
                Console.WriteLine("Too big!")
            else if(guessNumber < randomNumber) then
                Console.WriteLine("Too small!")
            else
            begin
                Console.WriteLine("Correct!")
                exitloop
            end
        end
        catch(ex, @Synergex.SynergyDE.BadDigitException)
        begin
            Console.WriteLine("Please type a number!")
        end
        endtry
    end
endmain
```

We've added a `repeat` block around the entire chunk of code that gets the user's guess and compares it to the `randomNumber`. We've also made the `else` block into a compound statement so we can execute both the `Console.WriteLine` and the `exitloop`. Now if you run the program you'll see something like the following:

```console
dotnet run
Guess the number!
Please input your guess.
55
Too big!
Please input your guess.
25
Too big!
Please input your guess.
13
Too big!
Please input your guess.
6
Too big!
Please input your guess.
3
Correct!
```

And there you have it! A fully functioning guessing game. You can play it as many times as you want and it will generate a new random number each time. You can also try to guess the number as many times as you want.

## Summary
This project was a hands on way to introduce you to some of the fundamentals of DBL. You learned about variables, looping, error handling and conditional statements. In the next chapter we'll dive deeper into common programming concepts.
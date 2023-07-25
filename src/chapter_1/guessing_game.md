# Programming a Guessing Game

Let’s jump into DBL by working through a hands-on project together! This
chapter introduces you to a few common Rust concepts by showing you how to use
them in a real program. You’ll learn about TODO, methods, associated
functions, and more! In the following chapters, we’ll explore
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
$ dotnet new synergy_console -n guessing_game
$ cd guessing_game
```

The first command, `dotnet new`, takes the template name (`synergy_console`) as the first argument and takes the name of the project (`guessing_game`) as the second argument. The second command changes to the new project’s directory.
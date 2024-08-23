# Banking on Basics: Exploring OOP with a Simple Bank Simulation
In this chapter, we'll use the object-oriented features of DBL to build a simple bank simulation. We're going to model fundamental banking operations, focusing on accounts and transfers, and interact with users through a straightforward command line interface. We aren't going to cover file I/O in this chapter, so we'll be using a simple in-memory collection to hold our data. This will allow us to focus on the object-oriented features of DBL without getting bogged down in the details of file I/O. Let's get started by creating a new project. Although this is going to be a .NET project, we won't use anything that isn't available in a DBL project targeting the Traditional DBL runtime. 

Let's get started by creating a new solution and a .NET console app using `dotnet new`:

```console
mkdir BankExample
cd BankExample
dotnet new sln
dotnet new synNETApp -n BankApp
dotnet sln add BankApp\BankApp.synproj
```

We're also going to make use of the Repository to define things like account data and transfer records. So let's create a repository project, add it to our solution, and add a reference to it from our console app:

```console
dotnet new synRepoProj -n Repository
dotnet sln add Repository\Repository.synproj
dotnet add BankApp\BankApp.synproj reference Repository\Repository.synproj
```

Now that we have out projects created and hooked up, let's dive into designing our accounts in the next section.

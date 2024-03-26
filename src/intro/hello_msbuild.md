# Hello MSBuild

In this section, we'll walk through the steps to create a new "Hello World" project using the .NET command-line interface (CLI) and a custom template named "SynNetApp". The .NET CLI is a powerful tool that allows you to create, build, and run .NET projects from the command line. Don't worry if your company isn't currently targeting .NET. The important parts of this walkthrough show how projects can be built and what sort of artifacts you can expect as output.

## Step 1: Open your command-line interface

First, open a Windows Command Prompt or a Terminal window. You'll use this to run the .NET CLI commands.

## Step 2: Install the SynNetApp template

Before creating a project, you need to ensure that the Synergex.Projects.Templates package is installed on your system. If you haven't installed it yet, you can do so by running the following command:

```bash
dotnet new install Synergex.Projects.Templates
```

This command will download and install all the DBL project templates you might want to use.

`**Maybe something like the following? "If you get an error such as “Synergex.Projects.Templates could not be installed, the package does not exist”, the problem could be that the NuGet feed (https://api.nuget.org/v3/index.json) is not set up as a package source on your system. See Microsoft documentation on package sources for information on adding this package source."`

## Step 3: Create a new project

Now, you're ready to create a new project. Navigate to the directory where you want to create your project and run

```bash
dotnet new synNETApp -n HelloMSBuild
```

`SynNetApp` is the name of the template used to create a DBL console application for .NET, and `-n HelloMSBuild` specifies the name of your new project. You can replace `HelloMSBuild` with whatever project name you prefer.

`**Should the term "project" be defined here or in some earlier section? Or can we assume readers (all kinds) will understand what projects are. `

## Step 4: Navigate to your project directory

Once the project is created, navigate to the project directory:

```bash
cd HelloMSBuild
```

Replace `HelloMSBuild` with your project name if you chose a different one.

## Step 5: Explore the project structure

Take a moment to explore the newly created project structure. You should see two files:
- `Program.dbl` contains the entry point of the application. This is where the application starts executing.
- `HelloMSBuild.synproj` contains the project definition in an MSBuild-compatible XML format. This file defines the project's name, source files, options, and dependencies.

Let's dig into the created HelloMSBuild.synproj file by opening it in a text editor. It should look something like this:

```xml
<Project Sdk="Microsoft.NET.Sdk" DefaultTargets="restore;Build">
	<PropertyGroup>
		<OutputType>Exe</OutputType>
    		<TargetFramework Condition="'$(TargetFrameworkOverride)' == ''">net6.0</TargetFramework>
    		<TargetFramework Condition="'$(TargetFrameworkOverride)' != ''">$(TargetFrameworkOverride)</TargetFramework>
		<DefaultLanguageSourceExtension>.dbl</DefaultLanguageSourceExtension>
    		<EnableDefaultItems>false</EnableDefaultItems>
  	</PropertyGroup>

	<ItemGroup>
		<Compile Include="Program.dbl" />
  	</ItemGroup>

	<ItemGroup>
    		<PackageReference Include="Synergex.SynergyDE.Build" Version="23.*" />
    		<PackageReference Include="Synergex.SynergyDE.synrnt" Version="12.*" />
	</ItemGroup>

</Project>
```

## Root element and SDK reference

Take a look at the first line, which includes the root element and specifies an SDK and default target:

```xml
<Project Sdk="Microsoft.NET.Sdk" DefaultTargets="restore;Build">
```

- `<Project>` is the root element of the MSBuild file, which contains configuration data for the project.
- `Sdk="Microsoft.NET.Sdk"` specifies that this project uses the .NET SDK. This SDK provides a set of standard targets, properties, and items.
- `DefaultTargets="restore;Build"` sets the default targets to be executed when MSBuild runs this file. In this case, it will first run the `restore` target (to restore NuGet packages) and then the `Build` target.

`**Should we define "targets" here?`
`**The phrase "when MSBuild runs this file" doesn't sound right to me. MSBuild uses this file to build the project, but is it correct to say it "runs" it?`

### PropertyGroup

```xml
<PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework Condition="'$(TargetFrameworkOverride)' == ''">net6.0</TargetFramework>
    <TargetFramework Condition="'$(TargetFrameworkOverride)' != ''">$(TargetFrameworkOverride)</TargetFramework>
    <DefaultLanguageSourceExtension>.dbl</DefaultLanguageSourceExtension>
    <EnableDefaultItems>false</EnableDefaultItems>
</PropertyGroup>
```

- `<PropertyGroup>`: Defines a group of properties for the project.
- `<OutputType>Exe</OutputType>`: Specifies the output type of the project, which in this case is an executable file.
- `<TargetFramework>`: Sets the target framework for the project. This project targets .NET 6.0 by default, but it can be overridden with a different framework version if `TargetFrameworkOverride` is specified.
- `<DefaultLanguageSourceExtension>.dbl</DefaultLanguageSourceExtension>`: Sets the default source file extension for the language used in this project, which is `.dbl`.
- `<EnableDefaultItems>false</EnableDefaultItems>`: Disables the default behavior of including certain files in the build based on conventions. This may change in the future, but currently there are issues with automatic includes for DBL files.

### ItemGroup for compile

```xml
<ItemGroup>
    <Compile Include="Program.dbl" />
</ItemGroup>
```

- `<ItemGroup>`: This element is used to define a group of items. Items represent inputs into the build system.
- `<Compile Include="Program.dbl" />`: This line includes `Program.dbl` in the compilation process. It's specifying that `Program.dbl` is a file to be compiled.

### ItemGroup for PackageReferences

```xml
<ItemGroup>
    <PackageReference Include="Synergex.SynergyDE.Build" Version="23.*" />
    <PackageReference Include="Synergex.SynergyDE.synrnt" Version="12.*" />
</ItemGroup>
```

- Another `<ItemGroup>` section, this time for NuGet package references.
- `<PackageReference Include="Synergex.SynergyDE.Build" Version="23.*" />`: This line adds a NuGet package reference to `Synergex.SynergyDE.Build`, a package that brings along the DBL compiler, with a version wildcard for major version 23.
- `<PackageReference Include="Synergex.SynergyDE.synrnt" Version="12.*" />`: Similarly, this adds a reference to `Synergex.SynergyDE.synrnt`, the runtime support package. The "23.*" notation indicates that the project should use the latest available version of the package with a major version number of 23. (The asterisk wildcard allows the project to use a minor or patch version under major version 23.)

## Step 6: Customize the Hello World message

Open `Program.dbl` in your favorite text editor. You'll see a line of code that looks something like this:

```dbl
import System

main
proc
    Console.WriteLine("Hello World")
endmain
```

Feel free to customize this message to something else, like this:

```dbl
Console.WriteLine("Hello from the DBL book!")
```

## Step 7: Run your application

Finally, it's time to run your application. Go back to your command line interface and execute:

```bash
dotnet run
```

This command will compile and execute your application. You should see your custom "Hello World" message displayed in the console.

Congratulations! You've successfully created and run a new .NET application using the synNETApp template. Let's scrape away `**"Let's set aside...` the very helpful dotnet CLI and try the build and run steps manually.

## Step 8: Build your application with MSBuild
So far you've seen dotnet CLI act as a wrapper around MSBuild. Now let's try to build the project using MSBuild directly. From the command line, run:

```bash
msbuild HelloMSBuild.synproj
```

This command will compile your application and produce an executable file named `HelloMSBuild.exe` under the `bin/Debug` directory. Depending on the version of .NET you're running, you should see a folder such as `net6.0` or `net8.0`. Once you navigate (`cd`) to that inner directory, you can run the executable directly, as we'll see in the next step.

## Step 9: Run your application directly

Once you've navigated (`cd`) to the inner directory created in the previous step (e.g., `bin/Debug/net6.0`), you can run the built executable file named `HelloMSBuild.exe` with the following command:

```bash
HelloMSBuild.exe
```

You should see your custom "Hello World" message displayed in the console just as before. We prefer using the dotnet CLI in this book, but it's good to know what's going on under the hood. Next, we'll move on to something a little more interesting than "Hello World" - a guessing game!

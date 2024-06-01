# Hello MSBuild

In this section, we'll walk through the steps to create a new "Hello World" project using the .NET command-line interface (CLI) and a custom template named "SynNetApp". The .NET CLI is a powerful tool that allows you to create, build, and run .NET projects from the command line. Don't worry if your company isn't currently targeting .NET. The important parts of this walkthrough show how projects can be built and what sort of artifacts you can expect as output.

## Open your command-line interface

First, open a Windows Command Prompt or a Terminal window. You'll use this to run the .NET CLI commands.

## Install Synergy project templates

Before creating a project, you need to ensure that the Synergex.Projects.Templates package is installed on your system. If you haven't installed it yet, you can do so by running the following command:

```console
dotnet new install Synergex.Projects.Templates
```

This command will download and install all the DBL project templates you might want to use, including SynNETApp. 

>If you get an error like “Synergex.Projects.Templates could not be installed, the package does not exist”, the problem could be that the NuGet feed (https://api.nuget.org/v3/index.json) is not set up as a package source on your system. See Microsoft documentation on package sources for information on adding this package source.

## Create a new project

Now, you're ready to create a new project. Navigate to the directory where you want to create your project and run

```console
dotnet new synNETApp -n HelloMSBuild
```

`SynNETApp` is the name of the template used to create a DBL console application for .NET, and `-n HelloMSBuild` specifies the name of your new project. You can replace `HelloMSBuild` with whatever project name you prefer.

## Navigate to your project directory

Once the project is created, navigate to the project directory:

```console
cd HelloMSBuild
```

Replace `HelloMSBuild` with your project name if you chose a different one.

## Explore the project structure

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

### Root element and SDK reference

Take a look at the first line, which contains the root element and specifies an SDK and default target:

```xml
<Project Sdk="Microsoft.NET.Sdk" DefaultTargets="restore;Build">
```

- `<Project>` is the root element of the MSBuild file. It contains configuration data for the project.
- `Sdk="Microsoft.NET.Sdk"` specifies that this project uses the .NET SDK. This SDK provides a set of standard targets, properties, and items.
- `DefaultTargets="restore;Build"` sets the default targets to be executed when MSBuild runs this file. In this case, it will first run the `restore` target (to restore NuGet packages) and then the `Build` target.

### PropertyGroup

The next element, `PropertyGroup`, defines a group of properties for the project: 

```xml
<PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework Condition="'$(TargetFrameworkOverride)' == ''">net6.0</TargetFramework>
    <TargetFramework Condition="'$(TargetFrameworkOverride)' != ''">$(TargetFrameworkOverride)</TargetFramework>
    <DefaultLanguageSourceExtension>.dbl</DefaultLanguageSourceExtension>
    <EnableDefaultItems>false</EnableDefaultItems>
</PropertyGroup>
```

- `<OutputType>Exe</OutputType>` specifies the output type of the project, which in this case is an executable file.
- `<TargetFramework>` sets the target framework for the project. This project targets .NET 6.0 by default, but it can be overridden with a different framework version if `TargetFrameworkOverride` is specified.
- `<DefaultLanguageSourceExtension>.dbl</DefaultLanguageSourceExtension>` sets the default source file extension for the language used in this project, which is `.dbl`.
- `<EnableDefaultItems>false</EnableDefaultItems>` disables the default behavior of including certain files in the build based on conventions. This may change in the future, but currently there are issues with automatic includes for DBL files.

### ItemGroup for compile

The next element, `ItemGroup`, is used to define a group of items that represent inputs into the build system:

```xml
<ItemGroup>
    <Compile Include="Program.dbl" />
</ItemGroup>
```

The `<Compile Include="Program.dbl" />` line specifies that `Program.dbl` is to be compiled.

### ItemGroup for PackageReferences

The next `ItemGroup` section is for NuGet package references:

```xml
<ItemGroup>
    <PackageReference Include="Synergex.SynergyDE.Build" Version="23.*" />
    <PackageReference Include="Synergex.SynergyDE.synrnt" Version="12.*" />
</ItemGroup>
```

- The first `<PackageReference>` line adds a NuGet package reference for `Synergex.SynergyDE.Build`, a package that includes the DBL compiler. The "23.*" notation indicates that the project should use the latest available version of the package with a major version number of 23. The asterisk wildcard allows the project to use a minor or patch version under major version 23.
- Similarly, the next `<PackageReference>` line adds a reference to `Synergex.SynergyDE.synrnt`, the runtime support package, and includes a version wildcard for major version 12. 

## Customize the "Hello World" message

Open `Program.dbl` in your favorite text editor. You'll see a line of code that looks something like this:

```dbl
import System

main
proc
    Console.WriteLine("Hello World")
endmain
```

Customize the message by changing it to something like this:

```dbl
    Console.WriteLine("Hello from the DBL book!")
```

## Run your application

Finally, it's time to run your application. Go back to your command line interface and execute

```console
dotnet run
```

This command will compile and execute your application. You should see your custom message displayed in the console. Congratulations! You've successfully created and run a new .NET application using the synNETApp template. 

Now let's set aside the very helpful .NET CLI, and try the build and run steps manually.

## Build your application with MSBuild
So far you've seen dotnet CLI act as a wrapper around MSBuild. Now let's try to build the project using MSBuild directly. From the command line, run

```console
msbuild HelloMSBuild.synproj
```

This command will compile your application and produce an executable file named `HelloMSBuild.exe` under the `bin/Debug` directory. Depending on the version of .NET you are running, you should see a folder such as `net6.0` or `net8.0`. Once you navigate to that inner directory, you can run the executable directly, as we'll see in the next step.

## Run your application directly

Navigate to the inner directory created in the previous step (e.g., `bin/Debug/net6.0`). From here, run the built executable file with the following command:

```console
HelloMSBuild.exe
```

You should see your custom "Hello World" message displayed in the console just as before. We prefer using the .NET CLI in this book, but it's good to know what is going on under the hood. Next, we'll move on to something a little more interesting than "Hello World"—a guessing game!

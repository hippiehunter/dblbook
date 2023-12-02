# Hello MSBuild

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
dotnet new synNETApp -n HelloMSBuild
```

Here, `SynNetApp` is the template name, and `-n HelloMSBuild` specifies the name of your new project. You can replace `HelloMSBuild` with whatever project name you prefer.

#### Step 4: Navigate into Your Project Directory

Once the project is created, navigate into the project directory:

```bash
cd HelloMSBuild
```

Replace `HelloMSBuild` with your project name if you chose a different one.

#### Step 5: Explore the Project Structure

Take a moment to explore the newly created project structure. You should see two files:
- `Program.dbl`: contains the entry point of the application. This is where the application starts executing.
- `HelloMSBuild.synproj`: contains the project definition in an MSBuild-compatible XML format. This file defines the project's name, source files, options and dependencies.


Lets dig into the created HelloMSBuild.synproj file. It should look something like this:

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

### Root Element and SDK Reference

```xml
<Project Sdk="Microsoft.NET.Sdk" DefaultTargets="restore;Build">
```

- `<Project>`: This is the root element of the MSBuild file, which contains all the configuration data.
- `Sdk="Microsoft.NET.Sdk"`: Specifies that this project uses the .NET SDK. This SDK provides a set of standard targets, properties, and items.
- `DefaultTargets="restore;Build"`: This attribute sets the default targets to be executed when MSBuild runs this file. In this case, it will first run the `restore` target (to restore NuGet packages) and then the `Build` target.

##### PropertyGroup

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
- `<EnableDefaultItems>false</EnableDefaultItems>`: Disables the default behavior of including certain files in the build based on conventions. This may change in the future but currently there are issues with automatic includes for DBL files.

##### ItemGroup for Compile

```xml
<ItemGroup>
    <Compile Include="Program.dbl" />
</ItemGroup>
```

- `<ItemGroup>`: This element is used to define a group of items. Items represent inputs into the build system.
- `<Compile Include="Program.dbl" />`: This line includes `Program.dbl` in the compilation process. It's specifying that `Program.dbl` is a file to be compiled.

##### ItemGroup for PackageReferences

```xml
<ItemGroup>
    <PackageReference Include="Synergex.SynergyDE.Build" Version="23.*" />
    <PackageReference Include="Synergex.SynergyDE.synrnt" Version="12.*" />
</ItemGroup>
```

- Another `<ItemGroup>` section, this time for NuGet package references.
- `<PackageReference Include="Synergex.SynergyDE.Build" Version="23.*" />`: This line adds a NuGet package reference to `Synergex.SynergyDE.Build`, a package that brings along the DBL compiler, with a version wildcard for major version 23.
- `<PackageReference Include="Synergex.SynergyDE.synrnt" Version="12.*" />`: Similarly, this adds a reference to `Synergex.SynergyDE.synrnt`, the runtime support package, with a version wildcard for major version 12.

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
msbuild HelloMSBuild.synproj
```

This command will compile your application and produce an executable file named `HelloMSBuild.exe` under the `bin/Debug` directory. Depending on the version of .NET you're running you should see something like a `net6.0` or `net8.0` folder. Once you cd to that inner directory You can run the executable by executing:

This command compiles your application and generates an output folder, located in the `bin/Debug` directory. Within this directory, based on your .NET version, you will find a subdirectory named `net6.0`, `net8.0`, or similar. 

#### Step 9: Run Your Application Directly

Navigate (`cd`) to this subdirectory (`bin/Debug/net6.0`), and you can run the built executable file named `HelloMSBuild.exe` with the following command:

```bash
HelloMSBuild.exe
```

You should see your custom "Hello World" message displayed in the console just as before. We'll tend to prefer the dotnet cli in this book, but it's good to know what's going on under the hood. Next, we'll move on to something a little more interesting than "Hello World" - a guessing game!

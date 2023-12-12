# Projects
While scripted builds can offer a sense of straightforward control and can give the impression of quicker compile times for small changes, there are several compelling reasons why programmers should consider using structured build systems like MSBuild.

### Consistency and Standardization
**Structured Approach**: MSBuild provides a consistent and standardized approach to building projects. It ensures that every build follows the same steps and rules, reducing the chances of discrepancies that can often arise with custom scripts.

**Team Collaboration**: In a team environment, standardization is crucial. MSBuild allows different team members to work on the same project with a unified understanding of the build process, minimizing conflicts and confusion that can arise from individualized scripts.

### Complexity Management

**Handling Large Projects**: As projects grow in size and complexity, maintaining custom scripts for building can become increasingly cumbersome and error-prone. MSBuild is designed to handle complex dependency trees and project structures efficiently, something that's hard to maintain manually in scripts.

**Automated Dependency Tracking**: MSBuild automatically handles dependencies between files. This means that it intelligently recompiles only what is necessary, reducing the manual effort of tracking changed files, a process that is prone to human error. Going a step further, DBL actually checks the external signatures of your dependencies to prevent a small change to a core library turning into a full rebuild of your entire codebase.

### Integration with Tools and Ecosystems

**IDE Integration**: MSBuild is tightly integrated with Visual Studio and other development tools. This integration provides developers with seamless experiences, such as detailed build diagnostics, easy configuration management, and immediate feedback on build errors and warnings.

**Ecosystem Compatibility**: Using MSBuild ensures compatibility with a wide range of tools and plugins in the .NET ecosystem, including continuous integration systems.

### Advanced Features and Flexibility

**Customization and Extensibility**: While MSBuild provides a structured approach, it also offers extensive customization and extensibility options. Developers can define custom build steps, conditional builds, and integrate other tools as needed, all within a structured framework.

**Cross-Platform Builds**: Builds produced by MSBuild can be executed on Windows or Linux for both the Traditional runtime and the .NET runtime. However currently for DBL code your MSBuild still needs to be run on Windows.

### Maintainability and Future-Proofing

**Easier Maintenance**: A structured build system is generally easier to maintain and update. Changes in the build process or project structure can be implemented more systematically, without the need to rewrite scripts from scratch.

**Community and Support**: MSBuild, being a widely used and Microsoft-supported build system, benefits from a large community, regular updates, and professional support. This aspect ensures that the build system remains up-to-date with the latest technology trends and best practices.

#### Introduction to MSBuild's XML Format

MSBuild projects are defined using XML. At the heart of an MSBuild file, with a `.synproj` extension for DBL projects, are various elements that describe how to build a project. For DBL, you might have custom source file extensions, but the structure remains consistent with MSBuild standards.

### Older MSBuild Style Projects
Traditional DBL and DBL targeting the .NET Framework is currently built using the older MSBuild style projects.

**Verbose XML Schema**: Traditional MSBuild project files are known for their verbosity. They contain detailed specifications for every file in the project, along with numerous property and target definitions. This verbosity often made the project files large and cumbersome to edit and maintain.

**Package Management**: In older MSBuild projects, NuGet package references were typically managed in separate `packages.config` files. This approach required additional synchronization between the package configuration and the project file.

**Framework Targeting**: Targeting multiple frameworks required more manual setup. Developers had to carefully manage conditional statements within the project file to accommodate different frameworks, making the process error-prone and complex.

**Build Process Customization**: Customizing the build process involved manually editing the project file to include various MSBuild tasks and targets. This required a deep understanding of MSBuild's inner workings.

### SDK Style Projects

With the introduction of .NET Core, SDK-style projects became the standard. These projects are designed to be simpler, more concise, and easier to work with. When targeting .NET with DBL you'll be using SDK style projects.

**Simplified and Lean Structure**: SDK-style project files are much leaner and more readable. They use a simplified XML schema and often require only a minimal set of elements to work. Files are included implicitly, so there's no need to list each file individually.

**Integrated Package Management**: SDK-style projects integrate NuGet package references directly within the project file using the `PackageReference` node. This eliminates the need for `packages.config` and simplifies the management of dependencies.

**Multi-targeting Simplified**: SDK-style projects make it easier to target multiple frameworks. Developers can specify multiple target frameworks in a single property (`TargetFrameworks`), greatly simplifying the process.

**Cross-platform and Modern Tooling**: These projects are designed with cross-platform support in mind and are built to work seamlessly with modern tools like the .NET CLI. This makes them more adaptable to different environments and toolchains.

**Enhanced Project Sdk Attribute**: The `Sdk` attribute in the project file header specifies which SDK will be used (e.g., Microsoft.NET.Sdk for .NET Core projects). This attribute abstracts much of the complexity and allows the project file to focus on the specifics of the project itself.

Knowing that there is a difference between the project styles, we're going to try to explain the common elements that don't really change between the two styles.

#### Selecting the Output Type

The output type of a project is specified within the `<PropertyGroup>` element. For a DBL application, you might be building a console app or a library. This is specified using the `<OutputType>` tag. For example:

```xml
<PropertyGroup>
  <OutputType>Exe</OutputType>
  <!-- Other properties -->
</PropertyGroup>
```

This snippet sets the output type to an executable. The following table lists the various output types available for DBL projects:

```svgbob
+--------------------+--------------------------------------------------+
| OutputType         | Produces                                         |
+--------------------+--------------------------------------------------+
| Traditional DBL Output types                                          |
+--------------------+--------------------------------------------------+
| application        | A single .dbr application                        |
| olb                | A static library (.olb)                          |
| elb                | A dynamic library (.elb)                         |
| mainline           | Multiple .dbr applications, one per source file  |
+--------------------+--------------------------------------------------+
| DBL for .NET Output types                                             |
+--------------------+--------------------------------------------------+
| exe                | A single .NET executable                         |
| library            | A .NET DLL                                       |
+--------------------+--------------------------------------------------+
```

#### Referencing Other Projects

To reference other projects, such as libraries or dependencies, you use the `<ItemGroup>` element with `<ProjectReference>` tags. Each reference includes the path to the other project file:

```xml
<ItemGroup>
  <ProjectReference Include="..\Library\MyLibrary.synproj" />
  <!-- Additional project references -->
</ItemGroup>
```

This structure allows your DBL project to integrate and utilize functionalities from other projects within your solution.

#### Adding Source Files

Source files are included in the project through the `<ItemGroup>` element, using the `<Compile>` tag. For DBL, you would specify each source file (`.dbl`) you want to include:

```xml
<ItemGroup>
  <Compile Include="src\MyProgram.dbl" />
  <!-- Other source files -->
</ItemGroup>
```

This ensures that MSBuild recognizes and compiles all the necessary DBL source files.

#### Adding Include Files

Include files, which might contain shared code or definitions, are also added via the `<ItemGroup>` element. However, because you don't want to hand these to the DBL compiler as though they were source, you might use the `<None>` or `<Content>` tag:

```xml
<ItemGroup>
  <None Include="includes\MyIncludeFile.dbl" />
  <!-- Other include files -->
</ItemGroup>
```
This inclusion ensures that these files are part of the project and can be easily navigated and searched within Visual Studio but wont be treated as top level source files by the compiler.

### Managing access to the Synergy Repository


### Managing common build settings
There are a few ways to manage build settings that need to be common across multiple projects. The first is to use a Directory.Build.props file. This file can be placed in the root of your repository and will be automatically included in all projects within the repository. This is a good place to put settings that are common across all projects in the repository. For example, if you want to set the default target framework for all projects in the repository to .NET 6.0, you can add the following to the Directory.Build.props file:

```xml
<Project>
  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
  </PropertyGroup>
```

The second way to manage common build settings is to use `Common.props` files. The Visual Studio integration for DBL offers direct GUI access to managing build time environment variables. Because of the particulars of how environment variables are commonly used in DBL build systems, it's best to use a `Common.props` file to manage these settings. We aren't going to cover Visual Studio instructions here as there is a wealth of Youtube videos, articles and documentation. Knowing that you don't need to do this manually, you can use the following snippet to add a `Common.props` file to your project at the top of your `<Project` element:

```xml 
<Import Project="$(SolutionDir)Common.props" />
```

This will import the `Common.props` file from the root of your solution. You can then add the following to your `Common.props` file to set environment variables:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="15.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <CommonEnvVars>EXEDIR=$(ProjectDir)..\$(Configuration)\$(Platform)\;SOMEOTHER_ENVVAR=blablabla</CommonEnvVars>
  </PropertyGroup>
</Project>
```

This will set the `EXEDIR` and `SOMEOTHER_ENVVAR` environment variables for all projects in your solution. You can then use these environment variables in your build scripts. For example, you can use the `EXEDIR` environment variable to set the output directory for your build by adding/updating the following inside an active `<PropertyGroup>` element:

```xml
<UnevaluatedOutputPath>EXEDIR:</UnevaluatedOutputPath>
```

## Grouping projects into solutions
Using a solution file (.sln) in an MSBuild-based build system is required in order to effectively managing multiple projects. A .sln file is a text file that lists the projects that make up your solution, essentially serving as a project aggregator. It allows developers to organize, build, and manage a group of related projects as a single entity. This is particularly useful when your application consists of multiple components, such as a library, a user interface, and various service modules. Each project can be developed and maintained separately, with its own set of files, resources, and dependencies. The .sln file keeps track of these projects and their dependencies, ensuring that when you build the solution, MSBuild compiles the projects in the correct order. The flexibility of solution files (.sln) extends to allowing custom-named solution configurations, within which developers can selectively determine which projects to build and specify the particular project-level configurations, such as Debug or Release, for each, offering a tailored and granular control over the build process. This second level of configuration is powerful but it's very easy to confused if you're not careful with your naming conventions.

### Creating a Solution File
You likely already have a solution file for your project. If you don't, you can create one by using the `dotnet new sln` command. This command creates a new solution file with the same name as the current directory. You can also specify a name for the solution file by using the `-n` or `--name` option. For example, to create a solution file named `MySolution.sln`, you would use the following command:

```console
dotnet new sln -n MySolution
```

### Adding Projects to a Solution File
Once you have a solution file, you can add projects to it using the `dotnet sln add` command. This command adds the specified project to the solution file. In order to use `dotnet sln add` you will need to make sure that your project file has explicitly specified its project type. This is going to feel a little bit like boiler plate and it is, but doing things this way will ensure you know every part of your build system and will make it easier to maintain in the long run. You can check to see if you already have the required project type guid by opening your project file and looking for something like the following structure:

```xml
<Project>
...
<PropertyGroup>
...
<ProjectTypeGuids>{BBD0F5D1-1CC4-42FD-BA4C-A96779C64378}</ProjectTypeGuids>
</PropertyGroup>
...
```

Here's a list of the project type GUIDs and their meanings:

```svgbob
+----------------------------------------+----------------------------------------------+
| Project Type GUID                      | Meaning                                      |
+----------------------------------------+----------------------------------------------+
| {BBD0F5D1-1CC4-42FD-BA4C-A96779C64378} | DBL Base Project                             |
| {7B8CF543-378A-4EC1-BB1B-98E4DC6E6820} | Traditional DBL Flavor                       |
+----------------------------------------+----------------------------------------------+
```

If you have a Traditional DBL project you would combine the two project type GUIDs like this:
```xml
<ProjectTypeGuids>
{7B8CF543-378A-4EC1-BB1B-98E4DC6E6820};{BBD0F5D1-1CC4-42fd-BA4C-A96779C64378}
</ProjectTypeGuids>
```

Now that you have your project type GUIDs sorted out, in order to add a project named `MyProject` to the solution file, you would use the following command from the folder where the solution file is located:

```console
dotnet sln add path/to/MyProject.synproj
```

If you're missing the project type GUIDs, you'll get an error like this:

```console
dotnet sln add HelloWorld.synproj
Project 'D:\repos\HelloWorld\HelloWorld.synproj' has an unknown project type 
and cannot be added to the solution file. Contact your SDK provider for support.
```

Otherwise, you'll see a message like this:

```console
dotnet sln add HelloWorld.synproj
Project `HelloWorld.synproj` added to the solution.
```
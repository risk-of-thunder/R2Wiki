# Visual Studio Code

Caution: You may be more comfortable using VSCode, but most modders use Visual Studio Community for writing mods, and Visual Studio supports more .NET configurations than VSCode currently does. It's still possible to use VSCode, but requires some setup.

[Visual Studio Code](https://code.visualstudio.com/) (or VSCode) is a general-purpose open source IDE by Microsoft. If you are more comfortabe in VSCode and prefer to use it rather than learning a new IDE (Visual Studio, which most modders and developers use for C#/.NET projects), this page is a description on some of the differences between the two IDEs.

Reference:
- https://code.visualstudio.com/docs/languages/csharp
- https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code

# New Project

To match the convention that most mods use, we will create a top-level "solution" (using a `.sln` file) and then write our code in a source directory as a .NET `classlib`.

Create a new folder called `SamplePlugin` (can be done in VSCode or File Explorer).

Open this folder in VSCode, and open a Terminal. We will use the `dotnet` CLI to create the necessary solution and and cs/csproj files.

```
# Terminal

D:\Mods\SamplePlugin > dotnet new sln

D:\Mods\SamplePlugin > dotnet new classlib -o SamplePlugin

D:\Mods\SamplePlugin > dotnet sln add .\SamplePlugin\SamplePlugin.csproj
```

After running these commands, your directory should look like this:

```
SamplePlugin
├── SamplePlugin
│   ├── Class1.cs
│   ├── SamplePlugin.csproj
│   └── obj
└── SamplePlugin.sln
```

The top-level solution relates to build information for the "solution" (this project, or a group of projects) such as Debug/Release compiling or the architecture target. The `.csproj` file specifies information for a project such as its .NET version and its dependencies.

# Adding References

Visual Studio can add references through a file chooser dialog, but VSCode doesn't have a good extension that does this (as of this writing).

The csproj file can be manually edited to add the required references to BepInEx and Risk of Rain 2 DLLs. [Please take a look at this page for more details](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Assembly-References/)

# Building

## Building with VSCode

VSCode can auto-generate a "build" task for a .NET project that runs `dotnet build` or `dotnet msbuild`.

Go to `Terminal > Run Build Task`:
- click dialog "No build task found. Configure Build Task ..."
- "Create tasks.json" from template (VSCode will auto-generate a `tasks.json`)
- Select `.NET Core` since this project builds via `dotnet`.

You can see VSCode has filled out a tasks.json file that defines a build command that calls `dotnet build` with some additional arguments. This build task can now be run with `ctrl + shift + b` every time you want to build the project.

## Building Directly with `dotnet`

The project can be built from the command line by using the `dotnet` cli:

```
D:\Mods\SamplePlugin > dotnet restore

D:\Mods\SamplePlugin > dotnet msbuild
```

The build artifact will be be created under `SamplePlugin/SamplePlugin/bin/Debug/netstandard2.0/SamplePlugin.dll`.

When compiling for release, typically you will pass the Release parameter to msbuild:

```
D:\Mods\SamplePlugin > dotnet msbuild /p:Configuration=Release
```

The target will now be under `bin/Release`.

# Build Events

See: [Build Events](https://github.com/risk-of-thunder/R2Wiki/wiki/Build-Events) page that describes workflows while using Visual Studio.

Visual Studio Code also supports tasks that can be added in `.vscode/tasks.json`

## Copy result DLL to BepInEx directory

Create a directory in your `BepInEx/plugins` directory to store your plugin, e.g. `SamplePlugin/`.

Then add the following to your the tasks list in tasks.json for this project, adjusting paths as necessary:

```json
        {
            "label": "deploy",
            "command": "Copy-Item",
            "type": "shell",
            "args": [
                "SamplePlugin/bin/Debug/netstandard2.0/SamplePlugin.dll",
                "D:/Program Files (x86)/Steam/steamapps/common/Risk of Rain 2/BepInEx/plugins/SamplePlugin/"
            ],
            "group": "build"
        }
```

# Post Build Events

Say you want to automate the process of moving your mod to your plugin folder, or maybe run custom commands after. Maybe some files need to be moved before hand. To do this, visual studio has things called "Pre-Build Events" and "Post-Build Events". You can find these under your project properties _(You find these under "Project" at the top, followed by "Properties" all the way at the bottom `alt, P, P`)_
Under Build Events, you will see two text fields: Pre-build and Post-Build.

**Pre-build** events are run before the compiler compiles your project, and **post-build** after.
We will mainly focus on **post-build** events here.

The language for these events is [Batch](https://en.wikipedia.org/wiki/Batch_file) with additional macros with variables localized to your project. While this page shall not go into the details of language, we will discuss some of the more commonly used post-build events.

> If you are running into issues due to macros being empty. Feel free to hop into the discord and ask Paddywan or ping Harb. We'll share your pain and hopefully be able to tell you how to remedy it.

### Copy output DLL
Most commonly used is the copy command to get your dll to where you want it.
```batch
copy <something> <somewhere>
::example
copy /Y "$(TargetPath)" "$(SolutionPath)Builds\"
```
Let's go over the arguments individually
* `copy` signifies a copy instruction. It takes two arguments: what to copy and where to copy it to.
* `/y` is a flag for the copy instruction to suppress the prompting if you want to overwrite the destination file.
* `"$(TargetPath)"` is a macro to the output dll produced by building the project. See **macros** at the end of the page.
* `"$(ProjectPath)Builds\"` is a folder named Builds right next to your `.sln` file. This is especially useful if you want to have folder containing all latest builds of your mods contained in your solution.

### External Programs
If you have external files or programs you want to run, simply provide the absolute path to run it, followed by any arguments that you might need it.
```batch
[program] arg1 arg2 etc
::example
C:\MyAwesomeProgram.exe -makeReallyCool "$(TargetPath)"
```
Now sometimes you want to use the output of such a program in your build events. Because batch is windows and sometimes dumb, you need to save it to a file first.
```batch
[program] arg1 arg2 etc > file.ext
set VARNAME=<file.ext
del file.ext
::example
C:\MyAwesomeProgram.exe -makeReallyCool "$(TargetPath)" > temp.txt
set COOLDLL=<temp.txt
del temp.txt
```
Afterwards you can use this variable in commands by using `%VARNAME%`, in the example's case, this would be `%COOLDLL%`.

### Conditionals
Batch has `for, if` and probably more go google!
Batch does not have conditional operators, but there are some implementations for `or` and `and`

**Or**
```batch
set result=false
if %a% == 1 set result=true
if %b% == 1 set result=true
if "%result%" == "true" (
    do something
)
```
**and**
```batch
if %a% == 1 (
    if %b% == 1 (
        do something
    )
)
```

# Macros
* `$(OutDir)` is the folder to which your dll is outputted. This path ends in a `\`.
* `$(ConfigurationName)` is the configuration under which you've chosen to build under. By default this is set to "Debug", and a "Release" configuration is available. **NOTE:** sometimes this does not work and you need to use `$(Configuration)` instead.
* `$(ProjectName)` is the name of your project.
* `$(TargetName)` is the name of the output dll, excluding the `.dll` part.
* `$(TargetPath)` is the absolute path to your output dll, including the `.dll` part.
* `$(ProjectPath)` is the absolute path to your [project file](https://docs.microsoft.com/en-us/visualstudio/extensibility/internals/projects), including the `.csproj` part.
* `$(ProjectFileName)` is the file name of the project, usually ending in `.csproj`
* `$(TargetDir)` is the absolute path to the folder your dll will be placed in. This path ends in a `\`.
* `$(ProjectDir)` is the absolute path to the folder that contains your project file. This path ends in a `\`.
* `$(SolutionFileName)` is the filename of your [solution](https://docs.microsoft.com/en-us/visualstudio/extensibility/internals/solutions-overview), which contains all your projects. this file usually ends in in `.sln`.
* `$(SolutionPath)` is the absolute path to your solution file. Ending in `.sln`.
* `$(SolutionName)` is your solution name, excluding the `.sln` part.
* `$(TargetExt)`, `$(ProjectExt)`, and `$(SolutionExt)` will likely be `.dll`, `.csproj`, and `.sln` respectively.

 
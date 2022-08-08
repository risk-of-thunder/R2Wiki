# Assembly References

For making a RoR2 mod, your C# project will need to reference different `.dll` [assemblies](https://docs.microsoft.com/en-us/dotnet/standard/assembly/).

Example of assemblies you may be using:

-   `RoR2.dll` which contains the game code
-   `BepInEx.dll`, for declaring your `BaseUnityPlugin` class which is the entry point of your plugin (mod)

If you want to use the multiple libraries, your project must first contain a reference to it.

A reference is essentially an entry in a project file (.csproj) that contains the information that Visual Studio (more precisely the compiler) needs to locate the component or the service.

-   For Visual Studio 2022

    For adding a reference manually, right click on the References or Dependencies node in Solution Explorer and choose Add Reference. You can also right-click on the project node and select Add > Reference

But, here, we use [NuGet](https://docs.microsoft.com/en-us/nuget/) for most, if not all assembly references we have in our projects.

There are multiple reasons we do that, its much easier for the mod dev:

-   They only have to bump the version number of the assembly reference when a new game update comes.
-   They don't have to handle all the assembly **stripping / publicizing** themselves (more on that just below)

### Stripping

But, how do we publish the game dlls on NuGet, aren't those copyrighted, well, yes! They are. But we are publishing a version of them where only the method signatures, class definitions are in it, no method bodies! Yay! No copyright issues!

### Publicizing

We publicize the game assemblies, due to the nature of modding, and depending on how deeply you are modifying it, you'll be bound to modify the internal working of classes, methods, and so on.

Meaning that you'll at some point have to reference normally private variables, methods and so on while through your plugin assembly !

Before, in the days of dark and ancient times, we had to use `System.Reflection` for this, and we had no way of directly referencing private classes, members, methods, it was horrific, hard coded strings everywhere, no compiler safety, but now, this is over!

We just use some [magic .NET assembly reader / writer libraries](https://github.com/0xd4d/dnlib), and modify the metadata of those members to make them public. Yay!

## Ok, fine, where do I get the juice

We'll use the [BepInEx nuget feed](https://nuget.bepinex.dev/packages/RiskOfRain2.GameLibs).

-   Put this [nuget.config](https://github.com/xiaoxiao921/R2Boilerplate/blob/master/nuget.config) file next to your .sln (Visual Studio solution file) file.
-   Then, in your .csproj (Visual Studio C# Project) file, [have these lines](https://github.com/xiaoxiao921/R2Boilerplate/blob/master/ExamplePlugin/ExamplePlugin.csproj#L15-L23)

    This will make use of the BepInEx nuget feed and will handle all the necessary assembly references needed for modding RoR2.
    You'll also need to make sure you have a source file in your plugin that contains those lines:

    ```csharp
    using System.Security;
    using System.Security.Permissions;

    [assembly: SecurityPermission( SecurityAction.RequestMinimum, SkipVerification = true )]
    ```

    [If you are to lazy to type, you can download this .cs file that contains those lines](https://github.com/risk-of-thunder/R2API/blob/master/R2API/AssemblyInfo.cs#L12)

If Visual Studio is already running, close it, this way the BepInEx nuget feed get taken into account.

Open the solution again, then, right click on solution file => Restore NuGet packages / Or try building the solution.

If done correctly, you can now access any private, internal, or readonly types, methods, properties, fields, and events in the game assemblies.

## Publicizing / Stripping assemblies we don't already cover

If you feel like a game assembly should really be added and maintened by the core dev of the RoR2 Modding community, please let us in the modding discord.

If you want to publicize assemblies that we don't already cover on our NuGet packages and may a bit too obscure for general use, you can proceed as follow:

-   Download [NStrip](https://github.com/BepInEx/NStrip/releases)
-   Download latest release and unzip
-   Open console command in the folder where you unzipped
-   `.\NStrip.exe -p -cg -cg-exclude-events -remove-readonly <FULL_PATH_TO_ROR2.DLL_IN_GAME_MANAGED_FOLDER>`
    If you want to keep method bodies, add -n, but don't upload that dll on github or somewhere else if you do that
-   Inside your plugin, create a new empty source file
-   Add the following code to it:
    using System.Security;
    using System.Security.Permissions;

```csharp
using System.Security;
using System.Security.Permissions;

[assembly: SecurityPermission( SecurityAction.RequestMinimum, SkipVerification = true )]
```

-   You can now access any private, internal, or readonly types, methods, properties, fields, and events in the RoR2 assembly

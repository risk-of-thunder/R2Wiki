This page describes how to debug plugins, MonoMod patches and preloader patchers with the help of dnSpy.

## Why

Debugging plugins in Unity is difficult. Since the CLR which runs game code and plugins is managed by the Unity engine, attaching any CLR debugger is not simple. Thus often plugin creators will have to resort to just using logging and vague stack traces.

However, some simple debugging is possible with the help of dnSpy.

## Known limitations

Before proceeding, it is vital to understand the limitations of both dnSpy and BepInEx in terms of debugging. 
Here's a list of known limitations:

**dnSpy** (as of v6.0.4)

* You cannot directly step into C# closures or lambda functions (you can step through the IL and inspect locals).
* Debugging in-memory modules (loaded via [`Assembly.Load(byte[])`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assembly.load?view=netframework-4.7.2#System_Reflection_Assembly_Load_System_Byte___#)) is not properly supported in Unity games.

**BepInEx** (as of 5.0.0.121)

* If you are using the patcherless entrypoint (via `winhttp.dll`), *any patched assemblies will not be debuggable!* That means no debugging of `Assembly-CSharp.dll` or `UnityEngine.CoreModule.dll`.
    - To debug all possible assemblies, you will to [enable dnSpy support in BepInEx](#debugging-patched-assemblies).


## Turning the game into a debug build

First, the game's mono runtime has to be swapped to one that supports debugging with dnSpy.

Download the `mono-2.0-bdwgc.dll` dll here https://tinyurl.com/ror2devmono

Put the `mono-2.0-bdwgc.dll` to `<RoR2 Install Dir>\MonoBleedingEdge\EmbedRuntime`:

**Back up the original `mono-2.0-bdwgc.dll` before doing so!**

![Extract debug mono for Unity 2018.3.0 into EmbedRuntime folder in RoR2.](https://i.imgur.com/yQorpnI.png)

## Setting up dnSpy and setting breakpoints

Download the [latest version of dnSpy](https://github.com/0xd4d/dnSpy/releases). You can pick any of the two available versions (.NET 4.7.2 and .NET Core).

Extract the downloaded archive and run dnSpy.

Next, drag the plugins you want to debug into dnSpy (in this example we are using [`ExamplePlugin`](https://github.com/risk-of-thunder/R2Wiki/wiki/First-Mod)):

**NOTE: You must select the DLL that is in your `BepInEx` folder, and not the one in your Visual Studio solution!**

![Drag and drop the DLL you want to debug on dnSpy's assembly list.](https://i.imgur.com/awp8cm8.png)


You can change the settings of dnSpy via `View > Options`.

To set a breakpoint, navigate the assembly you want to debug and right click on the piece of code you want to debug.  
Next, select `Add breakpoint` to set the breakpoint:

![Right-click on the piece of code as select "Add breakpoint" to add a breakpoint.](https://i.imgur.com/FFZIwdt.png)

Note that some code might not be selectable. In that case you can change dnSpy to show the precise IL code from the dropdown in the top bar.

## Running the game via dnSpy

After you have set the breakpoints, you can start debugging the game.

Select `Debug > Start Debugging` to open up the *Debug Program* dialog.

Change the settings as follows:

* *Debug Engine*: Unity
* *Executable*: Locate and select `Risk of Rain 2.exe` from the game's installation directory.
* *Timeout (s)*: 30. You can optionally set it to higher values if the game loads too slow.

![dnSpy's Debug Program dialog.](https://i.imgur.com/TMhbCOE.png)

Finally, press `OK` to start the game.

Wait until the game loads your assembly. If everything worked correctly, the execution will stop on the breakpoint:

![dnSpy window when the game hits a breakpoint.](https://i.imgur.com/rY3De78.png)

From there, you can do same things like in the normal debugger:

* Inspect locals and type members
* Step into, step over, set more breakpoints (via the top bar)
* Modify values (in some cases)

Note that when you step in dnSpy, it steps one IL instruction at a time (in which case one single expression can take multiple steps to move over).

## Debugging patched assemblies

In some cases it is useful to be able to also debug assemblies that have been patched via BepInEx's preloader.  
However, this is very difficult, as the preloader patches and loads assemblies directly in memory, which makes debugging with dnSpy impossible without additional tinkering.

However, as of 15th April 2019, BepInEx includes two new configuration options: `LoadDumpedAssemblies` and `BreakBeforeLoadAssemblies`. With these, it is possible to debug assemblies loaded via the preloader (i.e. Assembly-CSharp).

First [install debug version of mono](#turning-the-game-into-a-debug-build) and [download dnSpy](https://github.com/0xd4d/dnSpy/releases) if you haven't done so yet.

Next, download and install the latest BepInExPack with dnSpy support included. As of 15th April 2019, you can only get it from [BepisBuilds](http://bepisbuilds.dyn.mk/bepinex_ror2_pack) (build #22 or newer), as there is no official release of it yet.

Run the game once in order for BepInEx to generate its full configuration file.  
Then, open to `BepInEx/config/BepInEx.cfg` and edit the the two configuration options to have the following values:

```ini
LoadDumpedAssemblies = true

BreakBeforeLoadAssemblies = true
```

After that [run the game via dnSpy](#running-the-game-via-dnspy). **Do not open any assemblies that are going to be patched (like `Assembly-CSharp`)!**

If everything worked, BepInEx will launch, patch assemblies and automatically break the execution and display a message in console:

![dnSpy stopped at a breakpoint set by BepInEx](https://i.imgur.com/aD7Enon.png)

Now go to `BepInEx/DumpedAssemblies` (as specified in the console), open patched assemblies you want to debug and set breakpoints.
When you're done, click `Continue` in the top bar to continue execution.

BepInEx will continue loading the patched assemblies. If everything worked, you will eventually hit a breakpoint in the patched assembly:

![Debugging patched assemblies works with dnSpy](https://i.imgur.com/nSnxVpS.png)

### Important notes

* **The assemblies in `DumpedAssemblies` must not be opened before debugging!** This is because otherwise BepInEx will not be able to write to the folder!
* There is a possibility of bugs if the assembly needs to know its location via `Assembly.Location` (pretty rare though, should not be an issue in case or RoR2).
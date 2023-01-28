
# Debugging Your Mods

The microsoft documentation says:

When you debug your app, it usually means that you are running your application with the debugger attached. When you do this, the debugger provides many ways to see what your code is doing while it runs. You can step through your code and look at the values stored in variables, you can set watches on variables to see when values change, you can examine the execution path of your code, see whether a branch of code is running, and so on. If this is the first time that you've tried to debug code, you might want to read [Debugging for absolute beginners](https://docs.microsoft.com/en-us/visualstudio/debugger/debugging-absolute-beginners?view=vs-2022) before going through this guide.

## General Setup
1. [Install BepInEx from thunderstore](https://thunderstore.io/package/bbepis/BepInExPack/)
2. [Install doorstop 4](https://cdn.discordapp.com/attachments/567836513078083584/1068586988673961984/BepInEx_x64_5.4.19.0_Doorstop_4.zip)
3. Drag and drop as follow into your Risk of Rain 2 game folder
	* If  you want to debug an R2ModMan profile instead, drag and drop the contents of the Zip file into your r2modman profile

![qsdqsd](https://cdn.discordapp.com/attachments/575431803523956746/950432823377023087/unknown.png)
It should replace a lot of files, press yes to all
4. Open the `doorstop_config.ini`
You can set the config like this
```
# If true, Mono debugger server will be enabled
debug_enabled=true

# When debug_enabled is true, this option specifies whether Doorstop should initialize the debugger server
# If you experience crashes when starting the debugger on debug UnityPlayer builds, try setting this to false
debug_start_server=true

# When debug_enabled is true, specifies the address to use for the debugger server
debug_address=127.0.0.1:55555

# If true and debug_enabled is true, Mono debugger server will suspend the game execution until a debugger is attached
debug_suspend=true
```
This will make doorstop suspend the game on start, open a debugging server at port 55555, allowing you to attach any debugger of your liking, you can use either VS, dnSpy or Rider.

Putting breakpoints inside game assemblies while using the Visual Studio Debugger is not possible currently, but works with dnSpy, please refer to the dnSpy section below on how to configure it.

*__Notice:__* using the custom Doorstop version can cause issues with the BepInEx GUI that's bundled in RoR2BepInExPack, it is recommended to go into BepInEx's config (``BepInEx\config\BepInEx.cfg``) and set the ``Logging.Console``'s ``Enabled`` config to ``true``, and setting BepInExGUI's config (``BepInEx\config\BepInEx.GUI.cfg``)'s ``Enable BepInEx GUI`` config to ``false``

## Visual Studio 2022
- Install [Visual Studio Tools for Unity](https://docs.microsoft.com/en-us/visualstudio/gamedev/unity/get-started/getting-started-with-visual-studio-tools-for-unity?pivots=windows)
- Install [UnityModExtension](https://cdn.discordapp.com/attachments/562704639569428506/950162181285085194/UnityModExtension.vsix)
- Configure the Unity Mod Extension if needed by going in Tools -> Options

  ![image](https://user-images.githubusercontent.com/837334/176724832-37afd649-e982-49c3-aa9f-f47be219d093.png)

- Press Debug -> Run Game and Start Debugging

  ![image](https://user-images.githubusercontent.com/837334/176724945-b1b7eb6c-b095-4058-9eb0-78b09d2ea25d.png)

## dnSpy

Make sure the `doorstop_config.ini` is setup like above, but with the `debug_suspend=false`, since having it as `true` doesn't seem to work with dnSpy

You'll need to setup the BepInEx.cfg so that it dumps game assemblies to folder, as there may be BepInEx patchers modifying the game assemblies.

In the `BepInEx/config/BepInEx.cfg`, set to true the following entries: `DumpAssemblies`, `LoadDumpedAssemblies` and `BreakBeforeLoadAssemblies`.

In dnSpy, make sure that the RoR2.dll that is in there is the assembly from `BepInEx\DumpedAssemblies\Risk of Rain 2\RoR2.dll`

Then, launch the game, attach with dnSpy through `Debug -> Start Debugging -> Debug Engine: Unity (connect)`

It should automatically breakpoint somewhere inside a BepInEx assembly, then you'll be able to put any breakpoint in the game assemblies

# Debugging Your Mods

The microsoft documentation says:

When you debug your app, it usually means that you are running your application with the debugger attached. When you do this, the debugger provides many ways to see what your code is doing while it runs. You can step through your code and look at the values stored in variables, you can set watches on variables to see when values change, you can examine the execution path of your code, see whether a branch of code is running, and so on. If this is the first time that you've tried to debug code, you might want to read [Debugging for absolute beginners](https://docs.microsoft.com/en-us/visualstudio/debugger/debugging-absolute-beginners?view=vs-2022) before going through this guide.

## <a name="general_setup"><a/> General Setup
1. [Install BepInEx from thunderstore](https://thunderstore.io/package/bbepis/BepInExPack/)
2. [Install doorstop 4](https://cdn.discordapp.com/attachments/567836513078083584/1068586988673961984/BepInEx_x64_5.4.19.0_Doorstop_4.zip)
3. Drag and drop as follow into your Risk of Rain 2 game folder
	* If  you want to debug an R2ModMan profile instead, drag and drop the contents of the Zip file into your r2modman profile

![Replace files](https://cdn.discordapp.com/attachments/575431803523956746/950432823377023087/unknown.png)
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

5. Don't forget to put the .pdb file next to your plugin .dll, that's the file which contains the debug data

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

## Rider

**REQUIRED**:
Your profile MUST have...

Debugging with Rider requires slightly more setup, but will be fully functional using Rider's built-in debugger tool. Additionally, you will have a highly-configurable build-run-debug pipeline. The process is straightforward:
- Build
- Run
- Connect to the debugger

### Building with Rider
As you know, by default, Rider outputs the build to project_root/bin/Debug directory. However, we will improve this to automatically our mod to our plugin directory.
1. Get absolute path of your desired output location (see below)
   1. For r2modman profiles (recommended), it should look something like ```C:\Users\YOUR_USER_NAME\AppData\Roaming\r2modmanPlus-local\RiskOfRain2\profiles\YOUR_PROFILE_NAME\BepInEx\plugins\YOUR_PLUGIN_NAME```
   2. If you are NOT using r2modman, your path should look something like this: ```C:\Program Files (x86)\Steam\steamapps\common\Risk of Rain 2\BepInEx\plugins```
2. Open your ```PROJECT.csproj``` (If you can't find it, make sure you select "File System", as I've underlined in the blue at the top):
![csproj_location.PNG](../../../media/bepinex/csproj_location.PNG)
3. Add the following to the csproj file
```cs
    <PropertyGroup Condition=" '$(Configuration)' == 'Debug' ">
      <OutputPath>bin\Debug\</OutputPath>
      <ProfilePluginPath>THE_PATH_YOU_COPIED_IN_STEP_1\</ProfilePluginPath> <!-- (Make sure you have a trailing slash!) -->
    </PropertyGroup>
        
    <Target Name="PostBuildScript" AfterTargets="Build" Condition="'$(Configuration)' == 'Debug'">
        <ItemGroup>
            <_CopyItems Include="$(TargetDir)*.*" />
        </ItemGroup>
        <Message Text="Copying files $(TargetDir) to $(ProfilePluginPath)" />
        <Copy SourceFiles="@(_CopyItems)" DestinationFolder="$(ProfilePluginPath)" /> 
    </Target>
</Project> <-- (Make sure you paste within the Project tag like i'm showing here. Don't include this line)
```
We're all done with building! Feel free to give it a test by building your project and ensuring the build ends up in the right location.

### Running with Rider <a name="running_with_rider"><a/>
We're going to piggyback on Rider's build in "Run and Debug" modes. By the end of this section, you will be able to run Risk of Rain using your r2modman profile (or your non-r2modman alternative) and freshly built plugin all in one click. 
1. Open Rider's run configuration editor (see photo):
 ![rider_run_config.PNG](../../../media/rider/rider_run_config.jpg)
2. For this next step, it is **critical** you have correctly completed the [General Setup](#a-namegeneralsetup-a-general-setup). Confirm this by launching your r2modman profile through r2modman. You'll know you're in the clear when your mods are loading without any issues.
3. In this next photo, you're going to create a new Run/Debug configuration. Let's start with creating a standalone process for your game (Don't worry about the fields for it yet).
![standalone_option.PNG](../../../media/rider/standalone_option.jpg) <a name="bro"><a/>
4. Now that you have a Standalone Player, let's get the fields filled out (Refer to photo below for reference through each step).
   1. (Refer to the circle on the right) Select your newly created **Standalone Player**
   2. Click on the **+** symbol below the **Before Launch** section of the configuration options and select **Build Project**. You'll know it's correct when you see that green hammer like I have at the middle of the screen.
   3. Set **Exe path** to ```C:/Program Files (x86)/Steam/steamapps/common/Risk of Rain 2/Risk of Rain 2.exe``` (or, wherever your Risk of Rain 2.exe file is located). In case you're wondering, this is the same executable that r2modman profiles execute, hence why the path is the same. Once you select this, **Working Directory** will automatically populate.
   4. The last thing you need is **Program arguments**. It's important to note that incorrect arguments will result in your mods failing to load. Please reach out to the [Community discord](DISCORD LINK) with questions. 
      1. **r2modman profiles**: This will be quick and easy. Simply launch r2modman, navigate to settings, and search for **Set launch parameters** (see photo below). Click on the search result and you should see something like this:
      ```cs
      Some parameters are provided by default:
      
      Modded:
      --doorstop-enabled true --doorstop-target-assembly "C:\Users\lukem\AppData\Roaming\r2modmanPlus-local\RiskOfRain2\profiles\dev\BepInEx\core\BepInEx.Preloader.dll"
      
      Vanilla:
      doorstop-enabled false
      ```
      2. Copy the text within the **Modded** category and paste that into the **Program arguments**, as shown in the picture below.
![run_config.PNG](../../../media/rider/run_config.jpg)
5. You now have **Standalone Player** properly configured. You'll know it works when clicking Rider's **Run** or **Debug** button against your **Standalone Player** configuration launches Risk of Rain directly using your r2modman profile. At this point, Rider is automatically building your mod, adding it to your custom r2modman profile, and launching for you.

### Connecting to the debugger
Last but not least, the debugger part. This will be quick and easy.
1. Open Rider's configuration editor again. Create each of the following Run/Debug configurations:
   1. Mono Remote
   2. Compound

## <a name="faq"><a/> FAQ



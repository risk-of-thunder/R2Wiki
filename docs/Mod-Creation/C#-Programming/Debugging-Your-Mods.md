# Debugging Your Mods

The microsoft documentation says:

When you debug your app, it usually means that you are running your application with the debugger attached. When you do this, the debugger provides many ways to see what your code is doing while it runs. You can step through your code and look at the values stored in variables, you can set watches on variables to see when values change, you can examine the execution path of your code, see whether a branch of code is running, and so on. If this is the first time that you've tried to debug code, you might want to read [Debugging for absolute beginners](https://docs.microsoft.com/en-us/visualstudio/debugger/debugging-absolute-beginners?view=vs-2022) before going through this guide.

-------------

- Note: r2modman setup is not currently supported
- [Install BepInEx from thunderstore](https://thunderstore.io/package/bbepis/BepInExPack/)
- [Install doorstop 4](https://cdn.discordapp.com/attachments/950162181427695627/950163300816125982/BepInEx_x64_5.4.19.0_Doorstop_4.zip)
- Drag and drop as follow into your Risk of Rain 2 game folder
![qsdqsd](https://cdn.discordapp.com/attachments/575431803523956746/950432823377023087/unknown.png)
It should replace a lot of files, press yes to all
- Open the `doorstop_config.ini`
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
This will make doorstop suspend the game on start, open a debugging server at port 55555, allowing you to attach any debugger of your liking, you can use either VS or Rider.

## Visual Studio 2022
- Install [Visual Studio Tools for Unity](https://docs.microsoft.com/en-us/visualstudio/gamedev/unity/get-started/getting-started-with-visual-studio-tools-for-unity?pivots=windows)
- Install [UnityModExtension](https://cdn.discordapp.com/attachments/562704639569428506/950162181285085194/UnityModExtension.vsix)
- Configure the Unity Mod Extension if needed by going in Tools -> Options

  ![image](https://user-images.githubusercontent.com/837334/176724832-37afd649-e982-49c3-aa9f-f47be219d093.png)

- Press Debug -> Run Game and Start Debugging

  ![image](https://user-images.githubusercontent.com/837334/176724945-b1b7eb6c-b095-4058-9eb0-78b09d2ea25d.png)

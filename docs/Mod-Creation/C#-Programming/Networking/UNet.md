# UNet

 *A detailed tutorial on how to use the UNetWeaver Utility for networking properly your mods.*

---
## UNetWeaver

### What is this?

Ever wanted to use in your BepinEx plugin the [usual networking stuff used by Unity users](https://docs.unity3d.com/2018.3/Documentation/Manual/UNetActions.html) ?
Now you can! Using this utility from the [Unity code repository](https://github.com/Unity-Technologies/UnityCsReference/blob/2018.3/Extensions/Networking/Weaver/) itself, with some modifications, you'll be able to use it outside the Unity Editor on such a [class](https://gist.github.com/xiaoxiao921/2aed4cdac1352a41e087908a51cf27ec) of your plugin for example.

#### Why use UNetWeaver over alternatives:

* No user-side dependencies. This means that users only have to install one file (your dll).
* Native Unity tags, simply `using UnityEngine.Networking;` will get you everything you need.


### Example implementation
The simplest example of networking is to simply send a message to the console of all users whenever a (specific) console command is issued.

```csharp
using UnityEngine.Networking;
using UnityEngine;
using BepInEx;
using R2API;
using R2API.Utils;

[BepInPlugin("com.networktest","ifISeethisInTechSupportIwillBerateYou","0.0.1")]
//Commandhelper is only needed for this example.
//PrefabAPI is needed for the InstantiateClone Method contained within.
[R2APISubmoduleDependency(nameof(CommandHelper), nameof(PrefabAPI))]
class MyPluginClass : BaseUnityPlugin
{
    //Static references so we do not need to do tricky things with passing references.
    internal static GameObject CentralNetworkObject;
    private static GameObject _centralNetworkObjectSpawned;

    public void Awake()
    {
        //We create an empty gameobject to hold all our networked components. The name of this GameObject is largely irrelevant.
        var tmpGo = new GameObject("tmpGo");
        //Add the networkidentity so that Unity knows which Object it's going to be networking all about.
        tmpGo.AddComponent<NetworkIdentity>();
        //Thirdly, we use InstantiateClone from the PrefabAPI to make sure we have full control over our GameObject.
        CentralNetworkObject = tmpGo.InstantiateClone("somethingUnique");
        // Delete the now useless temporary GameObject
        GameObject.Destroy(tmpGo);
        //Finally, we add a specific component that we want networked. In this example, we will only be adding one, but you can add as many components here as you like. Make sure these components inherit from NetworkBehaviour.
        CentralNetworkObject.AddComponent<MyNetworkComponent>();

        //In this specific example, we use a console command. You can look at https://github.com/risk-of-thunder/R2Wiki/wiki/Console-Commands for more information on that.
        CommandHelper.AddToConsoleWhenReady();
    }

    // ExecuteOnServer as a concommand flag makes sure it's always exectuted on the... you guessed it, server.
    [ConCommand(commandName = "debuglog_on_all", flags = ConVarFlags.ExecuteOnServer, helpText = "Logs a network message to all connected people.")]
    private static void CCNetworkLog(ConCommandArgs args)
    {
        //Although here it's not relevant, you can ensure you are the server by checking if the NetworkServer is active.
        if (NetworkServer.active)
        {
            //Before we can Invoke our NetworkMessage, we need to make sure our centralized networkobject is spawned.
            // For doing that, we Instantiate the CentralNetworkObject, we obviously check if we don't already have one that is already instantiated and activated in the current scene.
            // Note : Make sure you Instantiate the gameobject, and not spawn it directly, it would get deleted otherwise on scene change, even with DontDestroyOnLoad.
            if (!_centralNetworkObjectSpawned)
            {
                _centralNetworkObjectSpawned = 
                    Object.Instantiate(CentralNetworkObject);
                NetworkServer.Spawn(_centralNetworkObjectSpawned);
            }
            //This readOnlyInstancesList is great for going over all players in general, 
            // so it might be worth commiting to memory.
            foreach (NetworkUser user in NetworkUser.readOnlyInstancesList)
            {
                //Args.userArgs is a list of all words in the command arguments.
                MyNetworkComponent.Invoke(user, string.Join(" ", args.userArgs));
            }
        }
    }
}

//Important to note that these NetworkBehaviour classes must not be nested for UNetWeaver to find them.
internal class MyNetworkComponent : NetworkBehaviour
{
    // We only ever have one instance of the networked behaviour here.
    private static MyNetworkComponent _instance;
    
    private void Awake()
    {
        _instance = this;
    }
    public static void Invoke(NetworkUser user, string msg)
    {
        _instance.TargetLog(user.connectionToClient, msg);
    }

    // While we can't find the entirety of the Unity Script API in here, we can provide links to them.
    // This attribute is explained here: https://docs.unity3d.com/2017.3/Documentation/ScriptReference/Networking.TargetRpcAttribute.html
    [TargetRpc]
    //Note that the doc explictly says "These functions [-> Functions with the TargetRPC attribute] must begin with the prefix "Target" and cannot be static." 
    private void TargetLog(NetworkConnection target, string msg)
    {
        Debug.Log(msg);
    }
}
```

When you build this example (and patch it with the Weaver), you'll end up with a BepInEx plugin that adds the `debuglog_on_all` command to the console. When you call it, everything after the command will get send to each connected player's console, provided they have this plugin installed to recieve the networked component. 
Notably, this only works if the Host has this plugin installed, and any client wishing to issue this command or see this command too. Otherwise their console won't know what to do with the string `debuglog_on_all`.) or how to deal with the weird network message that tries to spawn an unknown object.

### How to patch

1. **As a Patcher.**

- Download the [archive](https://github.com/risk-of-thunder/R2Wiki/files/14428540/UNetWeaver.zip) that contains the BepinEx Patcher UNet Weaver.
- Extract it in your `Risk of Rain 2\BepInEx\patchers` folder.
- If everything went correctly, your assembly will be patched when loading the game.
- This automates the need of pointing the weaver towards the right libs, as they will all be loaded by BepInEx already.
- Note thate this is **run once** on your (the developer's) computer. After this you will have a patched plugin that you can distribute without the patcher.
- Yes, this is essentially using Risk of Rain 2 as a plugin builder.

2. **As a PostBuild-Event**

- Download the [archive](https://github.com/risk-of-thunder/R2Wiki/files/14428459/NetworkWeaver.zip) that contains the UnetWeaver executable.
- You can set it up how you want, but for now we'll assume that you extracted it in a folder called `NetworkWeaver` in your project root and your libraries are there too in a `libs` folder.
- In your project's properties, create the following postbuild event
  - `REM TEXT` is a comment. Batch is not the best for comments, but it'll keep it copypastable.
  - The wiki also has the [Build Events](https://github.com/risk-of-thunder/R2Wiki/wiki/Build-Events) page for more information on these.

   ```bat
   REM First we copy the from the output folder to the NetworkWeaver folder.
   REM We store the output from robocopy in a file called robocopy which we'll delete at the end of the file.
   robocopy $(TargetDir) $(ProjectDir)\NetworkWeaver $(TargetFileName) > $(TargetDir)Robocopy
   REM Then we navigate our script to the NetworkWeaver folder to make the follow up line less verbose.
   cd $(ProjectDir)\NetworkWeaver
   REM Unity.UNetWeaver.exe {path to Coremodule} {Path to Networking} {Path to output folder} {Path to the dll you want patched} {Path to all needed references for the to-be-patched dll}
   Unity.UNetWeaver.exe "..\libs\UnityEngine.CoreModule.dll" "..\libs\com.unity.multiplayer-hlapi.Runtime.dll" "Patched/"  $(TargetFileName) "$(ProjectDir)\libs"
   REM We store the prepatched dll disabled as a .prepatch so that you can check it if you want, but first we need to make sure that file doesn't exist already.
   IF EXIST $(TargetFileName).prepatch (
   DEL /F $(TargetFileName).prepatch
   )
   REM Then we rename the prepatched file to bla.dll.prepatch
   ren $(TargetFileName) $(TargetFileName).prepatch
   REM move our script to the Patched Folder
   cd Patched
   REM move from the current directory (.) to the projectDirectory and store the output once more to the Robocopy file.
   robocopy . $(ProjectDir)  $(TargetFileName) > $(TargetDir)Robocopy
   REM Remove the patched file from the Patched folder, because the Weaver won't run if the file already exists.
   del  $(TargetFileName)
   REM Delete the holder for all robocopy output.
   del $(TargetDir)Robocopy
   ```

- if everything went correctly, your patched plugin.dll will be in your project root whenever you build. (So next to your .csproj file.)

If you feel like it, you can see what the Weaver changed in your assembly by opening it with dnSpy and looking at the networking classes you had. New generated methods and fields should be in them.
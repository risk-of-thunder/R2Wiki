# UNet

 *A detailed tutorial on how to use the UNetWeaver Utility for networking properly your mods.*

---
## UNetWeaver

### What is this?

UNetWeaver is a patcher by the [Unity code repository](https://github.com/Unity-Technologies/UnityCsReference/blob/2018.3/Extensions/Networking/Weaver/) itself that injects code in any `NetworkBehaviour` components to ensure proper serialization and deserialization. Without it such components would fail to communicate across the server. This allows you to use the [usual networking stuff used by Unity users](https://docs.unity3d.com/2018.3/Documentation/Manual/UNetActions.html) outside the Unity Editor, such as [this class](https://gist.github.com/xiaoxiao921/2aed4cdac1352a41e087908a51cf27ec).

Why use UNetWeaver over alternatives:

* No user-side dependencies. This means that users only have to install one file (your dll).
* Native Unity tags, simply `using UnityEngine.Networking;` will get you everything you need.

### Networking crash course

#### Communication

The server is generally the authoritative figure for spawning objects, as well as sending messages to the clients and syncing variables. As such, when a client finds themselves in a position of having to execute some code that is only suitable from the server, they need to let the server know accordingly. There are mainly two ways of achieving this:

* Using the [`CommandAttribute`](https://docs.unity3d.com/2018.3/Documentation/ScriptReference/Networking.CommandAttribute.html) to invoke a method on the server
* Triggering a console command on the server (Risk of Rain 2 native mechanism, internally uses the CommandAttribute)

Once the server has received the appropriate signal, they can either execute the code on their machine or pass along a message to any other concerned clients. This can be achieved by using:

* [`TargetRpcAttribute`](https://docs.unity3d.com/2018.3/Documentation/ScriptReference/Networking.TargetRpcAttribute.html) to invoke a method on a certain client's machine
* [`ClientRpcAttribute`](https://docs.unity3d.com/2018.3/Documentation/ScriptReference/Networking.ClientRpcAttribute.html) to invoke a method on all clients

These attribute should be applied on non-static methods. Furthermore:

* `CommandAttribute` methods must start with the `Cmd` prefix
* `TargetRpcAttribute` methods must start with the `Target` prefix
* `ClientRpcAttribute` methods must start with the `Rpc` prefix

#### Serializable types

Any methods associated with the aforementioned attributes can contain only certain types of arguments which can be serialized across the network.

* Basic type (byte, int, float, string, UInt64, etc)
* Built-in Unity math type (Vector3, Quaternion, etc),
* Arrays of basic types
* Structs containing allowable types
* NetworkIdentity
* NetworkInstanceId
* NetworkHash128
* GameObject with a NetworkIdentity component attached.

### Example
The simplest example of networking is to simply send a message to the console of all users.

#### Implementation with a console command

```csharp
using BepInEx;
using R2API;
using UnityEngine;
using UnityEngine.Networking;

// Necessary so concommands can be registered.
[assembly: HG.Reflection.SearchableAttribute.OptIn]

[BepInPlugin("com.networktest","ifISeethisInTechSupportIwillBerateYou","0.0.1")]
// PrefabAPI is needed for the InstantiateClone Method contained within.
[BepInDependency(PrefabAPI.PluginGUID)]
class MyPluginClass : BaseUnityPlugin
{
    // Static references so we do not need to do tricky things with passing references.
    internal static GameObject CentralNetworkObject;
    private static GameObject _centralNetworkObjectSpawned;

    private void Awake()
    {
        // We create an empty gameobject to hold all our networked components. The name of this GameObject is largely irrelevant.
        var tmpGo = new GameObject("tmpGo");
        // Add the networkidentity so that Unity knows which Object it's going to be networking all about.
        tmpGo.AddComponent<NetworkIdentity>();
        // Thirdly, we use InstantiateClone from the PrefabAPI to make sure we have full control over our GameObject.
        CentralNetworkObject = tmpGo.InstantiateClone("somethingUnique");
        // Delete the now useless temporary GameObject
        GameObject.Destroy(tmpGo);
        // Finally, we add a specific component that we want networked. In this example, we will only be adding one, but you can add as many components here as you like. Make sure these components inherit from NetworkBehaviour.
        CentralNetworkObject.AddComponent<MyNetworkComponent>();
    }

    // ExecuteOnServer as a concommand flag makes sure it's always exectuted on the... you guessed it, server.
    [ConCommand(commandName = "debuglog_on_all", flags = ConVarFlags.ExecuteOnServer, helpText = "Logs a network message to all connected people.")]
    private static void CCNetworkLog(ConCommandArgs args)
    {
        // Although here it's not relevant, you can ensure you are the server by checking if the NetworkServer is active.
        if (NetworkServer.active)
        {
            // Before we can Invoke our NetworkMessage, we need to make sure our centralized networkobject is spawned.
            // For doing that, we Instantiate the CentralNetworkObject, we obviously check
            // if we don't already have one that is already instantiated and activated in the current scene.
            // Note : Make sure you Instantiate the gameobject, and not spawn it directly,
            // it would get deleted otherwise on scene change, even with DontDestroyOnLoad.
            if (!_centralNetworkObjectSpawned)
            {
                _centralNetworkObjectSpawned = Object.Instantiate(CentralNetworkObject);
                NetworkServer.Spawn(_centralNetworkObjectSpawned);
            }
            // This readOnlyInstancesList is great for going over all players in general, 
            // so it might be worth commiting to memory.
            foreach (NetworkUser user in NetworkUser.readOnlyInstancesList)
            {
                // `args.userArgs` is a list of all words in the command arguments.
                MyNetworkComponent.Invoke(user, string.Join(" ", args.userArgs));
            }
        }
    }
}

// Important to note that these NetworkBehaviour classes must not be nested for UNetWeaver to find them.
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

    [TargetRpc]
    private void TargetLog(NetworkConnection target, string msg)
    {
        Debug.Log(msg);
    }
}
```

When you build this example (and patch it with the Weaver), you'll end up with a BepInEx plugin that adds the `debuglog_on_all` command to the console. When you call it, everything after the command will get sent to each connected player's console, provided they have this plugin installed to receive the networked component. 
Notably, this only works if the Host has this plugin installed, and any client wishing to issue this command or see this command too. Otherwise their console won't know what to do with the string `debuglog_on_all`, or how to deal with the weird network message that tries to spawn an unknown object.

In order to send this command dynamically with code, use `Console.instance.SubmitCmd(NetworkUser.readOnlyLocalPlayersList[0], "debuglog_on_all Test message", false);`

#### Implementation with CommandAttribute

```csharp
using BepInEx;
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.Networking;
using UnityEngine.ResourceManagement.AsyncOperations;

[BepInPlugin("com.networktest","ifISeethisInTechSupportIwillBerateYou","0.0.1")]
class MyPluginClass : BaseUnityPlugin
{
    private void Awake()
    {
        // In order for the CommandAttribute to work, we must execute it from a player game object
        // e.g. associated with a NetworkConnection, such as PlayerMaster.
        Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Core/PlayerMaster.prefab").Completed += delegate (AsyncOperationHandle<GameObject> obj)
        {
            if (obj.Result)
            {
                obj.Result.AddComponent<MyNetworkComponent>();
            }
        };
    }

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.J))
        {
            var localUser = LocalUserManager.GetFirstLocalUser();
            if (localUser != null && localUser.cachedMaster != null)
            {
                localUser.cachedMaster.GetComponent<MyNetworkComponent>().LogMessage("Test message");
            }
        }
    }
}

// Important to note that these NetworkBehaviour classes must not be nested for UNetWeaver to find them.
internal class MyNetworkComponent : NetworkBehaviour
{
    public void LogMessage(string msg)
    {
        if (NetworkServer.active)
        {
            // If we are on the server, job done - just send the message to everyone
            RpcLogMessage(msg);
        }
        else
        {
            // Else tell the server to handle the message
            CmdLogMessage(msg);
        }
    }

    [Command]
    private void CmdLogMessage(string msg)
    {
        // The client tells the server to execute this
        RpcLogMessage(msg);
    }

    [ClientRpc]
    private void RpcLogMessage(string msg)
    {
        // The server tells everyone to execute this
        LogMessageInternal(msg);
    }

    private void LogMessageInternal(string msg)
    {
        // This is executed on everyone's machine
        Debug.Log(msg);
    }
}
```

### SyncVar

Adding the `SyncVarAttribute` to a field makes it so that its value is synchronised with all the clients when the server changes it. While a client can change this value locally it does not trigger an update for everyone else. If a client wants to update a SyncVar for everyone, they should use a `Command` to let the server do it.

The following types can be SyncVars:

* Basic type (byte, int, float, string, UInt64, etc)
* Built-in Unity math type (Vector3, Quaternion, etc)
* Structs containing allowable types (NetworkInstanceId, etc)
* GameObject with a NetworkIdentity component attached

There are also some list variants, except that these are not attributes but actual types.

* SyncListString
* SyncListFloat
* SyncListInt
* SyncListUInt
* SyncListBool
* SyncListStruct\<T\>

Note that a NetworkBehaviour can only support up to 32 SyncVars / SyncLists.

#### Differences with ClientRpc

A SyncVar is always updated to the current value when the networked object is spawned for a client that joins the session late. On the other hand a ClientRpc is only sent to clients that exist at its time of sending and any late joiners will miss it. As such, a ClientRpc is suitable for one-off events, such as an explosion, while a SyncVar for tracking game state, e.g., a character's health.

#### SyncVar hook

A hook is like an event that is fired every time a SyncVar is updated. Note that this method is only called for clients when they receive the updated value; **the server must call this manually**. Furthermore, since the hook is called instead of updating the value directly, the hook must also make sure to update the value.

```cs
public class SyncVarHookExample : NetworkBehaviour
{
    [SyncVar(hook = nameof(OnSyncTarget)]
    private GameObject target;

    // A component cannot be a SyncVar so we will update it ourselves
    private CharacterBody targetBody;

    [Server]
    public void UpdateTarget(GameObject newTarget)
    {
        OnSyncTarget(target);
    }

    private void OnSyncTarget(GameObject newTarget)
    {
        // At this point in the code, we have access to both the old target
        // and the new target in case we need to do any handing-off operations.

        // Here we just want to update the value - DON'T OMIT THIS
        target = newTarget;

        // Any further logic
        if (target)
        {
            targetBody = target.GetComponent<CharacterBody>();
        }
    }

    public override void OnStartClient()  
    {
        base.OnStartClient();
        // While a client is guaranteed to have received the up-to-date value before
        // this point, SyncVar hooks are not called when data is deserialized during
        // initialization. If the hook is something that you always want to run every
        // time the value is assigned to something, it can be manually called here.
        OnSyncTarget(target);
    }
}
```

### Manual serialization

Each NetworkBehaviour has the uint property `syncVarDirtyBits` which encodes whether any of its 32 sync vars has just been updated. This value can be updated with `SetDirtyBit(uint dirtyBit)`, e.g., SetDirtyBit(16u). This value should be a power of 2, each corresponding to a unique object that needs to be synchronised. When the `NetworkIdentity` of a game object observes that any of its NetworkBehaviours has dirty bits, it serializes that information and sends it to the clients. This means that a game object sends a single message for any of its NetworkBehaviours that need updating, all concatenated one after the other. It is extremely important that deserialization is accurate since if a NetworkBehaviour reads more or fewer bytes than it is supposed to it, it will throw off deserialization for the remaining components.

The actual serialization / deserialization is handled by overriding the `OnSerialize` and `OnDeserialize` methods. Weaver populates these automatically for you if the class has any SyncVar fields (along with creating appropriate messages for any Command / ClientRpc calls). If SyncVars cannot capture the complexity of the data that you need to serialize and you need to resort to a manual implementation, the SyncVarAttribute should not be used anywhere in the class.

Generally the data packed for serialization has the structure `dirtyBits[dataForVariableA][dataForVariableB]`, where the dirty bits in essence act like a header that tell us how to read the remaining data. It is crucial that the written data is read in the same order and with the correct type each. Furthermore, `OnSerialize` returns a bool that signals whether all the dirty bits have been serialized so that the NetworkIdentity can call its `ClearAllDirtyBits()`.

```cs
class ManualSerializationExample : NetworkBehaviour
{
    // Manually defining the dirty bits
    private const uint var1DirtyBit = 1u;
    private const uint var2DirtyBit = 2u;

    private int var1;      // this could have been a SyncVar, but we need to avoid it
    private bool[] var2;   // something complex to serialize

    [Server]
    private void SetVar1(int newValue)
    {
        if (var1 != newValue)
        {
            var1 = newValue;
            SetDirtyBit(var1DirtyBit);
        }
    }

    [Server]
    private SetVar2Value(int index, bool newValue)
    {
        if (index < var2.Length && var2[index] != newValue)
        {
            var2[index] = newValue;
            SetDirtyBit(var2DirtyBit);   
        }
    }

    private void SerializeVar2(NetworkWriter writer)
    {
        writer.WritePackedUInt32(var2.Length);
        for (int i = 0; i < var2.Length; i++)
        {
            writer.Write(var2[i]);
        }
    }

    private void DeserializeVar2(NetworkReader reader)
    {
        int count = (int)reader.ReadPackedUInt32();
        for (int i = 0; i < count; i++)
        {
            var2[i] = reader.ReadBoolean();
        }
    }

    public override bool OnSerialize(NetworkWriter writer, bool forceAll)
    {
        // This is used when a network object is to be spawned on the client
        // so that all of the values are updated.
        if (forceAll)
        {
            // Write(int) writes 4 bytes to the buffer. Most integers tend
            // to not make use of all 4 bytes, in which case it will be more
            // space efficient to use WritePackedUInt32(uint) since it does a
            // bit of clever compression internally. However, if the value
            // is greater than 16777215u, it will end up writing 5 bytes,
            // in which case Write(int) is more preferred.
            writer.WritePackedUInt32((uint)var1);
            SerializeVar2(writer);
            return true;
        }

        writer.WritePackedUInt32(syncVarDirtyBits);
        if ((syncVarDirtyBits & var1DirtyBit) != 0u)
        {
            writer.WritePackedUInt32((uint)var1);
        }
        if ((syncVarDirtyBits & var2DirtyBit) != 0u)
        {
            SerializeVar2(writer);
        }
        return syncVarDirtyBits != 0u;
    }

    public override void OnDeserialize(NetworkReader reader, bool initialState)
    {
        if (initialState)
        {
            var1 = (int)reader.ReadPackedUInt32();
            DeserializeVar2(reader);
            return;
        }

        uint dirtyBits = reader.ReadPackedUInt32();
        if (dirtyBits & var1DirtyBit) != 0u)
        {
            var1 = (int)reader.ReadPackedUInt32();
        }
        if (dirtyBits & var2DirtyBit) != 0u)
        {
            DeserializeVar2(reader);
        }
    }
}
```

### How to patch

**As a PostBuild-Event**

- Download the [archive](https://github.com/user-attachments/files/17396610/NetworkWeaver.zip) that contains the UnetWeaver executable.
- You can set it up how you want, but for now we'll assume that you extracted it in a folder called `NetworkWeaver` in your project root and your libraries are there too in a `libs` folder.
- In your project's properties, create the following postbuild event
  - `REM TEXT` is a comment. Batch is not the best for comments, but it'll keep it copypastable.
  - The wiki also has the [Build Events](https://github.com/risk-of-thunder/R2Wiki/wiki/Build-Events) page for more information on these.

    ```bat
    REM First we copy the from the output folder to the NetworkWeaver folder.
    REM We store the output from robocopy in a file called robocopy which we'll delete at the end of the file.
    robocopy "$(TargetDir) " "$(ProjectDir)NetworkWeaver" "$(TargetFileName)" > "$(TargetDir)Robocopy"
    robocopy "$(TargetDir) " "$(ProjectDir)NetworkWeaver" "$(TargetName).pdb" > "$(TargetDir)Robocopy"

    REM Then we navigate our script to the NetworkWeaver folder to make the follow up line less verbose.
    cd "$(ProjectDir)\NetworkWeaver"

    REM Unity.UNetWeaver.exe {path to Coremodule} {Path to Networking} {Path to output folder} {Path to the dll you want patched} {Path to all needed references for the to-be-patched dll}
    Unity.UNetWeaver.exe "../libs/UnityEngine.CoreModule.dll" "../libs/com.unity.multiplayer-hlapi.Runtime.dll" "Patched/"  $(TargetFileName) "$(ProjectDir)/libs"

    REM We store the prepatched dll disabled as a .prepatch so that you can check it if you want, but first we need to make sure that file doesn't exist already.
    IF EXIST "$(TargetFileName).prepatch" (
    DEL /F "$(TargetFileName).prepatch"
    )
    IF EXIST "$(TargetFileName).prepatch" (
    DEL /F "$(TargetFile).pdb.prepatch"
    )

    REM Then we rename the prepatched file to bla.dll.prepatch
    ren "$(TargetFileName)" "$(TargetFileName).prepatch"
    ren "$(TargetName).pdb" "$(TargetName).pdb.prepatch"

    REM move our script to the Patched Folder
    cd Patched

    REM move from the current directory (.) to the projectDirectory and store the output once more to the Robocopy file.
    robocopy "." "$(ProjectDir) " "$(TargetFileName)" > "$(TargetDir)Robocopy"
    robocopy "." "$(ProjectDir) " "$(TargetName).pdb" > "$(TargetDir)Robocopy"

    REM Remove the patched file from the Patched folder, because the Weaver won't run if the file already exists.
    del "$(TargetFileName)"
    del "$(TargetName).pdb"

    REM Delete the holder for all robocopy output.
    del "$(TargetDir)Robocopy"
    ```

- If everything went correctly, your patched plugin.dll will be in your project root whenever you build. (So next to your .csproj file.)

**As a Patcher**

- Download the [archive](https://github.com/risk-of-thunder/R2Wiki/files/14428540/UNetWeaver.zip) that contains the BepinEx Patcher UNet Weaver.
- Extract it in your `Risk of Rain 2\BepInEx\patchers` folder.
- If everything went correctly, your assembly will be patched when loading the game.
- This automates the need of pointing the weaver towards the right libs, as they will all be loaded by BepInEx already.
- Note that this is **run once** on your (the developer's) computer. After this you will have a patched plugin that you can distribute without the patcher.
- Yes, this is essentially using Risk of Rain 2 as a plugin builder.

If you feel like it, you can see what the Weaver changed in your assembly by opening it with dnSpy and looking at the networking classes you had. New generated methods and fields should be in them.
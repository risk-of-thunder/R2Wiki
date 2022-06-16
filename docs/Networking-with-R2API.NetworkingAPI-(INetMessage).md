## Step 1: Add Submodule Dependency
Add an `R2APISubmoduleDependency` attribute to your `BaseUnityPlugin`. Add `nameof(NetworkingAPI)` as a parameter.
```c#
// some usings...
namespace MyNamespace.Something
{
    // some other attributes...
    [R2APISubmoduleDependency(nameof(NetworkingAPI), nameof(SomeOtherAPI), nameof(ThatYouCanAddAPI)]
    public class MyModPlugin : BaseUnityPlugin
    {
        // some code...
    }
    
    // some other classes...
}
```

## Step 2: Implement `INetMessage`
Somewhere in your mod, you add a `using R2API.Networking.Interfaces;` in the file where you will implement the `INetMessage` interface. You make an empty class in the same file and implement the interface.
```c#
using R2API.Networking.Interfaces;
// other code
public class SyncSomething : INetMessage
{
  
}
```

## Step 3: Add the required override methods for the `INetMessage` interface
If you use Visual Studio, you can just hover over `INetMessage`, then click show potential fixes. After that, click `Implement Interface`.

![Implement Interface 1](https://i.imgur.com/KLEN0pu.png)

![Implement Interface 2](https://i.imgur.com/Fbb0omK.png)

The class should now look like this.
```c#
public class SyncSomething : INetMessage
{
        public void Deserialize(NetworkReader reader)
        {
            throw new System.NotImplementedException();
        }

        public void OnReceived()
        {
            throw new System.NotImplementedException();
        }

        public void Serialize(NetworkWriter writer)
        {
            throw new System.NotImplementedException();
        }
}
```

## Step 4: Determine which needs to be in sync
Now you must know what values must sync. In this example, we will play a sound on a player character for the clients. That means I need the `CharacterBody` of the player character. You need to send the `NetworkInstanceId` of the `CharacterBody.gameObject`. For the sake of demonstration, let's sync a bunch of useless stuff.

## Step 5: Declare the variables then code in `Serialize` and `Deserialize`
Now we know that we will sync the `Vector3 position`, the useless `int number` and the `NetworkInstanceId netId` of the `CharacterBody.gameObject`. Declare them in the class that implemented the interface.
```c#
public class SyncSomething : INetMessage
{
        NetworkInstanceId netId;
        Vector3 position;
        int number;

        // the methods...
}
```
Then you implement `Serialize`. `Serialize` is the method that will write the variables into the network coming from the caller of the machine. It will be used to send to the target clients or server. Order of writing them is important!
```c#
public class SyncSomething : INetMessage
{
        // other code
        public void Serialize(NetworkWriter writer)
        {
            writer.Write(netId);
            writer.Write(position);
            writer.Write(number);
        }
}
```
Then you implement `Deserialize`. `Deserialize` is the method that handles how to read the data that was received on clients. The order of reading should be the same as the order of how it was written.
```c#
public class SyncSomething : INetMessage
{
        // variable declarations

        public void Deserialize(NetworkReader reader)
        {
            netId = reader.ReadNetworkId();
            position = reader.ReadVector3();
            number = reader.ReadInt32();
        }

        // other code
}
```
The class should now look like this.
```c#
public class SyncSomething : INetMessage
{
        NetworkInstanceId netId;
        Vector3 position;
        int number;

        public void Deserialize(NetworkReader reader)
        {
            netId = reader.ReadNetworkId();
            position = reader.ReadVector3();
            number = reader.ReadInt32();
        }

        public void OnReceived()
        {
            throw new System.NotImplementedException();
        }

        public void Serialize(NetworkWriter writer)
        {
            writer.Write(netId);
            writer.Write(position);
            writer.Write(number);
        }
}
```

## Step 6: Implement the logic in `OnReceived`
You put the logic in `OnReceived`. The receiver will fire this method after deserializing. You can use the declared variables like normal. Here is a sample code.
```c#
    public class SyncSomething : INetMessage
    {
        NetworkInstanceId netId;
        Vector3 position;
        int number;

        public void Deserialize(NetworkReader reader)
        {
            netId = reader.ReadNetworkId();
            position = reader.ReadVector3();
            number = reader.ReadInt32();
        }

        public void OnReceived()
        {
            if (NetworkServer.active)
            {
                MyModPlugin.myLogger.LogMessage("SyncSomething: Host ran this. Skip.");
                return;
            }
            Chat.AddMessage($"Client received SyncSomething. Position received is {position}. Number received is {number}.");
            GameObject bodyObject = Util.FindNetworkObject(ownerBodyId);
            if (!bodyObject)
            {
                MyModPlugin.myLogger.LogWarning("SyncSomething: bodyObject is null.");
                return;
            }
            Util.PlaySound("somevanillasoundstring", bodyObject);
        }

        public void Serialize(NetworkWriter writer)
        {
            writer.Write(netId);
            writer.Write(position);
            writer.Write(number);
        }
    }
}
```

## Step 7: Code in the Constructors of the class
The constructors are also required for registering your Network Message and for actually using it. You need to make two: one with no arguments, and one with all the arguments based on the declared variables.
```c#
public class SyncSomething : INetMessage
{
        NetworkInstanceId netId;
        Vector3 position;
        int number;

        public SyncSomething()
        {
        }

        public SyncSomething(NetworkInstanceId netId, Vector3 position, int num)
        {
            this.netId = netId;
            this.position = position;
            number = num;
        }

        // other methods
}
```

And with that, you should have a working `INetMessage` class.

## Step 8: Register your Network Message
You need to register your `INetMessage` first before actually using it. You can do this in your mod plugin's `Awake`.
```c#
// some usings...
namespace MyNamespace.Something
{
    // some attributes
    public class MyModPlugin : BaseUnityPlugin
    {
        private void Awake()
        {
            // some code...
            NetworkingAPI.RegisterMessageType<SyncSomething>();
            // more code...
        }
        // other methods...
    }
    
    // some other classes...
}
```

## Step 9: Calling the class to trigger a sync
Let's imagine we have a method where only the server fires the code.
```c#
// Somewhere...
public void PlaySoundInServer(CharacterBody body)
{
    Util.PlaySound("somesoundstring", body.gameObject);

    NetworkIdentity identity = body.gameObject.GetComponent<NetworkIdentity>();
    if (!identity)
    {
        MyModPlugin.myLogger.LogWarning("PlaySoundInServer: The body did not have a NetworkIdentity component!");
        return;
    }
    new SyncSomething(identity.netId, body.gameObject.transform.position, 9000).Send(NetworkDestination.Clients);
}
```

## Step 10: Pat yourself in the back
You managed to sync that random sound... or whatever! Check the console for errors if it fails to sync in-game. Logs will extremely help you.
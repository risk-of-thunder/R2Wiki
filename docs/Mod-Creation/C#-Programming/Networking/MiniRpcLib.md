# MiniRpcLib

## NOWADAYS ITS HIGHLY RECOMMENDED TO USE UNETWEAVER INSTEAD

### AVAILABLE HERE

## FIXME: LINK: https://github.com/risk-of-thunder/R2Wiki/wiki/Networking-with-Weaver:-The-Unity-Way

[MiniRpcLib](https://github.com/wildbook/R2Mods/blob/develop/) is a Remote Procedure Call Library implemented by [Wildbook](https://github.com/wildbook), for RoR2. Wildbook has created a [Demo/tutorial](https://github.com/wildbook/R2Mods/blob/develop/MiniRpcDemo/MiniRpcDemo.cs) so please do check this out first.

### Implementing Update() network communication with MiniRPCLib

MiniRPCLib is a fantastic utility and really makes it easy to to manipulate networked clients, however there are some shortcomings and drawbacks. One such issue is invoking client methods every Update(), which is perfectly fine for a client which possesses your mod, however for a vanilla client/client without your mod, invoking a method every update causes sever network desync for them due to unexpected/dropped packets. The solution to this is to implement a "Ping"/Check if the client responds before we continue to update them with data. A working example can be found [here.](https://github.com/Paddywaan/CorpseBloomPlusPlus)

```csharp
private Dictionary<NetworkInstanceId, bool> clientHasMod = new Dictionary<NetworkInstanceId, bool>();
private Dictionary<NetworkInstanceId, bool> clientIsPinged = new Dictionary<NetworkInstanceId, bool>();
public IRpcFunc<bool, bool> clientPingCheck { get; set; }
public Constructor()
{
	clientPingCheck = miniRpc.RegisterFunc<bool, bool>(Target.Client, (user, x) =>
	{
		Debug.Log($"[Client] HOST sent us: {x}, returning true");
		return true;
		//return $"Hello from the server, received {x}!";
	}
}
public void Update()
{
	if (NetworkServer.active)
	{
		foreach (NetworkUser nu in NetworkUser.readOnlyInstancesList)
		{
			if (nu.GetCurrentBody() != null && nu.GetCurrentBody().healthComponent.alive)
			{
				if (clientHasMod.ContainsKey(nu.GetCurrentBody().netId) && clientHasMod[nu.GetCurrentBody().netId])
				{
					updateClient.Invoke(args, nu); //update client
				}
				else //client has not been pinged
				{
					if (!clientIsPinged.ContainsKey(nu.GetCurrentBody().netId))
					{
						Debug.Log("[HOST]Sending PingCheck");
						clientIsPinged.Add(nu.GetCurrentBody().netId, true); //ping client
						clientPingCheck.Invoke(false, result =>
						{
							if(result)
							{
								clientHasMod.Add(nu.GetCurrentBody().netId, true); //client is modded
								Debug.Log($"[HOST] Received response from {nu.GetCurrentBody().netId}: {result}, player has mod.");
							}
							else
							{
								Debug.Log($"[HOST] Received response from {nu.GetCurrentBody().netId}: {result}, player is not modded.");
								clientHasMod.Add(nu.GetCurrentBody().netId, false); //client is vanilla
							}
						}, nu);
					}
				}
			}
		}
	}
}
```

# Tips and Tricks

### Existing Methods
RoR2.Util has a lot of existing helping methods such as CheckRoll, ApplySpread, QuaternionSafeLookRotation, and PlaySound. Consider taking a look there before making any existing methods.

### Iterate through all living entities
```csharp
foreach (var characterBody in CharacterBody.readOnlyInstancesList)
{ 
    // characterBody is a living entity (like a monster, or a player / survivor body)
}
```
### PlayerCharacterMasterController are objects that hold information about a player that played during this run (still there even when the player get disconnected)
```csharp
var instances = PlayerCharacterMasterController.instances;
foreach (var playerCharacterMaster in PlayerCharacterMasterController.instances)
{
    //You can get the master via playerCharacterMaster.master
    //and the body via playerCharacterMaster.master.GetBody()
}
```
### Get reference to local players (split-screen is a thing)

```csharp
var localPlayers = LocalUserManager.readOnlyLocalUsersList;
```

### GodMode for players

```csharp
On.RoR2.HealthComponent.TakeDamage += (orig, self, damageInfo) => {
    var charComponent = self.GetComponent<CharacterBody>();
    if (charComponent != null && charComponent.isPlayerControlled)
    {
        return;
    }
    orig(self, damageInfo);
};
```

### Toggle the mouse
```csharp
using R2API.Utils;

public int ToggleCursor()
{
    var pes = MPEventSystemManager.primaryEventSystem;
    pes.cursorOpenerCount = pes.cursorOpenerCount > 0 ? 0 : 1;
    return pes.cursorOpenerCount;
}
```

### Broadcast chat message:

```csharp
Chat.SendBroadcastChat(new SimpleChatMessage { baseToken = "<color=#e5eefc>{0}: {1}</color>",  paramTokens = new [] { "SOME_USERNAME_STRING", "SOME_TEXT_STRING" } })
```

If you want to send something custom

```csharp
Chat.SendBroadcastChat(new SimpleChatMessage { baseToken = "<color=#e5eefc>{0}</color>",  paramTokens = new [] { "SOME_TEXT_STRING" } })
```
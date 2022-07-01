# Tips and Tricks

### Existing Methods
RoR2.Util has a lot of existing helping methods such as CheckRoll, ApplySpread, QuaternionSafeLookRotation, and PlaySound. Consider taking a look there before making any existing methods.

### Iterate through all players
```C#
for (int i = 0; i < CharacterMaster.readOnlyInstancesList.Count; i++)
{ 
    //CharacterMaster.readOnlyInstancesList[i] is the player.
}
```
or
```C#
var instances = PlayerCharacterMasterController.instances;
foreach (PlayerCharacterMasterController playerCharacterMaster in instances)
{
    //You can get the master via playerCharacterMaster.master
    //and the body via playerCharacterMaster.master.GetBody()
}
```
### Get reference to local player

```C#
PlayerCharacterMasterController.instances[0].master
```

### GodMode for players

```C#
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
```C#
using R2API.Utils;

public int ToggleCursor()
{
    var pes = MPEventSystemManager.primaryEventSystem;
    pes.cursorOpenerCount = pes.cursorOpenerCount > 0 ? 0 : 1;
    return pes.cursorOpenerCount;
}
```

### Redirect the log output from the logfile to the game console
```C#
On.RoR2.RoR2Application.UnitySystemConsoleRedirector.Redirect += orig => { };
```

### Broadcast chat message:

```C#
Chat.SendBroadcastChat(new SimpleChatMessage { baseToken = "<color=#e5eefc>{0}: {1}</color>",  paramTokens = new [] { "SOME_USERNAME_STRING", "SOME_TEXT_STRING" } })
```

If you want to send something custom

```C#
Chat.SendBroadcastChat(new SimpleChatMessage { baseToken = "<color=#e5eefc>{0}</color>",  paramTokens = new [] { "SOME_TEXT_STRING" } })
```
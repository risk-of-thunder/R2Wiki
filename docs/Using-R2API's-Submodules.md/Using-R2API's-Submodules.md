Since the **Hidden Realms** update of all of R2API's submodules need to be requested before they are loaded. This is to contain R2API errors that leave the game unplayable. Here's how to request them:

```cs
...
Using R2API.Utils;
...

namespace YourNameSpace
{
    [R2APISubmoduleDependency("api1name","apiname2")]//You would replace api1name with something like "SurvivorAPI".
    [BepInPlugin(GUID, Name, Version)]
    [BepInDependency(R2API.R2API.PluginGUID)]//This isn't necessary for the request for submodules, but you are dependent on R2API otherwise you wouldn't request a R2API submodule..
    public class YourPlugin: BaseUnityPlugin
    {
        etc.
    }
}
```
However, since typos are easy to make consider using the `nameof()` function to have your IDE check if it's a valid name.
For example: `[R2APISubmoduleDependency(nameof(SurvivorAPI))]`. Notice how there's no explicit string in there anymore.

Consider checking out the [R2API wiki](https://github.com/risk-of-thunder/R2API/wiki) for more detailed information about it's submodules.
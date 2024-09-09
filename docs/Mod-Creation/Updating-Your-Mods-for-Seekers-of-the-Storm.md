report on discord [here](https://discord.com/channels/562704639141740588/1279030064192950382) if you find anything else, please and thanks

## Before Updating Libraries
there is a namespace that begins `Assets.RoR2`

if you have a class named `Assets` (all henry survivors will), code attempting to call this class will be intercepted by that namespace. Before updating. Rename `Assets` class to  just `Asset` or `ModAssets` or something

## Update Your Projects
- in visual studio, right click your csproj > properties > General > set Target Framework to 2.1 if it isn't already
- I'm copypasting this to update my nuget libraries
```xml
        <PackageReference Include="BepInEx.Core" Version="5.*" />
        <PackageReference Include="RiskOfRain2.GameLibs" Version="1.3.2-r.1" />
        <PackageReference Include="UnityEngine.Modules" Version="2021.3.33" />
        <PackageReference Include="MMHOOK.RoR2" Version="2024.9.5">
            <NoWarn>NU1701</NoWarn>
        </PackageReference>
```
- update your unity project to version 2021.3.33
- update your wwise project to v2023.1.4.8496 and rebuild your soundbanks
  - I had issues loading the soundbank through code, so I just cut that code and renamed my soundbank from .bnk to .sound and let r2api do it
- if your weaver doesn't weave, make sure your post build is targeting the right destination (2.1 folder instead of 2.0) and throw [this dll](https://cdn.discordapp.com/attachments/1279030064192950382/1279663585189953577/netstandard.dll?ex=66d54315&is=66d3f195&hm=aabf2c9d2c7931a1fd4e044a960e0f755e2a3d0fd461c68453e9376855b3894d&) in your libs folder haha woops who put that there 

## Code Changes
- `TemporaryOverlay` changed to `TemporaryOverlayInstance`
  - instead of `TemporaryOverlay overlay = someModelGameObject.AddComponent<TemporaryOverlay>();`
    - `TemporaryOverlayInstance temporaryOverlayInstance = TemporaryOverlayManager.AddOverlay(someModelGameObject);`
  - rest of the fields are generally the same, sans `AddToCharacerModel` being corrected
  - instead of destroying the component, now do `if(temporaryOverlayInstance != null) temporaryOverlayInstance.Destroy()`
- Achievements now give lunar coins. `RegisterAchievementAttribute` now has a field for lunar coin rewards
  - mastery skins give 10
  - some skill achievements give 3, some 5. check the game's achievements for more examples
  - On that, UnlockableAPI hasn't been updated to work with this. I believe it's ripperino for that. you should update your achievements to just use the registerachievementattribute. [Henry Tutorial](https://github.com/ArcPh1r3/HenryTutorial/wiki/Tutorial#6-unlockables-and-achievements) has a good way of doing this.
- a lot of catalog.init functions have been changed to be coroutines returning `IEnumerator` instead of `void`. do `yield return Orig()` instead of just `orig()`
- `DamageType` changed to `DamageTypeCombo` (due to the addition of `DamageTypeExtended` which is just `DamageType` again cause they ran out of room for more damagetypes)
  - This doesn't affect r2api damageapi. that should be working the same. if you are seeing issues with that, report them
- `BulletAttack.BulletHit` is pooled now. If, for whatever reason, you were instantiating any directly (with e.g. = default(BulletHit)), it'll now cause some weird NREs -- capable of, among other things, completely killing the BepInEx logger if you try to log it directly. 
  - This needs to be replaced with BulletAttack.GetBulletHit(), and probably something to clear the pool manually later if you aren't doing stuff during existing BulletAttack code

## Character Stuff
- some Effects and ProjectileGhosts are pooled now. 
  - if your projectiles aren't set up for this, I just did a `projectileController.ghostPrefab.AddComponent<VFXAttributes>().DoNotPool = true;` to avoid it for now
  - I would highly suggest looking into adding an `EffectManagerHelper` component to pool your effects. pool is cool. I will update this with more detail when I do it myself
- in `HenryMod.Modules.Prefabs`, change `characterModel.temporaryOverlays = new List<TemporaryOverlayInstance>();`
- if you set up your `KinematicCharacterController` yourself, set the `playerCharacter` value to true for survivors.
  - Henry clones commando's body for his setup so you're likely not to need to

## Misc
- If your mod is translated, in addition to `es-ES` for Spanish (Spain), there is now `es-419` for Spanish (Mexico)
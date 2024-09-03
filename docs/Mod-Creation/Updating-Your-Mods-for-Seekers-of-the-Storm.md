report on discord [here](https://discord.com/channels/562704639141740588/1279030064192950382) if you find anything else, please and thanks

## before updating libraries
there is a fucking `Assets.RoR2` namespace

if you have a class named `Assets` (all henry survivors will), code attempting to call this class will be intercepted by that namespace. Before updating. Rename `Assets` class to  just `Asset` or `MyAssets` or something

## update your projects
- in visual studio, right click your csproj > properties > General > set Target Framework to 2.1 if it isn't already
- I'm copypasting this to update my nuget libraries
```xml
        <PackageReference Include="BepInEx.Core" Version="5.*" />
        <PackageReference Include="RiskOfRain2.GameLibs" Version="1.3.1.275-r.0" />
        <PackageReference Include="UnityEngine.Modules" Version="2021.3.33" />
        <PackageReference Include="MMHOOK.RoR2" Version="2024.8.28">
            <NoWarn>NU1701</NoWarn>
        </PackageReference>
```
- update your unity project to version 2021.3.33
- update your wwise project to v2023.1.4.8496 and rebuild your soundbanks
  - I had issues loading the soundbank through code, so I just cut that code and renamed my soundbank from .bnk to .sound and let r2api do it
- if your weaver doesn't weave, make sure your post build is targeting the right destination (2.1 folder instead of 2.0) and throw [this dll](https://cdn.discordapp.com/attachments/1279030064192950382/1279663585189953577/netstandard.dll?ex=66d54315&is=66d3f195&hm=aabf2c9d2c7931a1fd4e044a960e0f755e2a3d0fd461c68453e9376855b3894d&) in your libs folder haha woops who put that there 

## code changes
- places that used `TemporaryOverlay` might need to be changed to `TemporaryOverlayInstance` instead
- Achievements now give lunar coins. `RegisterAchievementAttribute` now has a field for lunar coin rewards
  - mastery skins give 10
  - some skill achievements give 3, some 5. check the game's achievements for more examples
  - On that, UnlockableAPI hasn't been updated to work with this. I believe it's ripperino for that. you should update your achievements to just use the registerachievementattribute. [Henry Tutorial](https://github.com/ArcPh1r3/HenryTutorial/wiki/Tutorial#6-unlockables-and-achievements) has a good way of doing this.
- a lot of catalog.init functions have been changed to be coroutines returning `IEnumerator` intead of `void`. I'm not smart enough to summarize but there's discussion about it [here](https://discord.com/channels/562704639141740588/562704639569428506/1279478137004228650)
  - long story short, `yield return Orig()`
- `DamageType` changed to `DamageTypeCombo` (due to the addition of `DamageTypeExtended` which is just `DamageType` again cause they ran out of room for more damagetypes
  - This doesn't affect r2api damageapi. that should be working the same. if you are seeing issues with that, report them

## character stuff
- some Effects and ProjectileGhosts are pooled now. 
  - if your projectiles aren't set up for this, I just did a `projectileController.ghostPrefab.AddComponent<VFXAttributes>().DoNotPool = true;` to avoid it for now
  - I would highly suggest looking into adding an `EffectManagerHelper` component to pool your effects. pool is cool. I will update this with more detail when I do it myself
- in `HenryMod.Modules.Prefabs`, change `characterModel.temporaryOverlays = new List<TemporaryOverlayInstance>();`
- if you set up your `KinematicCharacterController` yourself, set the `playerCharacter` value to true for survivors.
  - Henry clones commando's body for his setup so you're likely not to need to
- keep an eye out for any of your skills that may have "landing" behavior. changes to updates have made detecting this from entitystates inconsistent
- Chronic Expansion has code related for the item display added to CharacterBody. There will be buff related errors if your character doesn't have an item display for it. 
  - Either add an item display, or rely on the FixInvincibleMithrix mod.
  - hopefully I remove this section soon as gearbox fixes it

## the dreaded deltatime
you are verly likely going to be impacted by the framreate issues of the update/fixedupdate changes. There are a few things you can do to combat this.
- in your entitystates, change any `fixedDeltaTime`s in your `FixedUpdate`s to be `deltaTime` instead.
- if you have any custom `SkillDefs` with `OnFixedUpdate` code, do the same there

If they end up reverting their changes and bringing these back to FixedUpdate, `deltaTime` will still return the same as `fixedDeltaTime`, so it should be good

## misc
- If your mod is translated, in addition to `es-ES` for Spanish (Spain), there is now `es-419` for Spanish (Mexico)
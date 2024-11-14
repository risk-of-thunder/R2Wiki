report on discord [here](https://discord.com/channels/562704639141740588/1279030064192950382) if you find anything else, please and thanks

## General
### Before Updating Libraries
there is a namespace that begins `Assets.RoR2`

if you have a class named `Assets` (all henry survivors will), code attempting to call this class will be intercepted by that namespace. Before updating. Rename `Assets` class to  just `Asset` or `ModAssets` or something

### Update Your Projects
- in visual studio, right click your csproj > properties > General > set Target Framework to 2.1 if it isn't already
- I'm copypasting this to update my nuget libraries
```xml
        <PackageReference Include="BepInEx.Core" Version="5.*" />
        <PackageReference Include="RiskOfRain2.GameLibs" Version="1.3.5-r.1" />
        <PackageReference Include="UnityEngine.Modules" Version="2021.3.33" />
        <PackageReference Include="MMHOOK.RoR2" Version="2024.9.5">
            <NoWarn>NU1701</NoWarn>
        </PackageReference>
```
- update your unity project to version 2021.3.33
- update your wwise project to v2023.1.4.8496 and rebuild your soundbanks
  - I had issues loading the soundbank through code, so I just cut that code and renamed my soundbank from .bnk to .sound and let r2api do it
- if your weaver doesn't weave, make sure your post build is targeting the right destination (2.1 folder instead of 2.0) and throw [this dll](https://github.com/user-attachments/files/17725171/netstandard.zip) in your libs folder haha woops who put that there 

### Code Changes
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
- function `GenericSkill.OnFixedUpdate` now has a `float deltaTime` parameter
- `EntityStateCatalog.InstantiateState` now needs `ref` before the `SerializableEntityStateType` parameter

### Character Stuff
- some Effects and ProjectileGhosts are pooled now. 
  - if your projectiles aren't set up for this, I just did a `projectileController.ghostPrefab.AddComponent<VFXAttributes>().DoNotPool = true;` to avoid it for now
  - I would highly suggest looking into adding an `EffectManagerHelper` component to pool your effects. pool is cool. I will update this with more detail when I do it myself
- if you set up your `KinematicCharacterController` yourself, set the `playerCharacter` value to true for survivors.
  - Henry clones commando's body for his setup so you're likely not to need to

### Misc
- If your mod is translated, in addition to `es-ES` for Spanish (Spain), there is now `es-419` for Spanish (Mexico)

## How to upgrade a 2019.4 Thunderkit project to 2021.3.33

1. Create a new branch for the upgrade process, you'll want this in case you fuck something up.
2. Open your project on the 2019.4 version of unity
3. Ensure no errors are present, push your commit to your new branch
4. proceed to open your project on 2021.3  
``If you get a message regarding the project having compilation errors and wanting to init the project in safe mode, do not enter safe mode, it disables thunderkit and we'll need it to reimport the game.``
5. Once your project is open, close it and browse to your "Packages" folder on the project root
6. Open your ``manifest.json`` file.
7. Update any and all GIT dependencies on your project. bellow are the current latest versions of the common git projects used for modding  
    - ThunderKit - 9.0.0 or Greater  
    - Risk of thunder multiplayer HLAPI  
    - 1.1.1 or Greater  
    - RoR2EditorKit - 5.2.1 or Greater  
    - RoR2ImportExtensions - 1.7.0  
    - MSU - 2.0.0 or Greater (if used)
8. On the same folder where the manifest.json is located, delete the packages-lock.json file.
9. Once this is done, select every folder inside "Packages" and delete them, we're going to reimport the game.
10. Open the Project, you may get prompted to initialize R2EK, up to you if you initialize it now or later.
11. on your "ThunderKitSettings" folder, delete the following if present:
      * MaterialEditorSettings
      * R2EditorSettings
      * RoR2EditorKitSettings
      * ShaderDictionary
12. Open your Thunderkit settings window, configure your import configuration and import.
    * If using MSU, make sure that the WWise BlackLister is ENABLED, this will save us time for later

13. Post import, if youre using WWIse, follow the following lettered steps, otherwise, skip to 14
    a. If using the WWIse integration, you'll want to delete the WWise assemblies imported by the game, which are the following:

          * Ak.Wwise.API.WAAPI
          * AK.Wwise.Unity.API
          * AK.Wwise.Unity.API.WwiseTypes
          * AK.Wwise.Unity.MonoBehaviour
          * AK.Wwise.Unity.Timeline
          * Note: if you where using MSU and you had the wwise blacklister enabled, these assemblies where already ommited during import process.
    b. Once you ensure only the WWise AssemblyDef and WWise Editor assembly defs are present, fix any and all compilation issues.
    c. Once your project is free of compilation issues, travel to your mod's AssemblyDef, if the "useGuids" option on the assembly definition references is toggled, untoggle it.
    d. Open your assemblyDef on a text editor, make sure the strings inside the "references" array has the following entries. do not remove "Wwise", as otherwise the integration step update will fail
          * "Ak.Wwise.Api.WAAPI",
          * "AK.Wwise.Unity.API",
          * "AK.Wwise.Unity.API.WwiseTypes",
          * "AK.Wwise.Unity.MonoBehaviour",
          * "AK.Wwise.Unity.Timeline"
    e. Once you've ensured no errors remain, close your project and open the wwise launcher.
    f. ensure you have version ``2023.1.4.8496`` of Wwise installed.
    g. on the "Recent unity projects" tab, locate your project, clik "Upgrade WWise in project"
    h. Ensure the settings are correct, and then click "Upgrade" (or integrate, i forgot the exact word)
    i. The wwise launcher will proceed to upgrade your project to 2023.1.4 version of wwise, this will also keep any script references to your project's assets. Its important that there where no compilation errors because otherwise WWise fails to integrate.
    j. Once you finish integration, click the wwise icon shown on the image below
    k. You'll be prompted to upgrade your wwise project to the specific version, proceed with the upgrade.
14. once import is complete, you should be good to go. if you used the "LanguageTree" datum from R2EK, your manifest is going to have invalid entries in it. you can either remake your manifest or fix it yourself.
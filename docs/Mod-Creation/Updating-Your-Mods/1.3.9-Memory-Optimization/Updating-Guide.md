## Nuget
```
<PackageReference Include="BepInEx.Core" Version="5.*" />
<PackageReference Include="UnityEngine.Modules" Version="2021.3.33" />
<PackageReference Include="RiskOfRain2.GameLibs" Version="1.3.9-r.0" />
<PackageReference Include="MMHOOK.RoR2" Version="2025.6.3" NoWarn="NU1701" />
<PackageReference Include="RoR2BepInExPack" Version="1.30.0" />
```

## Referencing Game Assets
You'll notice a new face, RoR2BepInExPack. This has updated with a new namespace `RoR2BepInExPack.GameAssetPaths`, which is very rad.

### TL;DR: 
instead of
```
Addressables.LoadAssetAsync<GameObject>("RoR2/Junk/Commando/TracerBarrage.prefab");
```
We highly recommend doing
```
Addressables.LoadAssetAsync<GameObject>(RoR2BepInExPack.GameAssetPaths.RoR2_Junk_Commando.TracerBarrage_prefab);
```
### The Benefits
1. Autocomplete, baybee. 
1. If the path updates, **your reference will no longer break**. This is because the field in RoR2BepInExPack references the GUID, which is an unchanging identifier of the asset

### Tips for Autocomplete
- Start with `RoR2BepInExPack.GameAssetPaths` and type the category of what you're looking for  
so say I'm looking for mul-T stuff:  
![image](https://github.com/user-attachments/assets/a6f008c2-d36b-4bd8-9813-84aaba97fabb)
- now if I want a skin or i can just type skin  
![image](https://github.com/user-attachments/assets/4946630c-02e2-41f1-b730-a8ab21883f49)
- Another example: say I want some material from Engineer stuff  
![image](https://github.com/user-attachments/assets/e6aaebcf-3f64-4b0a-9ac2-40393bb932d7)

### Long explanation

As the game updates, if you've been loading any game assets with addressables, you're bound to run into some issues with game paths changing, as the developers may move files to different folders or addressable groups.  
The solution? Use GUIDs instead of paths.
Unlike paths, GUIDs are identifiers for assets that don't change when names or paths of the assets change. Moving forward, the developers are including a big .json of all the GUIDs in the game, so we have generated a library of classes in RoR2BepInExPack using those guids that we can now use to reference.

## CharacterModel and Skin stuff
Read the changelog page for more information.

If you were ever loading a character and messing with their renderers, you will now find that they are empty. No reference to meshes or materials. They are now all stored in their default skin, which all characters (including enemies) must now have.  
Some really old mods, for example, were loading commando's material by loading commando, going into his renderers, and grabbing the material from there. What you want to do is load any materials you're messing with directly from code, using the method above.

**Creating skins in code should work just fine**. For custom characters, if you weren't depending on any existing materials, your skin should be fine. Reach out in the modding discord if you're having trouble with this, and we can update this paragraph.

If you are cloning characters such as enemies and making your own variations on them, keep in mind that their renderers are also empty. You should load their skindef and make changes to the rendererinfos there, or load their materials directly if you were cloning them, etc.

MonoBehaviours are components that are attached to Unity GameObjects and perform various functions. See our article about them [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Unity-and-MonoBehaviour/), and see Unity documentation about them [here](https://docs.Unity3d.com/Manual/ScriptingImportantClasses.html)

MonoBehaviours are most commonly used in prefabs, created in the Unity Editor. From here, this article will assume you know how to create prefabs, attach scripts to them, and build those prefabs into assetbundles. See [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Loading-Assets/)
to learn how to load those assetbundles into your mod.

If you are creating and loading prefabs for use in your mod, you have the ability to attach any of the game's MonoBehaviours, and any custom MonoBehaviours that you want to write as well. Refer to the respective sections on how to do each.
___

## Using RoR2 MonoBehaviours

### Option 0: AddComponent
Possibly the simplest way is to skip the editor and simply call AddComponent in your code at runtime.

This works perfectly fine, but does not scale, and doesn't take advantage of the editor. It can easily become tedious to the point of limiting.

### Option 1: Dummy Scripts
Preferred if you are only using a few scripts

#### Steps
1. Create a script with the same name as an existing RoR2 script.
1. Open this script, and make sure it has the same namespace as the in-game script you want to use.
2. Copy over any Serialized Fields in the script that you want to use in editor.
    - For example, for the class `RagdollController`, a script like this will suffice:  
    ```
    namespace RoR2 
    {
        public class RagdollController : MonoBehaviour
        {
		    public Transform[] bones;

		    public MonoBehaviour[] componentsToDisableOnRagdoll;
        }
    }
    ```
3. You can now attach it to your prefab and assign fields in editor.  
When built into an assetbundle and loaded into the game, RoR2 will recognize this script as its own.

That's it. pretty simple, but this doesn't really scale. You will have to do this with *every* script you want to use in editor.  
Consider the next option if you want to use many RoR2 scripts.

### Option 2: Importing all RoR2 scripts
Preferred if you are using many RoR2 scripts, namely if you are creating projectiles.

If you are currently creating your mod in code, and only using Unity to build assetbundles, you can gain access to all of the RoR2 components by importing the game into your project.  
#### Notes 
- This process uses Thunderkit, as it was built to do this, however you don't have to fully move to the thunderkit workflow of creating mods. You can continue creating your mod in code, and building your assetbundle as before.
- This will take some setup, but if you are using components a lot, and if you want to work with projectiles, it is definitely worth it.
- If you are creating a character using the Henry Template, refer to [this article](https://github.com/ArcPh1r3/HenryTutorial/wiki/Using-RoR2-Scripts-in-Unity-Editor-(Thunderkit-Import)).  

#### Steps
1. [See this guide](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Unity-Editor-Usage/ThunderKit/Crash-Course-and-Getting-Started/) all about how to create mods with thunderkit
1. Skip to steps 4 and 5 if you're not pursuing a full thunderkit setup. More on this below.
1. If you're building your mod in code, you may disable the following steps in the import process:
    - MMHook Generator
    - Ensure RoR2 Thunderstore Source
    - Install Bepinex
    - R2API Submodule Installer
    - Install RoR2MultiplayerHLAPI

#### If you are working with other people or in any case uploading your mod on github, make sure you .gitignore the RoR2 package. Otherwise, you are redistributing game assets and that's a big no-no.
Add this to the .gitignore:
```
#RoR2 Packages Folder
Packages/Risk of Rain 2
#ThunderKit root folder & Logs
ThunderKit/
Assets/ThunderKitSettings/Logs/
```
### Option 3: Just Use Thunderkit
If you are more Unity-minded, Thunderkit is a mod creating workflow where everything is in editor.

All objects, code, and mod creation happens in one place and you can simply attach any scripts as normal, RoR2 or custom, so you may be interested in that. However, it is not recommended for beginners, as it requires familiarity with Unity, ror2, and thunderkit itself.  

See [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Unity-Editor-Usage/ThunderKit/Crash-Course-and-Getting-Started/) to get started, and if you run into any issues pursuing this, reach out in the modding discord.
___ 

## Using Custom MonoBehaviours
If you try to write a component, attach it to your prefab, and build your bundle, the game will not recognize it. There are a few options to accomplish this

### Option 0: AddComponent
Possibly the simplest way is to skip the editor and simply call AddComponent in your code at runtime.

This works perfectly fine, but does not scale, and doesn't take advantage of the editor. It can easily become tedious to the point of limiting.

### Option 1: Dummy Scripts

1. Create a script with the same name and namespace as your MonoBehaviour class in your mod
    - You can likely just copy the whole code over, but if you can't, you only need the class name and any serialized fields you want to use in editor.
2. Put this script in a folder in your Unity project
2. Right click and create an Assembly Definition in this folder, with the same name as the .csproj of your mod

Now you can attach this script to your prefab and the game will recognize it. Have fun assigning things in the inspector instead of having to call AddComponent a million times!

Note: If your code runs into errors because it references other RoR2 code, you will have to have that code in your project as well. Follow the RoR2 section above to find out how to do this.

### Option 2: Whole Assembly in Unity
You can just take your whole mod dll and slap it in your Unity project.  
To do this, you will need to use Thunderkit to add RoR2 and all other dependencies of your mod to your Unity project.

In my experience, this just ended up slowing down my development, compared to Option 1. If you are considering this option because you are using a ton of components, it may be time to pursue a thunderkit setup

### Option 3: Just Use Thunderkit
If you are more Unity-minded, Thunderkit is a mod creating workflow where everything is in editor.

All objects, code, and mod creation happens in one place and you can simply attach any scripts as normal, RoR2 or custom, so you may be interested in that. However, it is not recommended for beginners, as it requires familiarity with Unity, ror2, and thunderkit itself.  

See [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Unity-Editor-Usage/ThunderKit/Crash-Course-and-Getting-Started/) to get started, and if you run into any issues pursuing this, reach out in the modding discord.

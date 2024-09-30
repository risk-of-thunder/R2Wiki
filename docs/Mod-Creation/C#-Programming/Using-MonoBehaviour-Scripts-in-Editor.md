MonoBehaviours are components that are attached to Unity GameObjects and perform various functions. See our article about them [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/C%23-Programming/Unity-and-MonoBehaviour/), and see Unity documentation about them [here](https://docs.unity3d.com/Manual/ScriptingImportantClasses.html)

MonoBehaviours are most commonly used in prefabs, created in the Unity Editor. If you are creating and loading prefabs for use in your mod, you have the ability to attach any of the game's MonoBehaviours, and any custom MonoBehaviours that you want to write as well.

# Using RoR2 MonoBehaviours
There are two ways to use RoR2 scripts

## Option 0: AddComponent
Possibly the simplest way is to simply call AddComponent in your code at runtime.

This works perfectly fine, but does not scale. It can easily become tedious to the point of limiting.

## Option 1: Dummy Scripts
Preferred if you are only using a few scripts

1. Create a script with the same name and namespace as an existing RoR2 script
2. fill this script out with any Serialized Fields in the script that you want to use in editor.
    i. this includes any any `public` field, and any field with the `[SerializedField]` attribute

That's it. pretty simple, but this doesn't really scale. You will have to do this with *every* script you want to use in editor.  
Consider the next option if you want to use many RoR2 scripts.

## Option 2: Importing all RoR2 scripts
Preferred if you are using many RoR2 scripts, namely if you are creating projectiles.

If you are currently creating your mod in code, and only using Unity to build assetbundles, you can use ThunderKit to import the RoR2 codebase into your unity project, and you will have access to all unity components.  

This will take some setup, but if you are using components a lot, and if you want to work with projectiles, it is definitely worth it.

If you are creating a character using the Henry Template, refer to [this article](https://github.com/ArcPh1r3/HenryTutorial/wiki/Using-RoR2-Scripts-in-Unity-Editor-(Thunderkit-Import)).  

1. [Here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Thunderkit/Getting-Started/) is a video guide on how to import thunderkit into unity.
1. Once RoR2 is imported, you can build your assetbundle and continue building your mod in code like normal. You don't have to worry about the other thunderkit-specific stuff, unless you want to go that route.

#### If you are working with other people or in any case uploading your mod on github, make sure you .gitignore the RoR2 package. Otherwise, you are redistributing game assets and that's a big no-no.

## Option Bonus: Just Use Thunderkit
If you are more unity-minded, Thunderkit is an mod creating workflow where everything is in editor.

All objects, code, and mod creation happens in one place and you can simply attach any scripts as normal, RoR2 or custom, so you may be interested in that. However, it is not recommended for beginners.  
See [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Thunderkit/Getting-Started/) to get started, and if you run into any issues pursuing this, reach out in the modding discord.

# Using Custom MonoBehaviours
If you try to write a component, attach it to your prefab, and build your bundle, the game will not recognize it. There are a few options to accomplish this

## Option 0: AddComponent
Possibly the simplest way is to simply call AddComponent in your code at runtime.  
This works perfectly fine, but does not scale. It can easily become tedious to the point of limiting.

## Option 1: Dummy Scripts
Any MonoBehaviour in your mod code can be attached to a prefab in unity.

1. Create a script with the same name and namespace as your MonoBehaviour class in your mod
    i. You can likely just copy the whole code over, but if you can't, you only need the class name and any serialized fields you want to use in editor.
2. Put this script in a folder in your Unity project
2. Right click and create an Assembly Definition in this folder, with the same name as the .csproj of your mod

Now you can attach this script to your prefab and the game will recognize it. Have fun assigning things in the inspector instead of having to call AddComponent a million times!

Note: If your code runs into errors because it references other RoR2 code, you will have to have that code in your project as well. Follow the RoR2 section below to find out how to do this.

## Option 2: Whole Assembly in Unity
You can just take your whole mod dll and slap it in your unity project.  
To do this, you will need to use Thunderkit to add RoR2 and all other dependencies of your mod to your unity project.

In my experience, this just ended up slowing down my development. The above process worked well for my purposes. If you are considering this option because you are using a ton of components, it may be time to pursue a thunderkit setup

## Option 3: Just Use Thunderkit
If you are more unity-minded, Thunderkit is an mod creating workflow where everything is in editor.

All objects, code, and mod creation happens in one place and you can simply attach any scripts as normal, so you may be interested in that. However, it is not recommended for beginners.  
See [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Thunderkit/Getting-Started/) to get started, and if you run into any issues pursuing this, reach out in the modding discord.

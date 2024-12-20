# Prefabs
From the Unity Documentation :
> Unity’s Prefab system allows you to create, configure, and store a GameObject complete with all its components, property values, and child GameObjects as a reusable Asset. \
The Prefab Asset acts as a template from which you can create new Prefab instances in the Scene.

RoR2 isn't an exception and uses Prefabs to load most of its game objects, from bodies of all entities (classes, monsters...) to projectiles, skills, and so on.

## Prefab text dump
[prefabs1.3.6.zip](https://github.com/user-attachments/files/18200287/prefabs1.3.6.zip)

ITS ACTUALLY A 7z FILE !!! Change extension from .zip to .7z and use 7zip or winrar for opening.

[Original dumper code](https://gist.github.com/xiaoxiao921/9d7f5aa50676b475f3cf750b5ef57955)

[Extended dumper code](https://gist.github.com/yekoc/d632468df9ebc53641170301ac2f67ef) with state configuration + expanded collection values

[Further extended dumper code](https://gist.github.com/SChinchi/7dd6b321807e9166b65705e6aec96634) including inherited fields/properties and some serialised classes/structs, such as DCCS categories

## Using a runtime inspector

Both the RuntimeInspector and UnityExplorer mods allow you to view and manipulate any loaded game objects in-game.

For a short-lived object, such as a projectile, freezing time is required to inspect it thoroughly. This can be achieved by either UnityExplorer's "Time" input field from the top bar, or by using DebugToolkit and its command `time_scale`, e.g., `time_scale 0`.

Keep in mind that with UnityExplorer you need to have its overlay disabled before entering the lobby or it will break the UI.

## Using Thunderkit

A more natural approach of reading the values of prefabs is by using [Thunderkit's Addressable Browser](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Thunderkit/Getting-Started/).
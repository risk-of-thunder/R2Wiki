# Items
***A detailed tutorial on how to make custom items for Risk of Rain 2.***

## Table of Contents

- ### [R2API](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#r2api-1)
	- #### [*ItemAPI*](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#itemapi-1)
	- #### [*ItemDropAPI*](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#itemdropapi-1)
	- #### [*ResourcesAPI*](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#resourcesapi-1)
- ### [Unity](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#unity-1)
- ### [C# BepinEx Plugin Code](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#c-bepinex-plugin-code-1)
---
## [R2API](https://github.com/risk-of-thunder/R2API)
**[R2API Wiki](https://github.com/risk-of-thunder/R2API/wiki)**

Two R2API submodules will be detailed through this tutorial, with one only briefly covered :
#### *Reminder about the R2API submodules :*
For a submodule to be loaded at the start of the game, and thus be properly used by your plugin code, don't forget to declare at the top of your class heriting from **BaseUnityPlugin** : 
The **`R2APISubmoduleDependency`** attribute, with, as parameters : the name of the submodules that you'll be using in your code. 
***A more complete example of that is available under the [C# BepinEx Plugin Code](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#c-bepinex-plugin-code-1) section.***

#### [ItemAPI](https://github.com/risk-of-thunder/R2API/blob/master/R2API/ItemAPI.cs)
allows for an easy way of adding custom items, equipments, buffs, and elite types to the game. We'll only focus on the item and equipment part of the submodule on this tutorial.

To add an item to the game, a mod maker will have to call the 
[ItemAPI.Add(CustomItem item)](https://github.com/risk-of-thunder/R2API/blob/master/R2API/ItemAPI.cs#L163) or its equipement counterpart, [ItemAPI.Add(CustomEquipment item)](https://github.com/risk-of-thunder/R2API/blob/master/R2API/ItemAPI.cs#L181) method.
But what is a `CustomItem` / `CustomEquipment`?

- #### CustomItem / CustomEquipment
	A `CustomItem` instance holds 2 variables : 
	- An `ItemDef` / `EquipmentDef`
	- An array of `ItemDisplayRule`
 
  `ItemDef` / `EquipmentDef`are classes from the game code, they basically hold all the needed informations for the game to define an item or an equipment, its name, its tier, the text that will show up when you look at its logbook entry, and so on.
  `ItemDisplayRule` allows to define one rule, or multiple rules that will dictate where the item 3d model will show up on the survivors.

Once the method is called, an **`ItemIndex`** or an **`EquipmentIndex`** (depending on what you are doing) will be returned by **ItemAPI** for you to use on your plugin code to do various checks, like if a player currently have your item and such.
```csharp
MyCustomItemIndex = ItemAPI.Add(customItem);

...

if (characterBody.inventory.GetItemCount(MyCustomItemIndex)  >  0)
{
	// This player currently have my custom item in its inventory !
}
```
***A more complete example of that is available under the [C# BepinEx Plugin Code](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#c-bepinex-plugin-code-1) section.***

***Please note that ItemAPI uses ItemDropAPI to add your item to the game item pool, so it also needs to be part of your `R2APISubmoduleDependency` attribute parameters !***


#### [ItemDropAPI](https://github.com/risk-of-thunder/R2API/blob/master/R2API/ItemDropAPI.cs)
creates a way for custom items and equipments to drop, while also allowing modders to prevent certain pickups from dropping. We won't cover this submodule entirely in this tutorial, as **ItemAPI** already uses it and do the necessary stuff to make your item drop in a run. 
***Setting a correct **`ItemTier`** when filling your ItemDef is important for this to work !***

#### [*ResourcesAPI*](https://github.com/risk-of-thunder/R2API/blob/master/R2API/ResourcesAPI.cs)
From the **R2API Wiki** :
- Creates a detour for mods to load in their own resources/assets.
- Hooking on **`UnityEngine.Resources.Load`** is being done so that the paths provided are scanned, if an assetbundle path is recognized it'll instead load the asset directly from the assetbundle.

**Important part for us :**
- **A Custom path must follow a pattern like this** :
When an assetbundle is added to this API, a **modprefix** must come with it, for example : **`@MyAssetBundle`**
The path to the custom object provided then looks like this : **`@MyAssetBundle:Assets/Import/belt/belt.prefab`**

In short, this API will allow us to load your custom icons, 3d models, and so on easily into the game by providing a distinguishable custom path.
***A more complete example of that is available under the [C# BepinEx Plugin Code](https://github.com/risk-of-thunder/R2Wiki/wiki/Custom-Item-Creation#c-bepinex-plugin-code-1) section.***

## [Unity](https://unity.com)
Risk of Rain 2 uses Unity Version **2018.4.16**, so let's [download that and install it](https://download.unity3d.com/download_unity/e6e9ca02b32a/Windows64EditorInstaller/UnitySetup64-2018.4.16f1.exe).
I did an **.unitypackage** out of my Rampage mod, that you can [download here](https://github.com/xiaoxiao921/ItemLib/blob/master/AssetBundle%20Example%20Project.unitypackage), it'll serve as a base for this part.

Launch the editor and create a new project.
Once that done, double click the **.unitypackage**, it should make a popup, every files should be checked by default, press **Import**, once its imported, you should see various new files in your **Assets/** folder.

Click on `Window` -> `PackageManager` and search for `AssetBundleBrowser` and install it, if not already done so.

Let's start with the **Import/** folder.
As you can see when going into the **Import/belt/** folder, a prefab called is already configured to be included in a future **exampleitemmod assetbundle** :

![Highlight path and assetbundle name](https://i.imgur.com/6hCRT1n.png)
Same goes for the icon located in the **Import/belt_icon/** folder : 

![can change the targeted assetbundle](https://i.imgur.com/nUJ8Cmo.png)

*You can change the name of the future assetbundle by selecting New on the dropdown menu when clicking on it.*

Once all the assets you wanted to package are correctly assigned an assetbundle, simply right click on the window explorer of Unity and click on **`AB Make`**, it'll be created in the **Assets/AssetBundle** folder.

![AB Make](https://i.imgur.com/wWJTmie.png)

![rampage assetbundle](https://i.imgur.com/rQEZchv.png)

Now that we have our assetbundle, we can finally do the best part, the code. ;)

## C# BepinEx Plugin Code

### Building from source

- Clone / Download as zip the [Boilerplate Item Solution](https://github.com/xiaoxiao921/CustomItem)
- Open `CustomItem.sln`
- The code is honestly pretty straight forward, though there is one thing to do still :

#### Importing the assetbundle into the Visual Studio Project :
 - Right click the project name in the solution explorer 
 - Add 
 - Existing item 
 - Add the assetbundle
 - Properties of assetbundle
 - Select Embedded resource
 
![embed resource](https://cdn.discordapp.com/attachments/575431803523956746/685275050542235713/unknown.png)

Then, in the **`Assets.cs`** file, make sure that the namespace and the filename of the assetbundle is correctly set.
```csharp
using (var stream = Assembly.GetExecutingAssembly().GetManifestResourceStream("CustomItem.rampage"))
```

You can now compile and test your plugin !
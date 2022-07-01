# Creating skin for vanilla characters with custom model

This guide will go over the process for creating a mod that will add a skin with a custom model into `Risk of Rain 2`.
We will be going over two different ways to create this mod. One will require little to no coding knowledge while the other will be the completely programmatic way.

These are the tools you'll need to make a skin:
* Blender (2.9 or 3.X work, I personally use 2.93)
* Unity 2019.4.26f1
* Asset Studio GUI (See Ripping Assets)

It is recommended that you be familiar with how to use Blender before attempting either method of creating skins. Unity Experience is also recommended, though not required.

### Video Tutorial
There is an old video tutorial of KingEnderBrine's Skin Builder (the non-programmer path to creating skins), but a large portion of it it is now outdated.

[The tutorial is located here](https://www.youtube.com/watch?v=NzrVKDw-vq4)

The parts of the video tutorial that are still accurate are:
* [Blender. Creating a skin model](https://www.youtube.com/watch?v=NzrVKDw-vq4&t=133s)
* [Unity. Creating skin](https://www.youtube.com/watch?v=NzrVKDw-vq4&t=486s)
* [VisualStudio. Optional code](https://www.youtube.com/watch?v=NzrVKDw-vq4&t=770s)
* [Risk of Rain 2. Testing](https://www.youtube.com/watch?v=NzrVKDw-vq4&t=910s)

You can likely supplement the missing portions of the video with this text tutorial, or use the video tutorial as a visual aid to help walk through parts of the text tutorial

### WARNING
Before making a skin mod it is recommended to backup your profile. There were some cases where people's logbook was wiped out during the development process.

***

<details>
 <summary>Ripping Assets</summary>

## Ripping assets
First of all, you need to import a character asset from the game to Blender. You can read about how to do that [here](https://github.com/RuneFox237/RoR2SotVBlenderScripts/wiki/Ripping-Characters-for-skins-from-RoR2-Post-SotV).
</details>

***

<details>
 <summary>Blender</summary>

## Blender
Once you've imported the ripped survivor to Blender, you create/import the model that you want to be a skin.
If you're importing a model to create your skin you'll need to pose and scale it so that it matches as close to the survivor armature as possible.
This is easy if your imported model has it's own skeleton, though more difficult if it does not.

It is possible to move the survivor armature to match your imported model. But it is very likely to cause distortions in the final product. If you have to move the survivor armature. Try to only rotate the bones as this is the least likely to cause distortions compared to moving and scaling the bones.

![](https://cdn.discordapp.com/attachments/706089456855154778/742381045990817912/MatchMeshWithArmature.jpg)

When you have the armature and mesh matching as close as possible it's now time to parent the mesh to the armature. For our example we'll be doing this the simple way with automatic weights. Though if you have more blender experience, it is recommended to do this in the way that works for you.
To automatically apply weights make sure you're in Object mode then select the mesh followed by the armature. Press ctrl+p and select 'with automatic weights'. If this worked correctly, you should be able to go to pose mode and move the bones around, the mesh should follow the bones.

![](https://cdn.discordapp.com/attachments/706089456855154778/742381050600357989/AppliedWeights.jpg)

Now we need to manage the materials and meshes of the model so that they will work in RoR2. RoR2's shader can only support one material per mesh, and each character only has a limited number of meshes. When possible try to merge all of your meshes into a single mesh. In order to merge meshes and materials, it is recommended to do this via blender addons like Material Combiner, or through third party programs like SubstancePainter + TextureSetCombiner.

When you have your model finalized you can now export it to FBX, no special export options are needed. If you're using a blender version older than blender 3.0, you may be able to just drag the blend file into unity without exporting it to fbx.

</details>

***

This is where the guide diverges based on the whether or not you want to go the programming or non-programming route. Both routes require you to create a unity project though, so make a project using the unity version listed above(2019.4.26f1). This project can be used for not just your current skin mod, but any additional skin mods you want to make in the future.

***

<details>
 <summary>KEB's Skin Builder (Non-programming Route)</summary>

![]()

This is the guide for making a skin mod using King EnderBrine's Skin Builder. This requires little to no programming knowledge in order to build the mod. 

<details>
 <summary>Unity</summary>

## Unity

### Importing the package
After creating your unity project and opening it up. Go to Window->"Package Manager" on the toolbar at the top of the unity window. In the window that appears hit the "+" in the top left and select "Add package from Git URL".

![](https://i.imgur.com/HIdQ9Ju.png)

Copy and Paste the link below into the field and hit 'add'

`https://github.com/KingEnderBrine/RoRSkinBuilder.git`

This will import the skin builder package. There will likely be a popup asking if you've backed up your project. It is safe to hit proceed on this popup.

### Importing your assets
You can import your mesh and textures now by dragging and dropping them into the unity project's assets folder. If you're model file is an fbx, select it in the unity Explorer and uncheck the "Convert Units" check box and then scroll down and hit Apply. This should keep the model's scale the same.

![image](https://user-images.githubusercontent.com/8129796/171987507-fca260d1-4848-4292-8b61-f3dd05555eb9.png)

### Creating the Materials
You will need to create materials using Hopoo's shaders for your mesh. Create a new material in unity each material you need. To do this, in the projects window in unity, Right Click -> Create -> Material. In each material, make sure the Shader is set to "Fake RoR/Hopoo Games/Deferred/Standard" and assign your textures to the relevant areas.

TODO: add a link to a tutorial going over how Hopoo shader works.

### Skin Definition
For each skin in your mod you're going to need to make a Skin Defenition. Right Click in the project window and select "Create->RoR Skins->SkinDefinition"

You'll need to fill out all the fields of the SkinDef that you'll need for your skin. Descriptions of all the fields are located below.
For all text fields, DO NOT start your text with a numerical number 0-9 i.e. (text numbers like one are fine though) or special characters(!, @, $, etc.) unless otherwise stated as this will cause errors in the build process. 

<details>
<summary>Parts of the SkinDefinition</summary>

![]()

_**Mod Dependency**_: add this if your mod requires another mod also be installed for it to work correctly. i.e. if your skin mod is for a custom modded character. **Value** is the mod dependency that you can find by looking at the mod's thunderstore page. **Type ** can be either hard or soft. Hard means the mod in value is required, while soft means that the mod is not required but some functionality of your skin mod may require it.

_**Generate Enable Config**_: When Checked, this allows the mod to generate a config that allows the skins in the mod to be disabled/enabled

_**Body Name**_: This is the name of the suvivor body that the skin is for. This is the internal name for the survivor and usually the name of the survivor mesh when it's originally extracted from the game. i.e. Acrid's survivor body is CrocoBody, Rex is TreebotBody, etc.

_**Name Token Localization**_: This is a list of localization tokens. Input the number of languages you want to localize for in the size field. Each expandable element in the list has a **language** field that and a **value **field. The **Language **field takes ISO 2-letter language codes, like EN for English. The **Value **field is the translated name that you want your skin to display when highlighted in-game.

_**Icon**_: This is the icon that represents the skin in the skins list in-game. You can specify a sprite or check the "Create from Colors" and build one from the color picker there.

_**Unlockable Name**_: Name of the achievement that you want the skin to be locked behind. Useful for mastery skins and hooking into special acheviments.

_**Base Skins**_: The Base Skins that the skin will use for meshes and materials that are not replaced. These skins can be referenced by Index or Name. 

_**Renderers Source**_: Source of the renderers for the renderers collection, mouse over the text in unity for more details. I would suggest leaving it on "All Render Components"

_**Mesh Replacements**_: This list is where you add the meshes you want to replace the base survivor meshes with. Size is the size of the list. You can also add/remove elements by right clicking on an element in the list and selecting duplicate or delete respectively. Each element in the list has:
* a **Mesh** field - This is where you can add a mesh from your custom model. To do so you can target symbol on the right side of the field and select your mesh from the window that appears.
* a **Renderer Reference** field - This is the renderer that contains the mesh the mod will be trying to replace with the mesh in the mesh field. To find this renderer refer to [this txt file](https://github.com/RuneFox237/RoR2SotVBlenderScripts/blob/main/RoR2_SotV%20Blender%20Scripts/SotVSurvivorRenderIndexes.txt) which contains the indexes and names of all the renderers for all vanilla survivors. Find your survivor's BodyName in the list, you'll want to replace the mesh in one of the SkinnedMeshRenderers so use either the index or the name of the skinnedMeshRenderer, make sure to switch the Access Type to the one you want. **You cannot use the same Renderer for multiple meshes. Each Renderer can only support one mesh**

_**Game Object Activations**_: This list is used to make sure certain Renderer Game Objects are active/inactive. Each element in the list has:
* a Render Index field - See the Render Reference field in Mesh Replacements above for more info. 
* a "Should Activate" toggle - Checking this makes sure this game object is activated when the skin is equipped. Leaving it Unchecked makes sure this game object is deactivated when the skin is equipped. Deactivating a game object also disables any renderers that are components of this object. Useful for disabling meshes that you aren't using/replacing and particle systems that don't match your skin.

_**Render Infos**_: This list is used to replace the materials in the renderers with materials of your choosing, if a renderer is not included in this list, it will use the material provided by the base skin. Each element in this list has:
* A Default Material field - This is the material you want the mod to replace the base material with. i.e. this should be one of the custom materials you set up earlier.
* Default Shadow Casting Mode - The way this renderer/mesh generates shadows. See the Cast Shadows field in the [official unity documentation](https://docs.unity3d.com/510/Documentation/Manual/class-MeshRenderer.html) for more info.
* Ignore Overlays - Whether or not overlays in-game are applied to this renderer/mesh. i.e. things like Personal Shield Generator or some other buff effects.
* Render Reference - See Render Reference in Mesh Replacements for more info.

_**Minion Skin Replacements**_: This list is for replacing minions like the Engineers Turrets. Each minion needs it's own SkinDefinition. Each element in the list has:
* BodyName: This is the name of the body that the skin is for. i.e. regular Engineer turrets are EngiTurretBody and walkers are EngiWalkerTurretBody.
* Find Skin By Reference: Check this if your skin uses turret skins from the base game/other mods. Then you set the skin similar to how you set Base Skin
* Skin Definition: The skin def of the minion skin you want to use.

_**Projectile Ghost Replacements**_: List used to replace the projectile ghosts of a character. TODO: add a tutorial on how to do this.

</details>


### Skin Mod Info

Your mod will need a Mod Info in order for it to be created. Right Click in the project window and select "Create->RoR Skins->SkinModInfo"

You'll need to fill out most of the fields of the ModInfo in order to build your mod. Descriptions of all the fields are located below.
For all text fields, DO NOT start your text with a numerical number 0-9 i.e. (text numbers like one are fine though) or special characters(!, @, $, etc.) unless otherwise stated as this will cause errors in the build process. 

<details>
<summary>Parts of the Skin Mod Info</summary>

![]()

_**Mod Name**_: The name of your mod

_**Author**_: The name of the author of the mod (You!)

_**Version**_: Version number of the mod. Follows semantic version format i.e. 1.0.0.

_**Skins**_: This is the list of skins you want to include in your mod. Set the size and assign the skins by clicking the target on the right of each element and selecting the skin. If one of your skins has minion replacement(s) with corresponding skin(s) make sure you include them as well.

_**Additional Resources**_: A list where you can specify additional resources to the asset bundle built for the mod. You can leave this alone unless you know you need to add additional resources. Materials and meshes assigned in skindefs are automatically added to the bundle and DO NOT need to be added here.

_**Regenerate Code**_: This toggle when checked regenerates the code that is generated by the builder when the build button is pressed. Keep this checked if you made any changes to skindefs or additional resources. Only toggle this off if you want to make edits to the autogenerated code.

_**Regenerate Assembly Definition**_: This toggle when checked regenerates the assembly definition generated by the builder when the build button is pressed. Only toggle this off if you want to manually make edits to the Assembly Definition.

_**Build Button**_: Hit this when you want to build your mod. A progress bar pop-up may appear and Unity will likely freeze for a moment. If the mod builds with no errors then a separate windows explorer window will appear with your mod dll inside it. If there are errors an error sound will play and the window will not appear. Check the unity console for errors, fix them and try again.

</details>

</details>

</details>

***

<details>
 <summary>Manual Programming route</summary>

## Note: This is copied from an older version of the skin creation wiki pretty much verbatim and may not work correctly. Proceed with caution

## Unity
First of all, you need to use the `2019.4.26f` version of Unity, otherwise, the game will not be able to load your Assetundle.

Create a new Unity project, drag the Blender file into assets. Open the imported asset, select mesh, and press ctrl+d it will duplicate mesh into its asset, so you can build AssetBundle without excessive things from the Blender file. You can do the same thing with the material, or create new in Unity.
Should be looking something like this

![](https://cdn.discordapp.com/attachments/706089456855154778/742381037618855937/ImportIntoUnity.jpg)

Now install `AssetBundle Browser` package (In top panel: Window -> Package Manager), and open `AssetBundles` window (In top panel: Window -> AssetBundle Browser).
Select what you need for AssetBundle and drag it to the AssetBundles window, create 1 AssetBundle

![](https://cdn.discordapp.com/attachments/706089456855154778/742381029003886592/DragAndDropAssets.jpg)

Switch to the `Build` tab and press the build button. This will create AssetBundle in `{YourUnityProject}\AssetBundles\StandaloneWindows` folder.

## Visual studio
Create/[use a boilerplate](https://github.com/risk-of-thunder/R2Wiki/wiki/Baby's-First-Mod) visual studio project.
Drag and drop your AssetBundle file into the project.

![](https://cdn.discordapp.com/attachments/706089456855154778/742381035773231184/ImportAssetBundle.jpg)

Then select this file and set `Build Action` property to `Embedded resource`

![](https://cdn.discordapp.com/attachments/706089456855154778/742381030589071420/EmbeddedResource.jpg)

I will provide the code snippet for how to add the skin with some commentaries.

# Renderers
In the example code, there is an array of renderers. The render indices for vanilla survivors can be seen [here](https://github.com/RuneFox237/RoR2SotVBlenderScripts/blob/main/RoR2_SotV%20Blender%20Scripts/SotVSurvivorRenderIndexes.txt)


# Example code

```
using BepInEx;
using R2API;
using R2API.Utils;
using RoR2;
using System;
using System.Reflection;
using UnityEngine;

//Change `SkinTest` to your project name
namespace SkinTest
{
    //Marks mod as client-side
    [NetworkCompatibility(CompatibilityLevel.NoNeedForSync)]
    //Requests `R2API` submodules
    [R2APISubmoduleDependency(nameof(PrefabAPI), nameof(LoadoutAPI), nameof(SurvivorAPI), nameof(ResourcesAPI), nameof(LanguageAPI))]
    //Adds dependency to `R2API`
    [BepInDependency("com.bepis.r2api")]
    //Definition of a mod
    [BepInPlugin(
        //The GUID should be a unique ID for this plugin, which is human readable (as it is used in places like the config). Java package notation is commonly used, which is "com.[your name here].[your plugin name here]"
        "com.MyName.IHerebyGrantPermissionToDeprecateMyModFromThunderstoreBecauseIHaveNotChangedTheName",
        //The name is the name of the plugin that's displayed on load
        "IHerebyGrantPermissionToDeprecateMyModFromThunderstoreBecauseIHaveNotChangedTheName",
        //The version number just specifies what version the plugin is.
        "1.0.0")]

    //Rename `SkinTest` to match your .cs file name
    public class SkinTest : BaseUnityPlugin
    {
        private void Awake()
        {
            //Loading AssetBundle with a model. String value is "{ProjectName}.{AssetBundleFileName}"
            using (var assetStream = Assembly.GetExecutingAssembly().GetManifestResourceStream("SkinTest.lumberjackskin"))
            {
                var MainAssetBundle = AssetBundle.LoadFromStream(assetStream);
                //This string value will be used as a part of resource path. Replace `@SkinTest` with your name prefferably it should be `@{mod name}`
                ResourcesAPI.AddProvider(new AssetBundleResourcesProvider("@SkinTest", MainAssetBundle));
            }

            //Adding our skill after all characters were loaded
            On.RoR2.SurvivorCatalog.Init += (orig) =>
            {
                orig();

                AddLumberJackSkin();
            };

            //Adding language token, so every time game sees `LUMBERJACK_SKIN` it will be replaced with `Lumberjack`
            //You can also use `LanguageAPI.Add("TOKEN", "Value", "Language")` to add localization for specific language
            //For example `LanguageAPI.Add("LUMBERJACK_SKIN", "Дровосек", "RU")`
            LanguageAPI.Add("LUMBERJACK_SKIN", "Lumberjack");
        }

        private void AddLumberJackSkin()
        {
            //Getting character's prefab
            var survivorDef = SurvivorCatalog.GetSurvivorDef(SurvivorIndex.Commando);
            var bodyPrefab = survivorDef.bodyPrefab;

            //Getting necessary components
            var renderers = bodyPrefab.GetComponentsInChildren<Renderer>(true);            
            var skinController = bodyPrefab.GetComponentInChildren<ModelSkinController>();
            var mdl = skinController.gameObject;

            var skin = new LoadoutAPI.SkinDefInfo
            {
                //Icon for your skin in the game, it can be any image, or you can use `LoadoutAPI.CreateSkinIcon` to easily create an icon that looks similar to the icons in the game.
                Icon = LoadoutAPI.CreateSkinIcon(Color.black, Color.white, new Color(0.5F, 0.3F, 0), Color.white),
                //Replace `LumberJackCommando` with your skin name that can be used to access it through the code
                Name = "LumberJackCommando",
                //Replace `LUMBERJACK_SKIN` with your token
                NameToken = "LUMBERJACK_SKIN",
                RootObject = mdl,
                //Defining skins that will be applyed before our.
                //Default skin index - 0, Monsoon skin index - 1
                //Or you can use `BaseSkins = Array.Empty<SkinDef>(),` if you don't want to add base skin
                //Because we will replace only Commando mesh, but not his pistols we have to use base skin.
                BaseSkins = new SkinDef[] { skinController.skins[0] },
                //Name of achievement after which skin will be unlocked
                //Leave that field empty if you want skin to be always available 
                UnlockableName = "",
                //This is used to disable/enable some gameobjects in body prefab.
                GameObjectActivations = new SkinDef.GameObjectActivation[0],
                //This is used to define which material should be used on a specific renderer.
                //Only one material per renderer(mesh) is can be used
                RendererInfos = new CharacterModel.RendererInfo[]
                {
                    //To add another material replacement simply copy past this block right after and add `,` after the first one.
                    new CharacterModel.RendererInfo
                    {
                        //Loading material from AssetBundle replace "@SkinTest:Assets/Resources/matLumberJack.mat" with your value.
                        //It should be in this format `{provider name}:{path to asset in unity}`
                        //To get path to asset you can right click on your asset in Unity and select `Copy path` option
                        defaultMaterial = Resources.Load<Material>("@SkinTest:Assets/Resources/matLumberJack.mat"),
                        //Should mesh cast shadows
                        defaultShadowCastingMode = UnityEngine.Rendering.ShadowCastingMode.On,
                        //Should mesh be ignored by overlays. For example shield outline from `Personal Shield Generator`
                        ignoreOverlays = false,
                        //Which renderer(mesh) to replace.
                        //Index you need can be found here: https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-skin-for-vanilla-characters-with-custom-model#renderers
                        renderer = renderers[2]
                    }
                },
                //This is used to define which mesh should be used on a specific renderer.
                MeshReplacements = new SkinDef.MeshReplacement[]
                {
                    //To add another mesh replacement simply copy past this block right after and add `,` after the first one.
                    new SkinDef.MeshReplacement
                    {
                        //Loading mesh from AssetBundle look at material replacement commentary to learn about how value should be changed.
                        mesh = Resources.Load<Mesh>("@SkinTest:Assets/Resources/LumberJack.mesh"),
                        //Index you need can be found here: https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-skin-for-vanilla-characters-with-custom-model#renderers
                        renderer = renderers[2]
                    }
                },
                //You probably don't need to touch this line
                ProjectileGhostReplacements = new SkinDef.ProjectileGhostReplacement[0],
                //This is used to add skins for minions e.g. EngiTurrets
                MinionSkinReplacements = new SkinDef.MinionSkinReplacement[0]
            };

            //Adding new skin to a character's skin controller
            Array.Resize(ref skinController.skins, skinController.skins.Length + 1);
            skinController.skins[skinController.skins.Length - 1] = LoadoutAPI.CreateNewSkinDef(skin);

            //Adding new skin into BodyCatalog
            var skinsField = Reflection.GetFieldValue<SkinDef[][]>(typeof(BodyCatalog), "skins");
            skinsField[BodyCatalog.FindBodyIndex(bodyPrefab)] = skinController.skins;
            Reflection.SetFieldValue(typeof(BodyCatalog), "skins", skinsField);
        }
    }
}
```

## Result

![](https://cdn.discordapp.com/attachments/706089456855154778/742381034691231775/EndResult.jpg)

You can check out how it looks in motion: https://youtu.be/N5WPOxIErI0
</details>

***

<details>
 <summary>Testing your mod</summary>

## Testing your mod

Once you have a dll of your mod, you can drop it into your bepinex plugins folder and launch your modded RoR2. If everything is working, you should be able to select your skin in the character select screen.

TODO: Create a list of Common issues and solutions that appear when building skins.
</details>

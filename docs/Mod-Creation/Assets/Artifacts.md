# Artifacts
Artifacts ingame can be useful for giving your RoR2 run a bit of a twist, this guide walks you through creating an Artifact for your mod.

While there are certainly a number of ways to create Artifacts, this guide uses KomradeSpectre's [ItemModCreationBoilerplate](https://github.com/KomradeSpectre/ItemModCreationBoilerplate) as a base for creating the Artifact. more methods for creating custom Artifacts may come in the future...

## Index
* [Artifact Creation using ItemModCreationBoilerPlate.](https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-Custom-Artifacts#artifact-creation-using-itemmodcreationboilerplate)
* [Artifact Creation in one class.](https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-Custom-Artifacts#artifact-creation-in-one-class)
* [Artifact Creation using Thunderkit.](https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-Custom-Artifacts#artifact-creation-using-thunderkit)
* [Artifact Code Creation.](https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-Custom-Artifacts#artifact-code-creation)
* [Artifact Compound Creation.](https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-Custom-Artifacts#artifactcompound)


## Artifact creation using ItemModCreationBoilerplate
In Komrade's ItemModCreationBoilerplate, an artifact is composed of 2 key classes, the [ArtifactBase](https://github.com/KomradeSpectre/ItemModCreationBoilerplate/blob/master/ItemModCreationBoilerplate/Artifact/ArtifactBase.cs) and the [Artifact](https://github.com/KomradeSpectre/ItemModCreationBoilerplate/blob/master/ItemModCreationBoilerplate/Artifact/ExampleArtifact.cs) class itself. We will first walk thru ArtifactBase. Keep in mind that you need to have R2API in your references for this to work properly.

### Artifact Base
```csharp
using BepInEx.Configuration;
using R2API;
using RoR2;
using System;
using System.Collections.Generic;
using System.Text;
using UnityEngine;

namespace YourNamespaceHere
{
	public abstract class ArtifactBase
	{
		public abstract string ArtifactName { get; }
		public abstract string ArtifactLangTokenName { get; }
		public abstract string ArtifactDescription { get; }
		public abstract Sprite ArtifactEnabledIcon { get; }
		public abstract Sprite ArtifactDisabledIcon { get; }
		public ArtifactDef ArtifactDef;
		public bool ArtifactEnabled => RunArtifactManager.instance.IsArtifactEnabled(ArtifactDef);
		public abstract void Init(ConfigFile config);
		protected void CreateLang()
		{
			LanguageAPI.Add("ARTIFACT_" + ArtifactLangTokenName + "_NAME", ArtifactName);
			LanguageAPI.Add("ARTIFACT_" + ArtifactLangTokenName + "_DESCRIPTION", ArtifactDescription);
		}
		protected void CreateArtifact()
		{
			ArtifactDef = ScriptableObject.CreateInstance<ArtifactDef>();
			ArtifactDef.cachedName = "ARTIFACT_" + ArtifactLangTokenName;
			ArtifactDef.nameToken = "ARTIFACT_" + ArtifactLangTokenName + "_NAME";
			ArtifactDef.descriptionToken = "ARTIFACT_" + ArtifactLangTokenName + "_DESCRIPTION";
			ArtifactDef.smallIconSelectedSprite = ArtifactEnabledIcon;
			ArtifactDef.smallIconDeselectedSprite = ArtifactDisabledIcon;
			ContentAddition.AddArtifactDef(ArtifactDef);
		}
		public abstract void Hooks();
	}
}
```
Since this class is an **Abstract** class, it only works as a base. as such, just copy and paste this code snippet into an empty class file and create your artifact in another class that inherits from ArtifactBase.
Bellow is a detailed walkthrough on each variable and the methods.

ArtifactName: The name of your Artifact.
ArtifactLangTokenName: The lang token of your Artifact.
ArtifactDescription: The Description of the Artifact when you hover over it.
ArtifactEnabled: the icon of your Artifact when its enabled.
ArtifactDisabled: the icon of your Artifact when its disabled.
ArtifactDef: Your Artifact Def, Basically how the game identifies your Artifact.
ArtifactEnabled: A bool that can be used to check if the run has your artifact enabled. Which is the backbone for making your Artifact do stuff.

CreateLang(): Method that creates the lang tokens of your Artifact
CreateArtifact(): Method that finishes the creation of your Artifact and Registers it to the game using ArtifactAPI.
Hooks(): The hooks your artifact uses.

Once you finish copying and Parting ArtifactBase, all thats left to do is create the class where you will fill in the information and the logic behind your Artifact. for this, we will take a look at ItemModCreationBoilerplate's [ExampleArtifact](https://github.com/KomradeSpectre/ItemModCreationBoilerplate/blob/master/ItemModCreationBoilerplate/Artifact/ExampleArtifact.cs)

### ExampleArtifact
```csharp
using BepInEx.Configuration;
using System;
using System.Collections.Generic;
using System.Text;
using UnityEngine;
using UnityEngine.Networking;
using RoR2;
using static YourNameSpaceHere.BaseUnityPluginInheritedClass;
namespace YourNameSpaceHere
{
	class ExampleArtifact : ArtifactBase
	{
		public static ConfigEntry<int> TimesToPrintMessageOnStart;
		public override string ArtifactName => "Artifact of Example";
		public override string ArtifactLangTokenName => "ARTIFACT_OF_EXAMPLE";
		public override string ArtifactDescription => "When enabled, print a message to the chat at the start of the run.";
		public override Sprite ArtifactEnabledIcon => MainAssets.LoadAsset<Sprite>("ExampleArtifactEnabledIcon.png");
		public override Sprite ArtifactDisabledIcon => MainAssets.LoadAsset<Sprite>("ExampleArtifactDisabledIcon.png");
		public override void Init(ConfigFile config)
		{
			CreateConfig(config);
			CreateLang();
			CreateArtifact();
			Hooks();
		}
		private void CreateConfig(ConfigFile config)
		{
			TimesToPrintMessageOnStart = config.Bind<int>("Artifact: " + ArtifactName, "Times to Print Message in Chat", 5, "How many times should a message be printed to the chat on run start?");
		}
		public override void Hooks()
		{
			Run.onRunStartGlobal += PrintMessageToChat;
		}
		private void PrintMessageToChat(Run run)
		{
			if(NetworkServer.active && ArtifactEnabled)
			{
				for(int i = 0; i < TimesToPrintMessageOnStart.Value; i++)
				{
					Chat.AddMessage("Example Artifact has been Enabled.");
				}
			}
		}
	}
}
```

Copy and paste the code snippet above and replace the values with that your Artifact needs.
Keep in mind that for your ArtifactEnabled and Disabled icons you'll need to create an assetbundle for it, Assetbundles are outside of this tutorial's scope, but there is a wiki entry for them [here](https://github.com/KomradeSpectre/AetheriumMod/blob/rewrite-master/Tutorials/Item%20Mod%20Creation.md#creating-an-asset-bundle)

The final step of the process is Initializing your artifact, for this, ItemModCreationBoilerplate has its own code that we can use for initializing the Artifact. the code can be found [here](https://github.com/KomradeSpectre/ItemModCreationBoilerplate/blob/master/ItemModCreationBoilerplate/Main.cs)

### Artifact Initialization.

You need to add this List to your main class first.

```csharp
public  List<ArtifactBase> Artifacts  =  new  List<ArtifactBase>();
```

The following snippet code goes in your Awake method in your BaseUnityPlugin inherited class.
```csharp
var ArtifactTypes = Assembly.GetExecutingAssembly().GetTypes().Where(type => !type.IsAbstract && type.IsSubclassOf(typeof(ArtifactBase)));
foreach (var artifactType in ArtifactTypes)
{
	ArtifactBase artifact = (ArtifactBase)Activator.CreateInstance(artifactType);
	if (ValidateArtifact(artifact, Artifacts))
	{
		artifact.Init(Config);
	}
}
```
The following method helps initializing the Artifact. this goes outside of your Awake method but still inside your BaseUnityPlugin inherited class.
```csharp
public bool ValidateArtifact(ArtifactBase artifact, List<ArtifactBase> artifactList)
{
	var enabled = Config.Bind<bool>("Artifact: " + artifact.ArtifactName, "Enable Artifact?", true, "Should this artifact appear for selection?").Value;
	if (enabled)
	{
		artifactList.Add(artifact);
	}
	return enabled;
}
```
## Artifact Creation in one class

There are methods to create Artifacts using one class instead of using the great amount of structure that KomradeSpectre's ItemModCreationBoilerplate uses. for this, we will look at MonsterVariantsPlus, and how it creates it's Artifact of Variance artifact. it's Artifact class can be found [here](https://github.com/Nebby1999/MonsterVariantsPlus/blob/master/MonsterVariants%2B/SubClasses/Artifact.cs)

### Artifact Class
```csharp
using RoR2;
using R2API;
using UnityEngine;
using MonsterVariants.Components;
using MonoMod.RuntimeDetour;
using System.Reflection;
using System;
namespace MonsterVariantsPlus.SubClasses
{
	public class Artifact
	{
		public static ArtifactDef Variance = ScriptableObject.CreateInstance<ArtifactDef>();
		public static void InitializeArtifact()
		{
			Variance.nameToken = "Artifact of Variance";
			if (ConfigLoader.ArtifactIncreasesRewards)
			{
				Variance.descriptionToken = "All Variant's Spawn Rates & Rewards are Multiplied by " + ConfigLoader.SpawnRateMultiplier;
			}
			else
			{
				Variance.descriptionToken = "All Variant's Spawn Rates are Multiplied by " + ConfigLoader.SpawnRateMultiplier;
			}
			Variance.smallIconDeselectedSprite = AssetLoaderAndChecker.MainAssets.LoadAsset<Sprite>("Assets/Textures/Artifact/VarianceDisabled.png");
			Variance.smallIconSelectedSprite = AssetLoaderAndChecker.MainAssets.LoadAsset<Sprite>("Assets/Textures/Artifact/VarianceEnabled.png");
			ArtifactAPI.Add(Variance);
		}
		public static void MonsterVariantAwakeHook(Action<VariantHandler> orig, VariantHandler self)
		{
			var origRate = self.spawnRate;
			if (RunArtifactManager.instance.IsArtifactEnabled(Variance))
			{
				self.spawnRate *= ConfigLoader.SpawnRateMultiplier;
				//Avoid potentially bad spawn rates
				if(self.spawnRate < 0)
				{
					self.spawnRate = 0;
				}
				else if(self.spawnRate > 100)
				{
					self.spawnRate = 100;
				}
			}
			orig(self);
			self.spawnRate = origRate;
		}
	}
}
```
Most of the variables found in this clase are very similar to ArtifactBase's variables.
One thing to note is the use of a Custom Hook, unless you know how to create it, you can simply use a normal Hook and initialize the hook inside InitializeArtifact()

To have your mod load the Artifact, add this snippet of code to your BaseUnityPlugin inherited class.
```csharp
Artifact.InitializeArtifact();
```
## Artifact Creation using Thunderkit

Creating an artifact using Thunderkit is not exactly difficult. It just requires you to know how Thunderkit works and how to develop with it.

It is extremely encouraged to check Kevin from HP Customer Service's "Creating Mods with Thunderkit" wiki page.

This guide assumes you already followed the steps in that page. and already have your Thunderkit project set up.

### Creating your ArtifactDef

On your asset Bundle, you need to create an ArtifactDef. to do this, right click on the project folder, and go to Create -> RoR2 -> ArtifactDef.

![](https://cdn.discordapp.com/attachments/850538397647110145/851206050103427072/b3d6d977d0c7f2841c62017832d4e26f.png)

After creating your ArtifactDef, click it, in your inspector, you'll be able to change the settings of your Artifact to your heart's desires.

    * Name Token

        - The name of your artifact as it appears in the Survivor select screen

    * Description Token

        - The Description of your artifact as it appears in the Survivor select screen

    * Small Icon Selected Sprite
   
        - The Sprite of your artifact when it's enabled.

    * Small Icon Deselected Sprite

        - The sprite of your artifact when its disabled

Note: Filling out UnlockableDef & Pickup Model Prefab is NOT necesary.

![](https://cdn.discordapp.com/attachments/850538397647110145/851207413701017600/5b889b7e34f5041a2bdcff949749c2ab.png)

Once you've filled out your ArtifactDef, add it to your SerializableContentPack.

![](https://cdn.discordapp.com/attachments/850538397647110145/851207768643862528/97321fd379027d12f5823a36a7a28bfb.png)

Congratulations, once you load your Thunderkit made mod, the game will load your Custom Artifact.

However, Your artifact while existing in the game, it currently does nothing, this is because it's functionality is written in code.

### Making your Artifact do Stuff.

* Open up your AssemblyDef, create a new class where you will store your Artifact's code.

* Paste the following code snippet in your new class

```csharp
using RoR2;
using R2API;
using UnityEngine;
using MonoMod.RuntimeDetour;
using System.Reflection;
using System;

namespace YourNamespaceHere
{
	public class ThunderkitMadeArtifact
	{
		public static ArtifactDef MyArtifactDef = ContentPackProvider.contentPack.artifactDefs.Find("YourArtifactDef");
		public static void InitializeArtifact()
		{
			Hooks();
		}
		public static void Hooks()
		{
			  Run.onRunStartGlobal += MyArtifactEffect;
		}
		private void MyArtifactEffect(Run run)
		{
			   if(NetowkServer.active && RunArtifactManager.instance.IsArtifactEnabled(MyArtifactDef)
			  Chat.AddMessage("My Artifact has been enabled!"
		}
	}
}
```
You can now modify your Hook and make your artifact do stuff when its enabled.

Once you're finished with creating your artifact's effects, simply call the InitializeArtifact() method to initialize your artifact's effects.

## Artifact Code Creation

With R2API version 3.0.50, the ArtifactCodeAPI was added to the plethora of R2API Submodules.

ArtifactCodeAPI, in a nutshell,  allows mod creators to add their own Artifact Codes to the game, so they can have them appear inside the bulwark's ambry and potentially unlock the artifact the same way as vanilla artifacts.

ArtifactCodeAPI Allows the mod creator to:

* Create custom Artifact Codes easily with the ArtifactCode scriptable object.
* Create new Artifact Compounds.

Custom Code 

![](https://media.discordapp.net/attachments/562704639569428506/872532114711674900/unknown.png?width=1203&height=676)
![](https://media.discordapp.net/attachments/562704639569428506/872532136236838922/unknown.png?width=1203&height=676)

Custom Artifact Compound (Artifact Compound for [Genetic Artifact](https://thunderstore.io/package/Rico/GeneticArtifact/?utm_source=discord), made by Rico.)

![](https://media.discordapp.net/attachments/567836513078083584/872905325643186236/unknown.png)


## ArtifactCode scriptable object.
The ArtifactCode Scriptable Object greatly simplifies the creation of Codes, while normally you would make codes by using a Sha256HashAsset, and inputting complex ulong based values. ArtifactCode scriptable object creates these ulong values for you by reading thru the values inputted in 3 pairs of Vector3Int.

Here is an example on how the Artifact of Commando code would be generated using ArtifactCode
```csharp
artifactCode = ScriptableObject.CreateInstance<ArtifactCode>();
artifactCode.topRow = new Vector3Int(CompoundValues.Square, CompoundValues.Square, CompoundValues.Square);
artifactCode.middleRow = new Vector3Int(CompoundValues.Square, CompoundValues.Square, CompoundValues.Square);
artifactCode.bottomRow = new Vector3Int(CompoundValues.Triangle, CompoundValues.Triangle, CompoundValues.Triangle);
```
Please note that each Vector3Int corresponds to a row in the Artifact tablet.

As well, keep in mind that AddCode() requires you to pass your ArtifactDef as an argument.

### Thunderkit usage.
The ArtifactCode scriptable object can also be created from the editor and be used in conjunction with Thunderkit. It can be found in the Create Asset Menu
![](https://cdn.discordapp.com/attachments/567827235013132291/873231781753684009/unknown.png)

![](https://cdn.discordapp.com/attachments/575431803523956746/946772298050981998/a4b8e2098dbd0cc21e60e736c215340d.png)
Implementing the code is as easy as grabbing the desired ArtifactDef & ArtifactCode from your assetBundle and registering the code.
```csharp
var def = Assets.LoadAsset<ArtifactDef>("ArtifactDef");
var hash = Assets.LoadAsset<R2API.ArtifactCode>("Code");
R2API.ArtifactCodeAPI.AddCode(def, hash);
```
A list of the vanilla compound's Values can be found inside the ArtifactCodeAPI itself, under ArtifactCodeAPI.CompoundValues.

## ArtifactCompound
Creating a custom artifact compound can be a good way to ensure nobody else uses the same code you just created, which allows modders to avoid having code conflicts alltogether.
These are created using the ArtifactCompoundDef scriptable object, which is part of RoR2 code.
```csharp
geneArtifactCompoundDef = ScriptableObject.CreateInstance<ArtifactCompoundDef>();
geneArtifactCompoundDef.modelPrefab = GeneticsArtifactPlugin.geneticAssetBundle.LoadAsset<GameObject>("Assets/Genetics/CompoundGene.prefab");
geneArtifactCompoundDef.value = 15;
ArtifactCodeAPI.AddCompound(geneArtifactCompoundDef);
```
### Thunderkit usage.
Just like ArtifactCodes, you can easily create an ArtifactCompound using the Scriptable object and thunderkit's tools.

![](https://i.gyazo.com/b4b37a126fd9e2853701187eaa0d82c9.png)
```csharp
var Compound = Assets.LoadAsset<ArtifactCompoundDef>("CompoundDef");
R2API.ArtifactCodeAPI.AddCompound(Compound);
```
# Cookbook

Welcome to the Risk of Rain 2 modding cookbook, explore modding best practices with common code snippets. 

***Refer to the Table of Contents to the right of the page to jump to a specific snippet.***

## Loading Assets
After the Memory Optimization Update, modders now have an opt-in system to keep load times fast and keep your mod's memory usage in check. To use the `GameAssetPaths` you'll need a reference to the `RoR2BepInExPack` package in your `.csproj`. [Link to the latest version in the modding discord](https://discord.com/channels/562704639141740588/562704639569428506/1303101282894090240).
```cs
// Example for how to properly load in assets to be used later. It's divided up for better readability, but you can put it all in one-line.
public Material moonMat;
AssetReferenceT<Material> moonMatRef = new AssetReferenceT<Material>(RoR2BepInExPack.GameAssetPaths.RoR2_Base_moon.matMoonTerrain_mat);
AssetAsyncReferenceManager<Material>.LoadAsset(moonMatRef).Completed += (x) => moonMat = x.Result;
```

```cs
// Example for how to properly load in an asset then edit it. You can save it to a variable before/after editing inside of the .Completed.
AssetReferenceT<Material> starPickupRef = new AssetReferenceT<Material>(RoR2BepInExPack.GameAssetPaths.RoR2_DLC2_Items_LowerPricedChests.PickupSaleStar_prefab);
AssetAsyncReferenceManager<GameObject>.LoadAsset(starPickupRef).Completed += (x) =>
{
  MeshCollider collider = obj.Result.transform.find("SaleStar")?.GetComponent<MeshCollider>();
  if (collider)
  {
    collider.convex = true;
  }
};
```

## Creating Skills
There are multiple parts of a skill, the actual definition (SkillDef), the SkillFamily it belongs to (Primary/Secondary/Utility/Special), the SkillVariant within the family (if you have multiple SkillDefs under a family), and the GenericSkill which holds it all together. Usually the GenericSkill and SkillFamily already exists (if you're adding a skill to an existing body) so you'd need to create the SkillDef and SkillVariant.
```cs
  private void CreateSkill(GameObject body)
    {
      // Creating the SkillDef
      SkillDef skillDef = ScriptableObject.CreateInstance<SkillDef>();
      skillDef.skillName = "SKILL_NAME_TOKEN";
      (skillDef as ScriptableObject).name = "SKILL_NAME_TOKEN";

      skillDef.activationState = new SerializableEntityStateType(typeof(YourEntityState));
      skillDef.activationStateMachineName = "Body"; // This is usually either "Body" or "Weapon" depending on your skill
      skillDef.interruptPriority = InterruptPriority.Skill; // If the priority is the same or "higher" it can interrupt this skill

      skillDef.baseMaxStock = 1;
      skillDef.baseRechargeInterval = 4f;

      skillDef.rechargeStock = 1;
      skillDef.requiredStock = 1;
      skillDef.stockToConsume = 1;

      skillDef.dontAllowPastMaxStocks = true;
      skillDef.beginSkillCooldownOnSkillEnd = false;
      skillDef.canceledFromSprinting = false;
      skillDef.forceSprintDuringState = true;
      skillDef.fullRestockOnAssign = true;
      skillDef.resetCooldownTimerOnUse = true;
      skillDef.isCombatSkill = false;
      skillDef.mustKeyPress = false; // This can stay false
      skillDef.cancelSprintingOnActivation = false;
      
      // Creating a SkillFamily
      SkillFamily newFamily = ScriptableObject.CreateInstance<SkillFamily>();
      (newFamily as ScriptableObject).name = body.name + "Secondary" + "Family";
      ;
      newFamily.variants = new SkillFamily.Variant[1] { new SkillFamily.Variant { skillDef = skillDef } };

      // Creating a GenericSkill
      GenericSkill skill = body.AddComponent<GenericSkill>();
      skill._skillFamily = newFamily;
      body.GetComponent<SkillLocator>().secondary = skill;

      // Adding your new SkillDef and SkillFamily with R2API.ContentManagement
      ContentAddition.AddSkillFamily(newFamily);
      ContentAddition.AddSkillDef(skillDef);
    }
```
## Getter Hooks
HookGen doesn't generate `On` or `IL` hooks for getters, so if you want to hook a getter for extra you'll have to create it yourself. This example uses the `cost` attribute in the `DirectorCard` class which you can check using a decompiler (dnspy/ilspy)
```cs
// These go into your Awake function
var target = typeof(DirectorCard).GetPropertyGetter(nameof(DirectorCard.cost));
var hook = new Hook(target, OnCost);

// if your method isn't public you'll get an error
public int OnCost(Func<DirectorCard, int> orig, DirectorCard self)
{
  return orig(self);
}
```
## Creating AssetBundles
This is assuming you already have the Unity editor installed as well as a project created.
- Install the `Addressables` package if it's not already installed (`Window -> Package Manager`)
- You should now see a checkbox with `Addressables` next to it when inspecting a prefab
- Check the box and rename it if you'd like
- Press the `Select` button after checking the box or go to `Window -> Asset Management -> Addressables -> Groups` to access the addressables window
- You should see the added content in the group, if everything looks good, press `Build -> New Build -> Default Build Script` to create your asset bundle
- The bundle by default will be in `ProjectName -> Library -> com.unity.addressables -> aa -> Windows -> StandaloneWindows64` with a long name including "defaultlocalgroup", you can rename this bundle to use in your mod.

For these steps along with example pictures, it's in the modding discord [here](https://discord.com/channels/562704639141740588/562704639569428506/1409744488762703983)

## Releasing your mod

_This is an aggregation of other content scattered around, with additional info_

Some of this content also exists in Creating Asset Translation, but is more specific to language based mods.

### Building Your Mod

In Visual Studio, you can right click on your solution and hit `Build`, or use the shortcut `Ctrl+Shift+B`.

This will generate a `.dll` file.

### Testing Unpackaged Mod

Right click your solution and click `Open Folder in File Explorer`.

Go to the folder, `bin`, `Debug`, `netstandard2.0`, and your `.dll` file should appear.

* To test it, in your mod manager, go to `Settings`, `Browse profile folder`, and navigate over to `BepInEx`, `plugins`. 
* Copy your `.dll` file into the `plugins` folder, then launch the game.
  * You should place your `.dll` in a directory within that plugins folder (see the other plugins and how they do it). 

Once you are happy with your mod, we'll prepare it for uploading to [Thunderstore](https://thunderstore.io/)

### Packaging your mod

At a high level we need to place these things in a zip, but we will prepare them in a folder:

* Thunderstore files
* Your `dll` and dependencies

Here's the structure you should end up with, pre-zipping things:

```bash
ParentFolderWeWillNotZip/
  icon.png
  manifest.json
  README.md
  CHANGELOG.md
  YourModName/
    YourModName.dll
    your_asset_bundle.bundle
```

* Select all of the files, your inner folder, and zip them. 
* When you open your zip, those files should appear with the same structure. 
  * If they are inside a folder, Thunderstore won't see them. 
  * Then simply head on over to the Upload page, select the `.zip` file, fill out other data and submit.

### ⚠️ Double check your package

* I recommend making a new profile where you install your mod from the packaged version (zip) , this will help catch any snafus with
placing things in the wrong directory (sound banks / asset bundles)

Some helpful notes and caveats about the files below.

### Thunderstore files

* Refer to the [Thunderstore Guidelines](https://thunderstore.io/package/create/docs/) for specific files needed when uploading a mod
  * :warning: These file names are sensitive

#### manifest.json

* Make sure you include your dependencies here
* To find the dependency string for your mod, you can view that on that mod's page.
  * Example for [LanguageAPI](https://thunderstore.io/package/RiskofThunder/R2API_Language/) would give us `RiskofThunder-R2API_Language-1.0.1` (at time of writing)

#### Readme

This is the up front documentation for your mod.

1. Uses the **validator** to check your markdown
2. Image links _must_ be external at the moment, you cannot refer to them within a directory in your mod

#### icon.png

This is the logo for your mod, remember how users will view what you place, often times humans will prefer a human generated icon (even if ugly) over whatever gemini gave you (especially if you leave the AI watermark.......)

#### Changelog

Captures changes to your mod, be a good steward and include this.

### Your mod's logic and assets

* Nest these under a directory with your mod's name
* R2ModMan will also unpack any inner directories within that directory, so your asset bundle resolution may have to deal with the bundle existing as a sibling to the `.dll` rather than a child of the directory.

## Updating the mod

* In your project, it's important to increment the `PluginVersion` number.
* Outside of that, you simply need to increment the version number in your `manifest.json` file, zip everything, and upload again.

## Helpful Scripts

Here's a couple of the scripts/build tools I've used to make all of this simpler.

### Release build step

In VisualStudio, I have a separate build target called `Release` that does the packaging and zipping for me.

It requires a couple of things:

1. Updating your property group to include your base name and a version
```xml
	<PropertyGroup>
		<TargetFramework>netstandard2.1</TargetFramework>
		<LangVersion>preview</LangVersion>
		<AllowUnsafeBlocks>true</AllowUnsafeBlocks>

		<!-- For thunderstore package setup -->
		<Version>0.1.2</Version>
		<PackageBaseName>YourCoolMod</PackageBaseName>
	</PropertyGroup>
```
2. Your files for the Thunderstore parts need to exist in some directory, we will copy them to the final product
```bash
  ThunderstoreFiles/
    icon.png
    README.md
    manifest.json
    CHANGELOG.md
```


```xml
	<Target Name="Release_Package" AfterTargets="Build" Condition="'$(Configuration)'=='Release'">
		<PropertyGroup>
			<!-- $(TargetDir) already points to bin\Release\netstandard2.1\ -->
                        <!-- This creates the parent dir , the one we do not zip -->
			<PackageDir>$(TargetDir)Package\</PackageDir>


                        <!-- Where your thunderstore files live -->
			<ThunderstoreMeta>$(ProjectDir)\ThunderstoreMeta\</ThunderstoreMeta>

                        <!-- To create and place our dll in an inner folder -->
			<InnerFolder>$(PackageBaseName)\</InnerFolder>
			<InnerDest>$(PackageDir)$(InnerFolder)</InnerDest>

			<!-- From MyMod_0.0.0 to MyMod_0_0_0 -->
			<PackageName>$(PackageBaseName)_$([System.String]::Copy('$(Version)').Replace('.','_'))</PackageName>
			<ZipPath>$(TargetDir)$(PackageName).zip</ZipPath>
		</PropertyGroup>

		<!-- Clean fresh -->
		<RemoveDir Directories="$(PackageDir)" Condition="Exists('$(PackageDir)')" />
		<MakeDir Directories="$(PackageDir)" />

		<!-- DLL In inner folder -->
		<ItemGroup>
			<DllFiles Include="$(TargetPath)" />
		</ItemGroup>
		<Copy SourceFiles="@(DllFiles)" DestinationFolder="$(InnerDest)" SkipUnchangedFiles="true" />

		<!-- Thunderstore Mod Extras -->
		<ItemGroup>
			<ThunderstoreFiles Include="$(ThunderstoreMeta)README.md" Condition="Exists('$(ThunderstoreMeta)README.md')" />
			<ThunderstoreFiles Include="$(ThunderstoreMeta)manifest.json" Condition="Exists('$(ThunderstoreMeta)manifest.json')" />
			<ThunderstoreFiles Include="$(ThunderstoreMeta)icon.png" Condition="Exists('$(ThunderstoreMeta)icon.png')" />
			<ThunderstoreFiles Include="$(ThunderstoreMeta)CHANGELOG.md" Condition="Exists('$(ThunderstoreMeta)CHANGELOG.md')" />
		</ItemGroup>

		<!-- copy our files -->
		<Copy SourceFiles="@(ThunderstoreFiles)" DestinationFolder="$(PackageDir)" SkipUnchangedFiles="true" />

		<!-- Asset bundles -->
		<ItemGroup>
			<AssetBundles Include="$(ProjectDir)\assetbundles\mybundle.bundle" />
		</ItemGroup>
		<!-- Has to land next to dll -->
		<Copy SourceFiles="@(AssetBundles)" DestinationFolder="$(InnerDest)" SkipUnchangedFiles="true" />

		<!-- Zipadelphia -->
		<Delete Files="$(ZipPath)" ContinueOnError="true" Condition="Exists('$(ZipPath)')" />
		<ZipDirectory SourceDirectory="$(PackageDir)" DestinationFile="$(ZipPath)" Overwrite="true" />
		<Message Text="Packaged $(ZipPath)" Importance="High" />
	</Target>
```

This should result in the following structure in your `bin/Release/netstandard2.1` directory

```bash
Package/
  CHANGELOG.md
  icon.png
  manifest.json
  README.md
  MyMod/
   MyMod.dll
   mybundle.bundle

MyMod_0_0_0.zip
```

That zip file should be enough to upload to Thunderstore 🤞 
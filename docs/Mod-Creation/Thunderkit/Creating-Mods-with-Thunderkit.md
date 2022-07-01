# Creating Mods with Thunderkit

Thunderkit is a tool that assimilates mod creation into the Unity Editor. It gives access to all Components, ScriptableObjects, [and even allows for the loading of Risk of Rain 2 assets with AssetRipper](https://github.com/AssetRipper/AssetRipper). It allows for more efficient and organized creation of characters, projectiles, items, maps, and many, many more things. In short, it reduces the workload of creation inside of Visual Studio and emulates a similar developing environment to the one used by Hopoo Games.

Some examples of this tool's implementation:

![](https://i.imgur.com/MIlX7YY.png)
![](https://i.imgur.com/eAQ4IlO.png)
![](https://i.imgur.com/PuKKTFi.png)
## Getting Started
You will need [Unity 2018.4.16f](https://download.unity3d.com/download_unity/e6e9ca02b32a/Windows64EditorInstaller/UnitySetup64-2018.4.16f1.exe) to get started with this. Follow the guide below until at least 3:31.
 
[![Rain of Stages Introductory Tutorial](http://img.youtube.com/vi/T9T38ImFdtg/0.jpg)](http://www.youtube.com/watch?v=T9T38ImFdtg "Rain of Stages Introductory Tutorial")

While Rain of Stages is not necessary if you aren't creating a stage, it can still be helpful because it imports some things like object layers.

Thunderkit is now installed in your project and you should be able to use the Risk of Rain 2 assembly. **Note that this does not import the Risk of Rain 2 assets. Those must be imported using [AssetRipper](https://github.com/AssetRipper/AssetRipper).**

If the RoR2 assembly does not appear to be loaded in your project, Navigate to packages under your project tab. If you see a Risk of Rain 2 folder and an Assembly-CSharp inside it, right click it and press "Reimport". 
![](https://i.imgur.com/9q7QBlP.png)
*(If you do not see this, Go to Tools > Thunderkit > Settings and make sure that you added the directory and pressed "Locate Game".)*


### Creating Assemblies (.dll files)
Go to Tools > Thunderkit > Packages and install BepInEx and anything else your mod is dependent on. In the folder you are setting up your mod in, create an Assembly Definition. Check the boxes "Auto Referenced" and "Override References". Now you can fill in your references in the inspector.
Your assembly is a .csproject, and its references are the equivalent of your project references in Visual Studio. An assembly definition will include all .cs files inside of the folder and any inside of subfolders.

*(Thunderkit may not grab everything your project is dependent on from your game's folders. If you need to add those as well, simply drag them from your game into Packages > Risk of Rain 2 and refresh.)*

If you are importing your project into Thunderkit, good news! From this point, you can simply drag all of your .cs files into the folder that your assembly definition is set up in.

If you are brand new to modding and are starting out in Thunderkit, refer to the [In-Depth First Mod page](https://github.com/risk-of-thunder/R2Wiki/wiki/%5BIn-depth%5D-First-mod), ignoring step 1, adding references as mentioned above instead of how it's done step 2, and building as this guide states further down.

### Creating Manifests/Asset Bundles
The manifest is a component of how Thunderkit does building. To create one, look inside of Packages > Thunderkit > Editor > Templates > BepInEx > Manifests. Drag this to the folder your assembly definition is in. Click on it and fill it out with the information from your mod. For dependencies, you must look for them in the Packages folder in the Project tab and drag it into the box. If your mod does not have an assetbundle, leave it blank or delete it. Otherwise, store any assets you intend to use in a single folder and drag it into the blank.

![](https://i.imgur.com/9ushkjg.png)
![](https://i.imgur.com/GcvKKVI.png)Do not mess with the staging paths unless you know what you are doing. All of it is in the documentation, which can be viewed by going to Tools > Thunderkit > Documentation.

If you are an experienced modder, the assembly set up in Unity is different from the one you have set up before. Under most circumstances, this assembly does not accept embedded resources. Any resources, such as assetbundles or soundbanks, must be loaded externally.

### Building
For a simple build process, grab a copy of the Stage pipeline from the templates folder mentioned earlier and plug your manifest into it. For more complex build processes such as automatic deployment to your game folder, refer to the tutorial mentioned earlier or the Thunderkit Documentation. For this simple one, though, the built files are located in your Unity project's root folder in Thunderkit/Staging.

Pipelines are 100% customizable. You can make your own to suit your needs.


## Tips for Mods in Thunderkit
Many mods that are primarily based out of their assembly are very messy. Most build their content at runtime, which inflates loading times and honestly can be  either *very* messy to code or extremely complex and hard to understand. If you are porting your mod to thunderkit, you should go in with the mindset to reduce the amount done at runtime as much as possible.

### Using your own Content Pack
Following the anniversary update, Risk of Rain 2 added content packs that modders can use to easily add their content to the game without the help of R2API.
To make a SerializableContentPack, Right Click in Project Tab > Create > RoR2 > SerializableContentPack.
Store this inside of your assetbundle and plug in any of the listed objects into its proper place.

![](https://i.imgur.com/6m21EJK.png)

*(TODO: As of Patch 1.2.3, the following is outdated: Two things of note about this. First, Unlockable Defs cannot be created in the Unity Editor. There are ways of getting around this by importing one that already exists using the [AssetRipper](https://github.com/AssetRipper/AssetRipper). Second, EffectDefs are also included in the ContentPack, but do not show up in the menu and must be added through code. Both of these issues will be fixed in the next patch.)*

To load an assetbundle/contentpack, these two classes can be used by calling `Assets.PopulateAssets()` near the beginning of your `Awake()` method and `ContentPackProvider.Initialize()` at the end of it.

    using RoR2.ContentManagement;
    using System.Collections;
    using System.Reflection;
    using UnityEngine;
    using Path = System.IO.Path;
    
	namespace YourNamespace
	{
		public static class Assets
		{
			public static AssetBundle mainAssetBundle = null;
			//the filename of your assetbundle
			internal static string assetBundleName = "";

			internal static string assemblyDir
			{
				get
				{
					return Path.GetDirectoryName(Main.Info.Location);
				}
			}
	
			public static void PopulateAssets()
			{
				mainAssetBundle = AssetBundle.LoadFromFile(Path.Combine(assemblyDir, assetBundleName));
				ContentPackProvider.serializedContentPack = mainAssetBundle.LoadAsset<SerializableContentPack>(ContentPackProvider.contentPackName);
			}
		}
	
		public class ContentPackProvider : IContentPackProvider
		{
			public static SerializableContentPack serializedContentPack;
			public static ContentPack contentPack;
			//Should be the same names as your SerializableContentPack in the asset bundle
			public static string contentPackName = "";
			
			public string identifier
			{
				get
				{
					//If I see this name while loading a mod I will make fun of you
					return "ModName";
				}
			}
	
			internal static void Initialize()
			{
				contentPack = serializedContentPack.CreateContentPack();
				ContentManager.collectContentPackProviders += AddCustomContent;
			}
	
			private static void AddCustomContent(ContentManager.AddContentPackProviderDelegate addContentPackProvider)
			{
				addContentPackProvider(new ContentPackProvider());
			}
	
			public IEnumerator LoadStaticContentAsync(LoadStaticContentAsyncArgs args)
			{
				args.ReportProgress(1f);
				yield break;
			}
	
			public IEnumerator GenerateContentPackAsync(GetContentPackAsyncArgs args)
			{
				ContentPack.Copy(contentPack, args.output);
				args.ReportProgress(1f);
				yield break;
			}
	
			public IEnumerator FinalizeAsync(FinalizeAsyncArgs args)
			{
				args.ReportProgress(1f);
				yield break;
			}
		}
	}
Notes:
* Main refers to your class that inherits from BaseUnityPlugin.
* If you get an error which says "An Object Reference is required for the non-static field, method, or property 'BaseUnityPlugin.Info'" you can simply create a static field of type PluginInfo, and set its value to the instance of the main class' plugin info. and change "Info" to your static field's name. Example can be seen bellow

		public static PluginInfo pluginInfo;
		public void Awake()
		{
			pluginInfo = this.Info;
		}
		

### Using Custom Components/ScriptableObjects in the Editor
When using custom MonoBehaviour, the file needs to have the same name as the MonoBehaviour for it to load in the editor. You can then attach those to GameObjects.
As for ScriptableObjects, if you want them to show up in the Create Asset Menu, it also needs to be in a file name after the class, and the class must have `[CreateAssetMenu(filename = "The Default Filename when you create one", menuName = "OptionalCategoryName/NameInTheMenu", order = 0]` where order is where in the list it should show up. Make sure that both of these are in a folder managed by your assembly definition!

### Using a Stubbed Assembly
If you do not know anything about a stubbed assembly, ask in the Discord, but it's essentially a modified version of Assembly-CSharp that makes some things easier to code. **Do not replace your imported Assembly-CSharp with this as it will make your components/scriptableobjects extremely difficult to work with!** Instead, go to your currently imported Assembly-CSharp and change the platform for the plugin to be Editor only. Next, name your stubbed assembly something else and put it in the same folder as Assembly-CSharp and set this version to be Standalone only. Make your AssemblyDefinition references the stubbed assembly instead of the other one.

### Using Stubbed Shaders
When ripped from the game, shaders are compiled and cannot be used in Unity. Stubbed Shaders allow you to set the properties of a material in the Unity Editor instead of in code. Get the stubbed hgshaders [here.](https://drive.google.com/drive/folders/1ndCC4TiN06nVC4X_3HaZjFa5sN07Y14S?usp=sharing) These can also be used to replace imported game shaders when importing the game's assets to make them load their properties. To switch these to the unstubbed shader on runtime, use this method.

    public static void ApplyShaders()
    {
        var materials = Assets.mainAssetBundle.LoadAllAssets<Material>();
        foreach (Material material in materials) 
	        if(material.shader.name.StartsWith("StubbedShader"))
                material.shader = Resources.Load<Shader>("shaders" + material.shader.name.Substring(13));
    }

### Oh God All of My Components are Missing Monoscripts
Reimport Assembly-CSharp.

![Dummy.](https://i.imgur.com/dpgS2vz.png)
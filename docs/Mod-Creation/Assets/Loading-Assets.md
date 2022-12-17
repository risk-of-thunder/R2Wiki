# Loading Assets

## Overview

This guide covers a method for loading different resources (Such as Soundbanks, Assetbundles, and Language files) without the need to Embed your resources.

### Why do things this way?

-   Loading assets from Assetbundles directly consumes less memory overall.
-   No need to rebuild the DLL every time you build your Assetbundle or SoundBank, you can simply build the bundle and have it be moved directly to your mod's folder.
-   Having language files allows anyone to contribute to them via Github, without the need to touch code.
-   Essential for Thunderkit mods, as Thunderkit doesnt allow you to embed resources to the DLL.

### Folder Structure

The structure of your Plugin folder is one of the most important things to load your resources directly, For ease of loading stuff like Bundles, Banks and Language files, it is extremely recommended to leave all the resources you want to load directly on the same directory as your DLL, or on a folder that's in the same directory as your DLL.

![](https://i.gyazo.com/7944daadc43378c1358bad5f6896bab8.png)

###### example folder structure of Starstorm2 nightly, all the assetbundles are in the assetbundles folder, all the language files are in languages, and the soundbank is in soundbanks.

The easiest and safest way of getting the location of your DLL is thru your main plugin, via the Info property. To easily get access to the Info property, you can create a static field in your MainPlugin of type PluginInfo, and set it on Awake.

```csharp
public class MainClass : BaseUnityPlugin
{
	public static PluginInfo PInfo {get; private set;}
	public void Awake()
	{
		PInfo = Info;
	}
}
```

### Loading AssetBundles

For loading the AssetBundle for your mod, you can use the following class as an example.

```csharp
using UnityEngine;
using System.IO;

//Static class for ease of access
public static class Assets
{
	//The mod's AssetBundle
	public static AssetBundle mainBundle
	//A constant of the AssetBundle's name.
	public const string bundleName = "mybundle";
	// Not necesary, but useful if you want to store the bundle on its own folder.
	// public const string assetBundleFolder = "AssetBundles";

	//The direct path to your AssetBundle
	public static string AssetBundlePath
	{
		get
		{
			//This returns the path to your assetbundle assuming said bundle is on the same folder as your DLL. If you have your bundle in a folder, you can uncomment the statement below this one.
			return Path.Combine(Path.GetDirectoryName(MainClass.PInfo.Location), myBundle);
			//return Path.Combine(Path.GetDirectoryName(MainClass.PInfo.Location), assetBundleFolder, myBundle);
		}
	}

	public static void Init()
	{
		//Loads the assetBundle from the Path, and stores it in the static field.
		mainBundle = AssetBundle.LoadFromFile(AssetBundlePath);
	}
}
```

You can also load multiple assetBundles for your mod, but the loading of multiple bundles is outside of the scope of this guide.

### Loading SoundBanks

#### Loading SoundBanks from a folder

```csharp
using System.IO;

public static class SoundBank
{
	//The folder where your SoundBanks are, it is required for them to be in a folder.
	public const string soundBankFolder= "SoundBanks";
	public const string soundBankFileName = "MySoundBank.bnk";
	public const string soundBankName = "MySoundBank";
	public static string SoundBankDirectory
	{
		get
		{
			return Path.Combine(Path.GetDirectoryName(MainClass.PInfo.Location), soundBankFolder);
		}
	}

	public static void Init()
	{
		var akResult = AkSoundEngine.AddBasePath(assetsFolderFullPath);
		if (akResult == AKRESULT.AK_Success)
		{
			Log.Info($"Added bank base path : {assetsFolderFullPath}");
		}
		else
		{
			Log.Error(
				$"Error adding base path : {assetsFolderFullPath} " +
				$"Error code : {akResult}");
		}

		akResult = AkSoundEngine.LoadBank(SoundBankFileName, out _soundBankId);
		if (akResult == AKRESULT.AK_Success)
		{
			Log.Info($"Added bank : {SoundBankFileName}");
		}
		else
		{
			Log.Error(
				$"Error loading bank : {SoundBankFileName} " +
				$"Error code : {akResult}");
		}
	}
}
```

#### Loading Soundbanks with R2API

Loading a soundBank with R2API is trivial, since you just need to put your soundBank file in the bepinex/plugins folder and change its extension from `.bnk` to `.sound`. R2API will load it

### Loading Language Files

Loading LanguageFiles requires a special setup of the Languages folder, an example "Language Folder Tree" can be seen here.
![](https://cdn.discordapp.com/attachments/575431803523956746/946545906658975744/LangTreeExample.png)

###### Example language folder tree of starstorm, Notice how the root folder named Languages contains a set of subFolders, and each subFolder has the name of one of the Languages in ROR2, en is for English, es-419 for spanish, etc. While each of those subFolders has a .json file with the Language Tokens and strings.

#### Loading Language Files with RoR2's Language system

Loading your language files with a delegate within the game allows them to be loaded directly to ROR2's language systems, which means you're avoiding depending on LanguageAPI for loading tokens.
Keep in mind this has to be before the Language system is initialized.

```csharp
using RoR2;
using System.Collections.Generic;

public static class Languages
{
	//The name of the folder that contains the language tree
	public const string LanguageFolder = "Languages";
	public string RootLanguageFolderPath => Path.Combine(Path.GetDirectoryName(MainClass.PInfo.Location), LanguageFolder);

	public static void Init()
	{
		//This makes sure that there's no conflicts with R2API's Language systems. Important!
		if(Directory.Exists(RootLanguageFolderPath))
		{
			RoR2.Language.collectLanguageRootFolders += AddOurFolder;
		}
	}

	private static void AddOurFolder(List<string> list)
	{
		//Adding our root folder with the languages means ror2 will automatically load our language files.
		list.Add(RootLanguageFolderPath);
	}
}
```

#### Loading LanguageFiles with R2API

R2API automatically load any file with extension .language if it detects one in the bepinex/plugins folder
[More details in this page](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Localization/)

If you want to load language files manually instead, you can do something like this:

```csharp
//The code below assumes you only have 1 language file, and said file is in the same directory as your DLL.
using R2API;
public static class Languages
{
	public const string LanguageFileName = "LITLanguage.language";

	public static void Init()
	{
		LanguageAPI.AddPath(Path.GetDirectoryName(MainClass.PInfo.Location), languageFileName);
	}
}
```

Either way, when deploying your mod, the language files should be placed in the same folder as your mod's .dll with the correct folder structure or your mod won't be able to access them.

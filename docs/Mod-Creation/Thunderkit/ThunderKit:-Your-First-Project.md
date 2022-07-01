# ThunderKit: Your First Project
So, you want to begin a ThunderKit project but dont know what to do or where to start with? no problems, this guide will look into setting up your basic ThunderKit project. However, this guide will not cover the concepts of your mod's Manifest, Pipeline building, and so on

(This guide uses TK 5.2.2 and RoR2ImporterExtensions 1.0.0)

## What you'll need
* [Unity Version 2019.4.26f1](https://download.unity3d.com/download_unity/e0392c6b2363/Windows64EditorInstaller/UnitySetup64-2019.4.26f1.exe) (*)
* [Unity Hub](https://unity.com/unity-hub) (Extremely recommended)
* [GIT](https://git-scm.com)
* [GithubDesktop](https://desktop.github.com) (Recommended for managing your projects, as its very beginner friendly)
* Basic unity knowledge
(*) Specifically the version that's listed under the RiskOfRain2.exe's Details. RoR2ModCord's !unityversion command is always kept up to date

## Steps
* [Step 0](https://github.com/risk-of-thunder/R2Wiki/wiki/ThunderKit:-Your-First-Project#step-0-setting-up-the-repository): Setting up your Git repo (Completely optional, if youre not interested, skip to Step 1)
* [Step 0.5](https://github.com/risk-of-thunder/R2Wiki/wiki/ThunderKit:-Your-First-Project#step-05-wwise-integration): Wwise Integration
* [Step 1](https://github.com/risk-of-thunder/R2Wiki/wiki/ThunderKit:-Your-First-Project#step-1-creating-the-project--installing-thunderkit): Creating the project & installing ThunderKit
* [Step 2](https://github.com/risk-of-thunder/R2Wiki/wiki/ThunderKit:-Your-First-Project#step-2-installing-ror2importextensions): Installing RoR2ImportExtensions
* [Step 3](https://github.com/risk-of-thunder/R2Wiki/wiki/ThunderKit:-Your-First-Project#step-3-import-configuration-explained): Import Configuration, Explained
* [Step 4](https://github.com/risk-of-thunder/R2Wiki/wiki/ThunderKit:-Your-First-Project#step-4-the-thunderkit-package-manager): The ThunderKit Package Manager

## Step 0: Setting up the Repository

Setting up your project's repository is not exactly the same as setting up a repository for a visual studio mod, this is due to the fact that unity contains a lot of files that are set up or created on a whim when the project is either first opened, or while its opened.
This step uses GithubDesktop as it's guide, but you can use other desktop clients (Like GitKraken) or do pure GIT in the command line

* Begin by creating a new repository, go to File -> New Repository

![](https://cdn.discordapp.com/attachments/575431803523956746/976868775729176616/unknown.png)

* A window like this will pop up, fill it with the necesary information such as the repository's name, description, the path where the local copy will be stored, etc.
Note that we're using the Git ignore for Unity, this is important as it'll massively reduce the size of your repo in the internet by ignoring the files that unity creates on demand

![](https://cdn.discordapp.com/attachments/575431803523956746/976872444642066472/unknown.png)

Once you hit "Create Repository", the repository will be created, you can then hit "publish repository" and it'll be published to github under your account.

### .gitignore modification
It is recommended to modify the default .gitignore file that comes provided with the repository. This is a sub-guide on what to modify

* We will now setup the GitIgnore of the project. Open the file called ".gitignore" in the explorer with your preferred text editor. you can reach the .gitignore file by going into Repository -> Show in Explorer

![](https://cdn.discordapp.com/attachments/575431803523956746/976872707566211114/unknown.png)

![](https://cdn.discordapp.com/attachments/575431803523956746/976873096181088256/unknown.png)

(The .gitignore format uses `#` as comments)
* Once opened, you'll see something along the lines of this. begin by removing the first `/` of every gitignore entry.

![](https://cdn.discordapp.com/attachments/575431803523956746/976874589995040848/unknown.png)

Since we're using ThunderKit to mod the game, it is recommended to append the following at the end of the .gitignore file

	#RoR2 Packages Folder
	Packages/Risk of Rain 2
	#ThunderKit root folder & Logs
	ThunderKit/
	Assets/ThunderKitSettings/Logs/
	
If you're going to use the Wwise integration, append this at the end of the .gitignore file

	##-------##
	## wwise ##
	##-------##
	WwiseProject/.cache/
	WwiseProject/*.prof
	WwiseProject/*_WwiseProject.*.validationcache
	WwiseProject/*_WwiseProject.*.wsettings
	WwiseProject/**/*.akd
	Assets/Wwise/Editor/ProjectData/AkWwiseProjectData.asset*
	#notes:
	#1) if any other files go in Assets/Wwise/Editor/ProjectData/ besides AkWwiseProjectData.asset*,
	#then don't ignore Assets/Wwise/Editor/ProjectData.meta. If AkWwiseProjectData.asset is the only item in there,
	#then, it's OK to ignore this file.
	Assets/Wwise/Editor/ProjectData.meta
	Assets/Wwise/Deployment/Plugins/Mac/*
	#Integration zips
	WwiseUnityIntegration_Windows_Src.zip
	WwiseUnityIntegration_Mac_Src.zip

## Step 0.5: Wwise Integration
The process of integrating Wwise into your unity project is very recommended, the process is not precisely simple, so a different guide will be created in the future.
Assuming youre in the Risk of Rain 2 Modding Discord, click [here](https://discord.com/channels/562704639141740588/697582823925743657/858129809786667028) for a text guide in the #map-creation channel

## Step 1: Creating the project & installing ThunderKit
Once you're ready to create the project, open UnityHub, go to Projects and click "New Project"

![](https://cdn.discordapp.com/attachments/575431803523956746/976877905571373107/unknown.png)

Once the project creation wizard pops up, be sure to choose the editor version that matches the game! and in template,select the "3D" template

![](https://cdn.discordapp.com/attachments/575431803523956746/976878098450645002/unknown.png)

(For people who followed step 0: notice how i'm creating the project *inside* the repository, this is important)
Hit "Create Project" and wait for the editor to appear
Once the editor appears and your project is finished intializing, its time to install ThunderKit!
 1. Go to Window -> Package Manager

![](https://cdn.discordapp.com/attachments/575431803523956746/976879397833441321/unknown.png)

 2. On the upper left corner, hit the plus sign and click `Add package from git URL`

![](https://cdn.discordapp.com/attachments/575431803523956746/976879831109222410/unknown.png)

A field will appear where you need to input a git URL, go to [this repository](https://github.com/PassivePicasso/ThunderKit)
### Selecting the right version for you
Here you have two options. You can either:
 1. Use the latest, bleeding edge build (https://github.com/PassivePicasso/ThunderKit.git) (not recommended)
 2.  Use the latest release (recommended)
 If youre going to use the latest release, hover to the right side of the repository and hit the latest release, copy the link above and paste it in the field in the unity package manager, before hitting the "Enter" key, type a Hashtag then followed by the tagged version of your desire (IE: #5.2.1). hit "Add" and wait

 ![](https://cdn.discordapp.com/attachments/575431803523956746/976881334595256431/unknown.png)

(note: at the time of writing, ThunderKit version 5.2.2 has not been released, this version (or greater) is required for the importing process to succeed and work properly. If youre reading this and 5.2.2 has not been released yet, just use the latest bleeding edge build)

 If everything goes smoothly, you should see the ThunderKit Settings window pop up.

 ![](https://cdn.discordapp.com/attachments/575431803523956746/976881547649093652/unknown.png)

 Youre now ready to start modding with ThunderKit, however, it is recommended that you install the RoR2 Import Extensions before importing the game to the project.

## Step 2: Installing RoR2ImportExtensions
The Risk of Rain 2 Import Extensions is a `ThunderKit Extension` that extends the import process to enable extremely simple import process and automates some small annoyances that come with setting up your project to work properly.

It is recommended that you install it in the same manner as you installed ThunderKit, the RoR2 Import Extensions repository can be found [here](https://github.com/risk-of-thunder/RoR2ImportExtensions)

## Step 3: Import Configuration, Explained
With RoR2ImportExtensions installed, open the ThunderKitSettings window (Tools -> ThunderKit -> Settings), there's a setting called "Import Configuration", click it.

![](https://cdn.discordapp.com/attachments/575431803523956746/976883106332147823/unknown.png)

Import configuration is a new feature introduced in ThunderKit 5.0.0 that allows the importing process of a game to be extensible, these extensions can be created by anyone and as such allows for complete customization and modification of how importing works. as a matter of fact, RoR2ImportExtensions adds 11 new import options to ensure your project is as stable as possible.

At first glance, the options in the import configuration may look intimidating. This guide explains each one of them in mild detail, as knowing what each does is important

![](https://cdn.discordapp.com/attachments/575431803523956746/976884026512121986/unknown.png)

1. Post Processing Unity Package Installer (RoR2ImportExtension):
	Installs the PostProcessing package version 2.3.0 into the project, recommended to leave on if you're going to work with PostProcessing
2. TextMeshPro Uninstaller (RoR2ImportExtension):
	Removes the TMP package and allows the .dll version to come thru into the project, recommended to leave on as the one in RoR2 is slightly modified and not having it can cause instability
3. Assembly Publicizer (RoR2ImportExtension):
	Automatically publicizes the assemblies listed in "Assembly Names", these assemblies will be publicized with specific arguments using N-Strip, recommended to leave on
4. Import Assemblies (Default): Imports the assemblies of the game into the project, the assembly identity algorithm will define what kind of GUIDs the assemblies will obtain, it is recommended to set this to "AssetRipper Compatibility".
5. RoR2 LegacyResourceAPI Patcher (RoR2ImportExtension):
	Patches the LegacyResourceAPI to increase stability, as its incredibly unstable normally since it can cause hangs and errors in the editor
6. Import Project Settings (Default):
	Imports the game's project settings (such as nav mesh agents, layers, physics layers, tags, etc) into your project, recommended to leave on and choose "Everything"
7. Create Game Package (Default):
	Creates a package.json file for the game's assemblies, recommended to leave on}
8. Import Addressable Catalog (Default)
Imports the Addressable Catalog into your project, very recommended, as it enables the Addressables functionality of ThunderKit
9. Configure Addressable Graphics Settings (RoR2 Import Extension): 
	Shader related, recommended to leave on
10. Ensure RoR2 Thunderstore Source (RoR2 Import Extension):
	Creates a new Thunderstore Source and sets it's url to `https://thunderstore.io` , recommended to leave on
11. Install BepInEx (RoR2 Import Extension):
	Installs the latest BepInExPack into the project, recommended to leave on
12. Install FixPluginTypesSerialization (RoR2 Import Extension):
	Installs FixPluginTypesSerialization into the project, recommended to leave on
13. Install R2API (RoR2 Import Extension):
	Installs the latest R2API into the project, recommended to leave on
14. Install RoR2 Compatible Unity Multiplayer HLAPI (RoR2 Import Extension)
	Installs the RoR2MultiplayerHLAPI, Extremely recommended to leave on, as the hlapi runtime dll ror2 comes with is modified and will cause issues in weaving the assemblies and assets
15. Install RoR2EditorKit (RoR2 Import Extension):
	Installs the latest RoR2EditorKit, recommended to leave on
16. Get Bitness (Default):
	Sets wether the game is 32 or 64 bits, leave this on

Once youre ready, click the "ThunderKit Settings" settings option, and hit browse to locate the game's Executable file,

![](https://cdn.discordapp.com/attachments/575431803523956746/976887827331694633/unknown.png)

Once ready, hit import! be patient as the import process may take a while

While youre importing, you may come into this window telling you an API Update is required, ignore this and hit "No Thanks"

![](https://cdn.discordapp.com/attachments/575431803523956746/976888385392230400/unknown.png)

## Step 4: The ThunderKit Package Manager
ThunderKit comes with it's own PackageManager (Tools -> ThunderKit -> Packages). this package manager can be used to install more mods into your project (IE: installing Risk of Options to enable your mod to be configured with it).

The package manager can be used to install both RoR2 Mods, and more ThunderKit extensions

![](https://cdn.discordapp.com/attachments/575431803523956746/976889546056814662/unknown.png)
This guide will cover how you can upgrade your ThunderKit project from using the legacy R2API package, to using the new Split Assemblies package.

### ***__The what now?__***
The split assemblies package is the newest major release of R2API, this update took all the individual important aspects of R2API, and split them into their own separate assemblies, this reduces load times slightly, while increasing the development speed for new features, alongside quicker updates instead of waiting for a bunch of updates to pile up.

Using the split assemblies update is useful if you dont want to have a dependency on all of the 21 dependencies the legacy package has (The legacy package contains a dependency for each and every submodule that was originally part of it, this is to avoid errors and crashing)

This guide will cover the upgrade process done by Nebby, on his upcoming Revenant mod's Thunderkit project.

## Step 1: Preparing your project
First and foremost, ALWAYS backup your projects before doing any kind of upgrade process! the RiskOfThunder team is NOT responsible for blunders or data loss resulting from these blunders. We're always happy to help if issues do arise.

For migrating to the Split assemblies update, you'll require the following things:

	ThunderKit version 6.0.0 or Greater
	RoR2ImporterExtensions version 1.3.0 or Greater
	RoR2EditorKit version 3.5.0 or Greater

If you dont have these versions, you can update ThunderKit by modifying the Package.Json file in your packages folder, alongside removing the package lock for said versions, make sure you ask around if you dont know what this means.

For the importer extensions and the ror2ek, you can either use the Thunderkit Package manager, or use the github releases.

## Step 2: Locating your dependencies
The simplest way to know what dependencies you'll need, is by checking what you're already requesting using SubmoduleDependencies! While the **Revenant mod** unity project doesnt specify any SubmoduleDependencies, one of it's dependants does request them, we'll use these for the guide, but make sure you find yours!

![](https://cdn.discordapp.com/attachments/850538397647110145/1054534872678551692/image.png)

Something to keep in mind: If in your dependencies you have any of the following:

	PrefabAPI
	ItemAPI
	EliteAPI
	LoadoutAPI
	SoundAPI
	TempVisualEffectAPI
	UnlockableAPI
You'll also want to add "ContentManagement" to your dependency list! as the api's listed above depend on it.

## Step 3: The R2API Submodule Installer
The R2API Submodule Installer is a new extension point for ImporterExtensions that installs the selected R2API submodules, keep in mind that you cannot disable the Core module, as it's required for all other submodules to work.

![](https://cdn.discordapp.com/attachments/850538397647110145/1054540869434347590/image.png)

The image may not show it, but i'm selecting the API's i had taken from my submodule dependencies, alongside ContentManagement, as Elites depends on it.
You can disable all other extension points and keep the R2API submodule installer enabled to just execute it, go back to the main ThunderKitSettings, and hit import game.

If during the installation you stumble upon a message regarding an API update, hit "No Thanks". if you hit "I Made a Backup, Go ahead!" the stability of your project WILL decrease significantly.

After a while, you can see that all the submodules i needed have been installed in my project.

![](https://cdn.discordapp.com/attachments/850538397647110145/1054542057798107207/image.png)

## Step 4: The R2API Migrator Wizard
RoR2EditorKit version 3.5.0 adds the R2API Migrator Wizard, a custom wizard that can help during the migration process.

![](https://cdn.discordapp.com/attachments/850538397647110145/1054547263415926806/image.png)

The migrator does the following things:
* If a main class is supplied, and the migrator finds the following text: ``[BepInDependency("com.bepis.r2api", BepInDependency.DependencyFlags.HardDependency)]`` (and only this, it wont accept soft dependency or not specifying the type of dependency). it'll replace said BepInDependency for multiple BepInDependencies that point to each R2API submodule installed in the project.
* If an AssemblyDefinition is supplied, and in the precompiled references has the words ``R2API.dll``, it'll remove said reference and instead add all the R2API submodules installed in the project.
* If a Manifest is supplied, and in the Manifest's Identity's Dependency array is the R2API legacy manifest, it'll remove said dependency and instead add all the R2API submodule's manifests installed in the project.

It is recommended to supply all three, but, in the case of revenant, i only need to migrate my references in the assembly definition.

![](https://cdn.discordapp.com/attachments/850538397647110145/1054551809043210440/Untitled.png)

## Step 5: What now
Assuming the wizard did its job, your mod has been updated to use the new split assemblies system! all you need to do now is to make sure everything's working as intended.
The main class ugprader might cause some new line inconsistencies, make sure to fix these, your IDE should prompt an automatic fix.
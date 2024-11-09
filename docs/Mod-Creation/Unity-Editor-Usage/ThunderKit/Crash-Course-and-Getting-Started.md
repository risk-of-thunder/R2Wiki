# ThunderKit - Crash course and Getting Started

[ThunderKit](https://github.com/PassivePicasso/ThunderKit) is a Mod development Environment that's used for the creation of mods focusing on large amounts of content and with heavy utilization of the Assets system, wether those assets take form of GameObjects or ScriptableObjects. Due to the nature of working within the editor, you get access to all sorts of editor tooling, such as developing mods in a close aproximation to what GearBox uses for developing the game.

![TK_Server_Icon](https://github.com/user-attachments/assets/266bad9c-96ef-4173-a88f-9478b732f481)

## Key Features

Below are a series of images detailing the key features of the ThunderKit environment

![image](https://github.com/user-attachments/assets/4484c388-cfcb-46a6-8f8b-c5d4b0517e51)
![image](https://github.com/user-attachments/assets/68c84bfa-e96f-4197-ab50-a651767f0d06)
![image](https://github.com/user-attachments/assets/156b8ae8-c080-4912-83d5-21c4c9cfe466)
![image](https://github.com/user-attachments/assets/f21db46c-aca1-4c0c-b2e3-6661d3dc0e3e)

## Getting Started

You will need the current unity version used by the game, you can find the current version of unity being used in this [page](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Unity-Version/). This part of the guide will walk you thru the process of creating a Github repository for your mod, alongside creating your project. The guide will not cover how to utilize ThunderKit or setup a basic workflow, if youre looking for a setup that utilizes a well tested and developed Framework Library, look into [Moonstorm Shared Utils Page]()

Note: Any version strings you may see on pictures are purely from the moment this guide was written, these version numbers may change in the future, when in doubt, ask around the modcord to ensure youre using the correct versions of software

### Step 1: Github Repository

This part of the guide asumes youre utilizing a GitGUI, such as [GithubDesktop](https://desktop.github.com/download/)

1. Create and Initialize your repository

Keep in mind, you MUST select the Unity .gitignore, the gitignore file ignores key entries that are excluded from your project to lower the overall size of the repo on the github servers.

![image](https://github.com/user-attachments/assets/21e24a21-c832-4a92-a6d0-add3517677ef)

2. Modify the GitIgnore

The .gitignore file can be modified to ignore more specific files and assets, it is recommended to modify the following entries:

* Remove the beginning '/' character on all paths. this only applies if '/' is the first character of the path.

![image](https://github.com/user-attachments/assets/62cae4c5-60ec-4c7a-8029-ad323c962d02)

Should now look like the next image.

![image](https://github.com/user-attachments/assets/68032677-5b77-4c75-ab40-f9f4ce91425a)

We now have a very basic git repository for our mod using ThunderKit. Now we can proceed to create the project.

### Step 2: The unity project

1. Create a new unity project using the current version of unity. Make sure to utilize the Built In render pipeline option, as Risk of Rain 2 does not use neither the Universal or High Definition render pipelines.

![image](https://github.com/user-attachments/assets/882a6b0a-0034-4137-a8f8-d1accdaade91)

2. Once the project is created you should only have a *couple* of changed files, (Less than 100). if more than 100, or more than a 1000 files are changed, make sure your gitignore file is properly confiured.

![image](https://github.com/user-attachments/assets/6dd94a49-1376-4b22-9862-a5425888d8c2)

### Step 3: Preparing the .gitignore for more entries

You may want to modify your gitignore further to contain the following entries. You can replace the substring `<PROJECT_NAME>` with your project's name

    #Ensures RoR2 assemblies are not redistributed accidentally
    <PROJECT_NAME>/[Pp]ackages/[Rr]isk of Rain 2/*.dll
    <PROJECT_NAME>/[Pp]ackages/[Rr]isk of Rain 2/[Pp]lugins/*.dll
    
    #Client side only thunderkit related files.
    <PROJECT_NAME>/Assets/ThunderKitSettings/EditorInspectorSettings.asset
    <PROJECT_NAME>/Assets/ThunderKitSettings/RoR2.asset
    <PROJECT_NAME>/Assets/ThunderKitSettings/Logs/*
    <PROJECT_NAME>/Assets/ThunderKitSettings/ThunderKit Extensions.asset
    <PROJECT_NAME>/ThunderKit/
    
    #Ensures mod dependencies such as R2API and others are not explicitly added to the github repository to avoid unecesary redistribution
    SS2-Project/Packages/*
    !SS2-Project/Packages/packages-lock.json
    !SS2-Project/Packages/manifest.json

### Step 3.5: Installing the WWise integration. (Optional)

As you may already know, RoR2 utilizes Audiokinetic's Wwise as its audio engine. Integrating wwise into your unity project lets you utilize key Wwise components and features properly during your mod's development. Ask around in the modding discord server for the current wwise version. This step is optional and not mandatory for developing mods with thunderkit.

![image](https://github.com/user-attachments/assets/3d498c34-2af6-4877-96f3-4ab29c1f5a6a)

1. Open the Audiokinetic launcher, and click on the "Unity" section. Locate your unity project and hit "Integrate Wwise in project"

![image](https://github.com/user-attachments/assets/884b3414-13da-440c-ae4a-25e920b1147e)

2. Integrate wwise into the project utilizing the current wwise version, ask around in modcord if you do not know which version we're using.

![image](https://github.com/user-attachments/assets/c7864341-d010-425b-8e51-75b179dd54d9)
![image](https://github.com/user-attachments/assets/a102e853-8e32-4fea-bc07-64fb3be4398d)

3. Once completed, the wwise integration package has been installed.

![image](https://github.com/user-attachments/assets/9f6f5f37-8517-4a08-8566-aed35767dcaf)

4. On the project's root folder, you'll see two zip files, these two zip files can be deleted safely to reduce the size of the repository

![image](https://github.com/user-attachments/assets/11432da4-33c1-41e7-8055-4571b01d5514)

### Step 4: Installing ThunderKit

1. Open the unity project, go to ``Window/Package Manager`` to open the Unity Package Manager

![image](https://github.com/user-attachments/assets/46ac15bd-6def-4221-a3dc-f6df50c74ade)

2. Go to the [ThunderKit Repository](https://github.com/PassivePicasso/ThunderKit). Taking a look at the website you can see a big green ``<> Code`` button. Clicking it and then clicking the copy url button will copy the link to the repository.

![image](https://github.com/user-attachments/assets/c4c098a9-05d6-4e2b-89df-3dd2b0ec089e)

3. On the Package Manager window, hit the ``+`` button, and click the ``Add package from Git URL...`` option

![image](https://github.com/user-attachments/assets/7562cea9-2d0a-4bc1-84e7-2f4ba00d2159)

4. Paste the git URL copied beforehand. As a side note, it is recommeneded to install only the latest tagged release from the repository. To install a specific release tag, append the Tag's name preceded by a ``#`` character.

For example, to download the version 9.1.0 of ThunderKit to your project, you must use the git url ``https://github.com/PassivePicasso/ThunderKit.git#9.1.0``

![image](https://github.com/user-attachments/assets/170d6309-0eb6-4f24-834f-84fdc59815b2)

5. Once the package installs, its settings window should appear, thunderkit has been installed.

![image](https://github.com/user-attachments/assets/cefcc3f1-ae21-408f-9b99-e4e0afe023ed)

### Step 5: Importing the game and the RoR2 Import Extensions.

On a Thunderkit Context, we call a "Game Import" the process of importing the game's assemblies into our thunderkit project, this process is what allows us to utilize ThunderKit to the fullest by using the game's actual assemblies for development.

![image](https://github.com/user-attachments/assets/ec961fac-04b9-4a64-b53e-add98eea1ccc)

By default, ThunderKit comes with a set of basic import steps that are required for succesful game import, however, on Risk of Rain 2, we require extra import steps, we will achieve these steps by utilizing the RoR2 Importer Extensions

1. Open once again the Package Manager window by going to ``Window/Package Manager``

2. Go to the [RoR2 Import Extensions](https://github.com/risk-of-thunder/RoR2ImportExtensions) repository, and download, and install the latest version of the package.

3. Once the import extensions package is open, travel to ``Assets/ThunderKitSettings`` and delete the ``ImportConfiguration`` asset. This asset will be created automatically post deletion.

![image](https://github.com/user-attachments/assets/1a7de857-dc90-4c97-bb90-5e883370eafb)

4. Close the Thunderkit Settings window if its opened. Then open it again and traverse to the Import Configuration section. You should see an expanded selection of import steps.

![image](https://github.com/user-attachments/assets/a581c855-2db9-47b0-a5fb-d48b67b293ec)

Here's an indepth table with what each step does.

| Import Step | Description |
|--|--|
| Check Unity Version | Ensures the unity editor version matches the game's unity version. This exists mostly for debugging and utilizing thunderkit with a mismatch unity version is unsuported. |
| Disable Assembly Updater | Disables the assembly updater, this is to avoid unity updating managed libraries that should not be modified which can cause issues. |
| PostProcessing Unity Package Installer | Installs the Postprocessing unity package, useful if youre looking for adding custom post processing |
| Assembly Publicizer | Publicizes the listed assembly names, making all the fields public and removing their read only identifiers. fields that become public via the publicizer are marked as not serializable, keeping the inspector look intact. |
| MMHook Generator | Generates MMHook assemblies based off the listed assembly names, allowing you to easily hook game code to inject your custom code. |
| Import Assemblies | Imports the game's assemblies, this step is required to be enabled for the publicizer and mmhook generators to work properly. |
| Import Project Settings | Import the game's project settings, it is recommended to import all project settings |
| Set Deferred Shading | Sets the rendering path for all Graphics Tiers to Deferred |
| Create Game Package | Creates a new ``package.json`` file for the game's assemblies, leaving this disabled will cause the game's assemblies to not be detected by unity |
| Import Addressable Catalog | Imports the Addressable Catalog, also enables the Addressable Browser |
| Configure Addressable Graphics Settings | Assigns teh Risk of Rain 2 Deferred Shading and DeferredReflectionCustom shaders in the AddressableGraphicsSettings and by proxy in the project's Graphic Settings |
| Ensure RoR2 Thunderstore Source | Ensures the creation of a Thunderstore Source for Risk of Rain 2, allowing you to install external mods for cross compatibility |
| Install Bepinex | Installs BepInEx to your project, alongside its dependencies. |
| R2API Submodule Installer | Allows you to select specific R2API Submodules to install to the project. |
| Install RoR2MultiplayerHLAPI | Installs the RoR2MultiplayerHLAPI fork of the Unity HLAPI. allowing for networking procedures for your mod |
| Install RoR2 Editor Kit | Installs RoR2EditorKit, which contains a plethora of utilities and inspectors for developing mods. |
| Get Bitness | Obtains the "Bitness" of the game. (Wether the game runs on 32 or 64 bits) |
| Beep | Unity will play a system beep sound to inform the import is complete |
| Prompt Restart | Once the import process is complete, unity will prompt for a project restart for stability reasons |


5. Once your import configuration is configured, go to the ThunderKit Settings option

![image](https://github.com/user-attachments/assets/de18978e-20a1-4488-827c-680cf8a12d77)

6. Hit Browse and select the game's executable. Once the executable is selected, click "Import" to begin the import process.

7. You may be prompted to disbale the assembly updater, click "Restart Project"

![image](https://github.com/user-attachments/assets/aa4d64b1-b82a-42c8-8547-54e231c47e58)

8. The import has begun, please keep in mind that importing the game takes time, patience is key You may see CMD windows poping up, this is _**Normal**_ and not a cause for concern

9. Once the import process is complete, you may be prompted to restart the project for stability reasons, it is recommended to restart it.

![image](https://github.com/user-attachments/assets/fe935e4e-81ab-492f-a07d-249c811436b0)

10. If you integrated wwise, you may notice some errors related to duplicate assemblies. You'll need to manually delete the following assemblies from the project:

    Packages/riskofrain2/plugins/AkSoundEngine.dll

### Step 6: Compossable Object Crashcourse

Before we start creating a simple mod, it is EXTREMELY important to understand the concept of Compossable Objects within ThunderKit.

Creating mods in ThunderKit utilizes it's setups for managing and handling mod identity and deployment, this is done via whats known as the "Manifest" and "Pipelines", which are "Compossable Objects"

A Compossable Object, in this case, is a custom ScriptableObject that contains internal scriptable objects that "Compose" the main element. its used to contain large amount of compossable and modifiable data. Each main Compossable Object has a Compossable Element.

| Compossable Object | Compossable Element |
|--|--|
| Pipeline | Pipeline Job |
| Manifest | Manifest Datum |
| Path Reference | Path Component |

1. Manifest and Manifest Datum

As explained before, a Manifest's Compossable Element is a "Manifest Datum". A manifest datum always has a "Staging Paths" field. This field is utilized by ThunderKit to know where to "Stage" the manifest's data during the building process. It is recommended to use the default staging paths unless you want to create a more sophisticated build setup.

Below is a table that has all of the default datum types and a description of them

| Datum Type | Description |
|--|--|
| Manifest Identity | This is the main datum all manifests have, and it describes the ``manifest.json`` of your mod which will be used during the upload to thunderstore. Contains metadata such as the Author, the name of the mod, it's description, the version, and a list of Manifest Depencencies. Which ThunderKit will use to construct a valid ``manifest.json`` for Thunderstore. |
| Assembly Definitions | A Datum that represents assemblies of your mod, you can specify multiple assemblies for your mod, for more information on Assembly Definitions, check the manual entry [here](https://docs.unity3d.com/2021.3/Documentation/Manual/ScriptCompilationAssemblyDefinitionFiles.html) |
| Asset Bundle Definitions | A Datum that represents ASsetBundles for your mod, you can specify multiple assetbundles for your mod, and you can specify specific assets for your mod's bundle. If you specify a folder, all of the folder's contents and subfolders will be added to the specified Assetbundle. |
| Files | A generic datum used to contain extra files for your mod, these files can be staged in specific folders, and as such can be used to copy over a myriad of files such as language files, manifest files such as the mod's icon, readme and changelog, etc. |
| ThunderStore Data | A Datum that's used to hold your mod's github url. |
| Unity Packages | A Datum thats used to relate specific unity asset packages with your mod, this is rarely used in a modding context. |

2. Pipeline and Pipeline Job

A Pipeline compossable element is composed by PipelineJobs. A Pipeline in this case is a Scriptable Object thats used to execute complex code routines that act upon data held by a Manifest. You can think of the Manifest as pure data held that contains the metadata of a mod, and a Pipeline as code pieces that execute specific actions related to the manifest's data.

The pipeline by default has a single field, called the "Manifest" this is the main manifest that the pipeline will process. You can also set a Quick access to the pipeline to have access to it from the top toolbar.

Pipeline job execution is *Sequential*, meaning that the compossable element at index 0 will be the first job to run, then the index 1 will be next, so on and so forth.

![image](https://github.com/user-attachments/assets/9b58207d-65a9-49aa-b045-2ca43c62e846)

Pipeline job execution is *Sequential*, meaning that the compossable element at index 0 will be the first job to run, then the index 1 will be next, so on and so forth.

Below is a table that has all of the default Pipeline Job types and a description of them

| Pipeline Job Type | Description |
|--|--|
| Apply Patch | Applies a bsDiff patch file to an assembly to patch its managed code, this is rarely used in a modding context. |
| Create Patch | Creates a bsDiff patch based off an old assembly and a modified one, this is rarely used in a modding context. |
| Copy | A pipeline job thats used to copy over files and directories. |
| Delete | A pipeline job thats used to delete files and directories. |
| Execute Pipeline | A Pipeline job that executes another pipeline and awaits for its completion, this can be used to create complex deployment pipelines. |
| Execute Process | A Pipeline job that executes a executable as a process and awaits for its completion, this is rarely used in a modding context. |
| Stage Assemblies | A Pipeline job that stages the assemblies associated with the manifest. This staging process builds the assembly. You can specify wether to mark it as a release build or a debug build, allowing you to utilize conditional compilation |
| Stage AssetBundles | A pipeline job that stages the assetbundles associated with the manifest. This staging process builds the assetbundles. |
| Stage Dependencies | A Pipeline job that stages the dependencies associated with the manifest. you can specify a black list |
| Stage Manifest Files | A Pipeline job that stages any manifest datum of type "Files". this can be used to stage things like langauge files, changelogs, readmes, icons, etc. |
| Stage Thunderstore Manifest | A Pipeline job that stages your manifest's ``manifest.json`` file, allowing you to easily upload your mod to thunderstore.  |
| Stage Unity Packages | A Pipeline job that stages your manifest's Unity Asset Packages, this is rarely used in a modding context. |
| Zip | A Pipeline job that Zips a specific source folder and creates a zip file, this can be used to easily generate a .zip file ready for upload to thunderstore. |

3. Path Reference and Path Components

A PathReference compossable element is composed by PathComponents, these are used to create and represent complex path representations that are created when requested. You may notice during the previous two sections that some compossable elements have strings that are surrounded by ``<>``. In a ThunderKit context, a path stirng that's surrounded by ``<>`` means that the pipeline or manifest is requesting for a PathReference that has the name within the ``<>``. For example, You may notice that the Stage AssetBundles pipeline job has a BundleArtifactPath with it's default value set to ``<AssetBundleStaging>``.

![image](https://github.com/user-attachments/assets/2decfe41-462f-456f-ba1f-491cec5b86f2)

This in turn, represents this specific Path Reference, which will be used to construct the full path during the pipeline job where the asset bundles will be created.

![image](https://github.com/user-attachments/assets/486fbd7e-d7e5-42d2-8095-7faa0c18f10d)

The creation of a Path from a Path Reference and its components is *Sequential*, meaning that the Path Component at index 0 will be the root path, then the next component will be combined with the previous component, so on and so forth.

Below is a table that has all of the default PathComponent types and a description of them
| Path Component Type | Description |
|--|--|
| Asset Reference | Marks that an asset's path should be part of the generated path. |
| Constant | A Constant value, this can be either a complete path or part of a path, such as a folder name |
| Find Directory | Finds a specified directory with a specified pattern on ``path``, and uses that directory as part of the path generation. |
| Find File | Finds a specified file with a specified pattern on ``path``, and uses that file as a part of the path generation. |
| Game Executable | Returns the path to the game's executable, as dictated on the ThunderKit Settings. |
| Game Path | Returns the path to the game's main directory, as dictated on the ThunderKit Settings. |
| Manifest Name | Returns the name of the manifest, this is usually used within pipelines |
| Manifest Version | Returns the version string of the manfiest, this is usually used within pipelines |
| Output Reference | Returns the computed output of a specified Path Reference. |
| Path Component | Returns an empty string, might be unintented |
| Registry Lookup | Returns a specific key's value from the system's registry |
| Resolver | Given a specified ``<PathReference>``, return its resolved output |
| ThunderKit Root | Returns the main ThunderKit root folder, which is located in ``Project Root/ThunderKit`` |
| Working Directory | Returns the main project's root folder. |

### Step 7: My first ThunderKit mod

Now that we have a very basic grasp of compossable objects, we will begin creating a very simple mod that adds an ItemDef which increases all out stat multipliers by .1

This part of the project will utilize the unofficial editor extension "RoR2EditorKit", as it contains a wizard that will create basically all of the boilerplate for us. If you did not install RoR2EditorKit or you left it's import step disabled, you can manually install it using it's [github repository](https://github.com/risk-of-thunder/RoR2EditorKit/tree/main) the same way you installed ThunderKit and the Importer Extensions.

1. Go to ``Tools/RoR2EditorKit/Wizards/Mod``, click it and the Mod creator wizard will open.

![image](https://github.com/user-attachments/assets/706e6ac2-422a-4f23-9691-0eee5b49dd97)

2. The window will open, in this window you can fill out your mod's metadata. You can also tick on the "Add R2API Assemblies" option to add all R2API assemblies to your mod. You can also add more Precompiled Assemblies by adding them using the Precompiled Assembly References list.

![image](https://github.com/user-attachments/assets/7fd4383e-41bc-42fe-891b-fa16a23db15d)

3. Once configured, hit Run Wizard. once it completes, close the wiszard and go to your newly created folder where your mod resides. If your classes are missing, reimport the mod's folder by right clicking it and selecting "Reimport"

![image](https://github.com/user-attachments/assets/d053a27e-3bfc-4b46-b6b2-ef15c387199f)

You may appreciate a couple of new files in the folder.

| File/Folder Name | Description |
|--|--|
| Assets (folder) | A folder where your mod's assets are located, your mod's AssetBundle will be built from this folder. Keep in mind that as its a folder, any assets and sub-folders will be included to the final AssetBundle. |
| CHANGELOG.md | A MarkDown file that represents your mod's ChangeLog, this is optional in the ZIP file we'll upload to thunderstore eventually. |
| icon.png | Your mod's Icon, which will be used on thunderstore, make sure to update it because by default it'll just reuse RoR2EK's icon |
| <ModName>.asmdef | This is your main mod's assembly definition, it's what tells unity to create a new CSProj for modifying and creating your assembly. Due to the wizard, it already has defined all the necesary assembly references. |
| <ModName>ContentProvider.cs | This is your mod's IContentPackProvider class, this will be used to load your mod's content into the game (IE: Items, Bodies, Equipments, etc) |
| <ModName>Main.cs | This is your mod's Main class that inherits from BaseUnityPlugin, it contains a simple singleton pattern to get access to its instance alongside logging methods. Due to the wizard, it already has defined all the necesary BepInDependencies for your mod. |
| <ModName>Manifest.asset | This is your mod's Manifest asset configured to the bare minimum of datums, with all the required mod dependencies. It already defines your mod's assetbundle, your mod's assembly, files for the plugin and the Thunderstore URL|
| README.md | This is your mod's README file, general information about what your mod does should be here. |

4. To begin our item, we need to create both an ExpansionDef and an ItemDef, unlike the regular way of creating these using ``ScriptableObject.CreateInstance<T>()``, in ThunderKit we can create these ScriptableObjects as actual assets in our project. To do this, Right click the project window, and traverse to "Create", in there you can see further options, go to "RoR2" and you'll have access to all of RoR2's ScriptableObjects.

(Do not actually use raw strings for the tokens, you can find guides regarding language elsewhere.)

![image](https://github.com/user-attachments/assets/8ab041c3-1aa1-46b2-9810-927f284f898d)

5. This is the bare minimum of assets for our item to work, now comes loading our assets in code to create our ContentPack, and then give actual behaviours to our item. Open your ContentProvider class. Inside ``LoadStaticContentAsync`` our mod is loading our AssetBundle, after the ``while`` loop, we need to load both our ExpansionDef and ItemDef. And afterwards, add it to our ContentPack.

![image](https://github.com/user-attachments/assets/84ff6d25-f4c1-45ae-8b2c-95b397d4fd18)

6. Now that we're adding our content to our ContentPack, we need to program the behaviour, inside ``FinalizeAsync`` we can run code that will be executed once all ContentPacks have been initialized. increasing stats with our item is done via R2API's RecalculateStatsAPI

![image](https://github.com/user-attachments/assets/8c35e765-c47d-4192-9628-b50d7578f74c)

7. Our item is done, now we need to build our mod. For building, we will utilize a Pipeline. Right click the project setting, and go to ``ThunderKit/Pipeline``

![image](https://github.com/user-attachments/assets/2e2ec6c9-c59f-4390-b50b-4af6901bdeaf)

8. We now need to add our pipeline jobs to our pipeline, remember that jobs are run sequentially, this is what I Personally recommend. You will also need to create a new Path reference which will be used to tell thunderkit where to copy our built mod.

This is what our pipeline is doing:

    I. Build and Stage the selected manifest (ours)'s Assemblies, using a Release flag and staging debug databases for logging purposes.
    II. Build and Stage the selected manifest (ours)'s AssetBundles, utilizing no compression and building for Windows
    III. Stage the selected manifest (ours)'s Files. (README, CHANGELOG, icon, etc)
    IV. Stage the selected manifest (ours)'s manifest.json file
    V. Copy all of the selected manifest (ours)'s file located within "ManifestPluginStaging" to "DeploymentFolder"

![image](https://github.com/user-attachments/assets/bd2f48e7-5898-4c9c-9c06-bcf50cc082bd)

9. Deployment folder in this case refers to a PathReference, its where ThunderKit will deploy our mod. this is what i usually do:

Basically, this tells thunderkit to use the specified constant string (in this case, the path to my R2ModMan profile's plugins folder). And paste the files inside a folder that has our manifest's name.-

![image](https://github.com/user-attachments/assets/3952b5d1-01d1-4bca-aec3-d7757db9f4ef)

10. We are now ready to build our mod, select our pipeline and hit "Execute"

![image](https://github.com/user-attachments/assets/0a57d447-5412-4cc8-8174-f78a5d5be98a)

11. Our mod is built and has been staged into the plugins folder! download your dependencies and test it out!

![image](https://github.com/user-attachments/assets/60b622ce-bbab-41e2-bb31-e6d6ee293fad)
![image](https://github.com/user-attachments/assets/dffa328b-54a7-421d-9ea0-b082b6e93fc8)
![image](https://github.com/user-attachments/assets/b801d49e-860f-4b56-9d62-834be371a7d0)

### Other Tidbits

* If youre an experience modder, the assembly setup with AssemblyDefinitions do not support Embedding resources, this is because unity takes full control of your project's Solution and CSProj files, and as such, embedding is functionally impossible. Which is why stuff like AssetBundles are loaded externally.

* ThunderKit has relatively complete in-editor documentation, you can access it by using ``Tools/ThunderKit/Documentation``

![image](https://github.com/user-attachments/assets/c1788b6b-64ed-463d-9635-58ff86f070d1)

* Since we're working on the editor, we can easily create and setup custom components or ScriptableObjects, use this to your advantage. Keep in mind that unity will only recognize valid monobehaviours that have only 1 class inside the file and the file and class name share the same name.

![image](https://github.com/user-attachments/assets/4bd0035b-f91f-4471-b0dc-5cf6d922121f)
![image](https://github.com/user-attachments/assets/173f3e29-efe0-4237-9f82-508575a4ca2d)

* If you end up with the need to create a custom ScriptableObject, you can create a custom one in code easily, you can also apply the ``CreateAssetMenu`` attribute to get a convenient menu for creating new instances of your ScriptableObject

![image](https://github.com/user-attachments/assets/b128d114-0ee2-4d5f-8000-29c739d19030)
![image](https://github.com/user-attachments/assets/852353ed-6322-4d20-8d19-fefeea1a85aa)
![image](https://github.com/user-attachments/assets/92eeb417-910d-49ed-a312-6c218f54a77b)
# Custom Stages

This guide will cover the details of stage making and good practices to keep yourself organized. This guide will be assuming you already have a solid way to load a stage; if not, click [here](#nuketown) to learn how to download IDeath's Nuketown project to get a good reference on how everything works.

### Video Version
https://youtu.be/JX4eGSaGCHk

## Warning

Let it be known that it is **heavily** suggested to use Thunderkit to build your stages. Making a stage requires heavy amounts of visual feedback and not using it will cripple your workflow. If you aren't familiar with Thunderkit, refer to [this page](https://github.com/risk-of-thunder/R2Wiki/wiki/Mod-Creation_Thunderkit_Creating-Mods-with-Thunderkit). If you need more help, go to the Risk of Rain 2 Modding or Thunderkit Discord servers.

Another thing is that when building asset bundles using Thunderkit, each scene needs to be in its own assetbundle or else it will not build.

## What makes a stage a stage

Will the stage be a normal stage in a run like Titanic Plains or will it be a time stopped hidden realm like Gilded Coast? Will the stage have a pool of enemies and interactables? What point in my run will I be able to encounter this stage?

Your stage needs a list of established rules to follow by to streamline the stage's intended experience. Luckily, these rules can be declared (mostly) in the Scene's Hierarchy and ``SceneDef``.

### SceneDef
In the stage's ``SceneDef``, you can declare things like the tokens, required expansions, logbook entry fields, and music (though you may have to fill the ``MusicTrackDef`` fields at runtime if you want to reuse vanilla RoR2 music). This is very similar to how the ``ItemDef`` scriptable object is populated. One of the most important fields though is the ``Scene Type`` field. Each option for the dropdown goes as follows:

- ``Menu`` is something like the main menu or logbook.
- ``Stage`` is a normal stage in a run like Titanic Plains or Sky Meadow
- ``Intermission`` are abnormal stages that stop time like the Bazaar.
- ``Cutscene`` is a cutscene. Nothing more nothing less.

![](https://cdn.discordapp.com/attachments/829943783412072479/1017167147534913596/unknown.png)

Another important field is ``Base Scene Name Override``. Insert the internal name of the Unity Scene so the scene gets associated with the ``SceneDef``.

![](https://cdn.discordapp.com/attachments/829943783412072479/1017160370881826878/unknown.png)

### Scene Hierarchy
There are four main parts in the Scene Hierarchy that makes the stage work properly: the SceneInfo, Director (only if it has enemies spawning in it), GameManager, and World. 

![](https://cdn.discordapp.com/attachments/829943783412072479/1017172920960811098/unknown.png)

#### SceneInfo
The SceneInfo has two components: Scene Info (Script) and Classic Stage Info. The Scene Info (Script) holds NodeGraphs and MapNodeGroups- which will be covered later in the guide- while the Classic Stage Info holds Dccs and DccsPools.

![](https://cdn.discordapp.com/attachments/829943783412072479/1017174543204364298/unknown.png)

#### Director
The Director holds all the values to be given to the director while running a stage. It should hold a ``NetworkIdentity``, ``Director Core``, ``Scene Director``, and ``CombatDirector``. There can be more than one ``CombatDirector`` and most stages have two.  The Director is a pretty complex system so it won't be explained here. I (Jace) would suggest to reference other stages' directors when setting up your own director.
- **Important Sidenote:** you will need to populate ``Teleporter Spawn Card`` with the teleporter ``InteractableSpawnCard``. The easiest way to do this is to take [Nuketown's](#nuketown) ``Addressables Asset`` script and populate it as seen in this image below.

![](https://cdn.discordapp.com/attachments/829943783412072479/1017178274138443817/unknown.png)

#### GameManager
The GameManager makes your game run properly in this scene and it only contains one component: the Global Event Manager. If you want your game to yknow... function, add this. Please.

![](https://cdn.discordapp.com/attachments/829943783412072479/1017179118309220352/unknown.png)

#### World
The World doesn't contain any special components, but it contains your main geometry for the stage. Make sure that the World and everything in it is on the ``World`` Layer. If you cannot see the ``World`` Layer, under Import Configuration in the Thunderkit Settings tab, switch ``Included Settings`` to be ``Everything`` then reimport RoR2.
- Not every piece of tangible terrain needs to be under the World object in the hierarchy, it's just good practice to keep it under an umbrella. Just make sure said tangible terrain is still in the ``World`` Layer.
- **Important Sidenote:** If it so happens that you are testing your stage with Loader or Engi, you will notice that something isn't on the World layer because your hook or mines go directly through the terrain.

![](https://cdn.discordapp.com/attachments/829943783412072479/1017181673806377020/unknown.png)

## Putting the stage together.

This is the part where the stage is actually made. If you haven't already, have models/meshes already made for the basic layout of the stage. You can use Blender or install the ProBuilder 4.4.0 package for Unity. If you are installing ProBuilder, make sure to delete any ProBuilder dll files from your Risk of Rain 2 package you have imported. Putting the geometry together is fairly simple, but very time consuming, so come back to this guide when you have at least something.

- **Important Sidenote:** I am once again urging you to grab the ``Addressable Asset`` and ``Addressable Asset on Children`` scripts from the [Nuketown](#nuketown) project. 

-***This Page is still a WIP. I will finish it later -Jace***


## Nuketown

-   [Unity Version 2019.4.26](https://download.unity3d.com/download_unity/e0392c6b2363/Windows64EditorInstaller/UnitySetup64-2019.4.26f1.exe)

-   Create a new project with the 3D template

-   Add these lines in your `MyUnityProject/Packages/manifest.json` file

```json
"com.passivepicasso.thunderkit": "https://github.com/PassivePicasso/ThunderKit.git#1aeb51e18e41f65801f49f7ed290de5f54a90ebb",
"riskofthunder-ror2importextensions": "https://github.com/risk-of-thunder/RoR2ImportExtensions.git",
```

-   In the `ThunderKit Settings`:

    -   `Import Configuration`:

        -   `Assembly Identity Algorithm`: `Stabilized`

        -   `Import Project Settings`: `Everything`

    -   `ThunderKit Settings`:

        -   `Browse` to the game executable

        -   Click `Import`

-   If you get a message box telling you to make a backup, click `No Thanks`

![API Update Required Message Box](https://i.imgur.com/zROzKsY.png)

-   Close the Unity Editor

-   Put the content of the [Assets.zip](https://cdn.discordapp.com/attachments/575431803523956746/1006280734648057876/Assets.zip) inside the `MyUnityProject/Assets/` folder

-   Open the Unity Editor

-   Open the `NuketownStage/Scenes/NuketownScene.unity` scene

-   Click on the `GroundMapNodeGroup` GameObject, enable it with the `Unity Inspector` in the scene, right click for moving the camera around, but also for getting the preview of nodes and placing them around.

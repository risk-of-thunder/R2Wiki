# Stages

## ***!!! WARNING !!!***
Making stages is a difficult task. If you are new to modding please consider going  [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Getting-Started/First-Mod/) first. It is also recommended to have experience modding / working in Unity and [ThunderKit](#modding--editor-tools) as most of your time will be in Unity and Thunderkit.

There is also a bug in the latest version of ThunderKit:
![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/13570aa8-8540-44d3-a574-441dbbad8f0c)

To find the scripting define symbols you need to go to ``Edit`` > ``Project Settings`` > ``Player`` and append ``RISKOFRAIN2`` to your Scripting Define Symbols.

## Quick Terminology

- **Scene** - The internal scene asset / what the player is seeing when the scene is loaded. Includes stages, cutscenes, and menus. (``golemplains``, ``golemplains2``, ``arena``, ``artifactworld``, ``crystalworld``, etc.)
- **Stage** - A playable scene. All stages are scenes but not all scenes are stages. (``golemplains``, ``golemplains2``, ``arena``, ``artifactworld``, etc.)
- **Locale** - The name assigned to a stage or a group of stages. (Titanic Plains, Void Fields, Bulwark's Ambry, etc.)
- **Variant / "Alt"** - An alternate stage in a locale. (Titanic Plains having two different stages: ``golemplains`` and ``golemplains2``)
- **"Part of the Loop"** - Stages 1-5.

## Tools and References

### Modding / Editor tools

- **[Thunderkit](https://github.com/PassivePicasso/ThunderKit)** - This tutorial uses thunderkit. You can make stages without Thunderkit but it is very different and very limited. If you are unaware of how to use Thunderkit, refer to [this page](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Thunderkit/Getting-Started/) or [this video](https://www.youtube.com/watch?v=JI-erct5f4Y). If you need further help, ask questions in the Risk of Rain 2 Modding or Thunderkit Discord Servers.
- **[RoR2EditorKit](https://github.com/risk-of-thunder/RoR2EditorKit)** - A package that currently hosts the node placer and an abundance of other Unity editor utilites.
- **[R2API.Stages](https://thunderstore.io/package/RiskofThunder/R2API_Stages/)** - Not necessarily required but it is heavily urged to have your mod depend on this to make sure stages are implemented and balanced correctly, especially if you are making a variant.
- **[R2API.Addressables](https://thunderstore.io/package/RiskofThunder/R2API_Addressables/)** and **[R2API.Director](https://thunderstore.io/package/RiskofThunder/R2API_Director/)** - Not required but it hosts the Monster / Interactable Pool Addressable Scriptable objects explained later in the tutorial. Feel free to make your own means of making pools.
- **[LocationsOfPrecipitation](https://github.com/JaceDaDorito/jace-locationsofprecipitation/tree/main) (AKA "LoP")** - Required to populate certain fields (like the teleporter) or use certain prefabs (like newt statues). However, if you made an alternative to this that is also completely fine.
- Other usual Thunderkit project packages like **BepInEx** and **[RoR2MultiplayerHLAPI](https://thunderkit.thunderstore.io/package/RiskofThunder/RoR2MultiplayerHLAPI/)**.

**R2API** and **BepInEx** can be downloaded through the Thunderkit Package Manager. However, **RoR2EditorKit**, **LocationsOfPrecipitation**, and **RoR2MultiplayerHLAPI** should be downloaded via Unity's Package Manager instead. To do this, go to their respective github pages, copy the link under the green button that says ``<> Code``, go to Unity, click on ``Add package from git URL``, paste the link into the field, and then click ``Add``.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/63cdf57c-2947-4eb2-8a8a-3f28c44f2ef9)
![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/898220a4-d767-4ca4-8af7-17654d59a688)

You can monitor or change the package versions by going to  the ``manfiest.json`` in ``(Project Folder)/Packages/manifest.json``

[Waffle House](https://github.com/JaceDaDorito/WaffleHouse) is a good reference to see how the stage is constructed: whether it be how it manages assets or how it loads the stage.

### Runtime / Debug Tools

- [Debug Toolkit](!https://thunderstore.io/package/IHarbHD/DebugToolkit/) - A mod that gives access to a plethora of console commands for easier debugging.
- [Runtime Inspector](!https://thunderstore.io/package/Twiner/RuntimeInspector/) or [Unity Explorer](!https://thunderstore.io/package/sinai-dev/UnityExplorer/) - Mods that allow in-game inspection on anything loaded in your current scene.
- [Runtime Material Inspector](!https://thunderstore.io/package/Groove_Salad/RuntimeMaterialInspector/) - A mod that depends on Runtime Inspector that allows the editing of materials in game. Extremely important for stages that try to match RoR2's art style.
- [Steam's FPS counter](!https://www.theverge.com/23283921/steam-fps-counter-how-to) - A setting on Steam that allows you to view your FPS in game. Important for monitoring frame drops caused by your stage.

## Starting your Stage

Your stage needs a set amount of rules to be declared in order to work as intended. This section will be in Unity specifically, if you want to start with code you can go [here](#creating-mod-framework-and-loading-your-stage). This section goes over the ``SceneDef`` Scriptable Object so make sure to cross reference here occasionally if you decide to start with code.

RoR2EditorKit also has a Stage Wizard that sets up your stage for you. To use it, right click in your Project field, then go ``RoR2EditorKit`` > ``Wizards`` > ``Stage``. Make sure a token prefix and Manifest is present in the RoR2EditorKit settings in the Thunderkit Settings tab. After generating the stage, make sure to still read through these upcoming sections to fully grasp what was generated. Make sure to also double check that your stage is accurate to that of the tutorial; the wizard is missing a few things as it was made with less known knowledge about stage making. This will be fixed soon.

### SceneDef
A stage's ``SceneDef`` is an identifying Scriptable Object that holds a lot of the stage's basic behavior. In the ``SceneDef`` you can declare tokens, required expansions, logbook entry fields music, etc. This is similar to how the ``ItemDef`` scriptable object is populated. This also means you can create a ``SceneDef`` in code if desired. The ``SceneType`` field should be the first thing you figure out- all of which are listed below:

- ``Menu`` are UI based scenes. (main menu, logbook)
- ``Stage`` are stages that have time scaling and iterate on the stage counter. (Any stage in the loop like Titanic Plains, Void Locus)
- ``Intermission`` are stages that stop time scaling and do not iterate on the stage counter. (Guilded Coast, Bazaar)
- ``Timed Intermission`` are stages that are a mix of ``Stage`` and ``Intermission``. The stage has time scaling but does not iterate on the stage counter. Only applies to Void Fields. (Void Fields used to be a ``Stage`` before version ``1.2.4.4`` / CU 7.5)
- ``Cutscene`` is self explanitory. (Intro Cutscene, Moon Ending Cutscene)
- ``Invalid`` means it is no longer used. You probably shouldn't use this unless you are keeping a deprecated stage in the mod. (``moon``: the original moon stage)

``Stage``, ``Intermission``, and ``Timed Intermission`` are the only things going to be addressed in this tutorial.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/6507a6ed-d892-42e1-b10f-dc90440ffcdb)

If your stage is **completely new** and **not a variant**, the SceneDef name, Scene name, and ``Base Scene Name Override`` field should be the same string. A good practice is to have a prefix in your string to lower chances of mod conflicts. For example, I put ``waffle_`` in ``waffle_wafflehouse``.

If your stage is a **variant**, the SceneDef name and Scene name should be the same but different from the original stage; ``Base Scene Name Override`` should be the same as the original stage. For example, my Scorched Acres variant could be ``slip_wispgraveyard2`` but the ``Base Scene Name Override`` should be ``wispgraveyard``.

The reason why these rules exist is because the game identifies which scene to load by using the SceneDef and Scene names. The ``Base Scene Name Override`` field is important for the logbook and R2API.Stages's variant system- of which will be explained [here](#creating-mod-framework-and-loading-your-stage).

The Other elements in the SceneDef are as follows:

#### Classification
- ``Is Offline Scene`` is checked if the scene can only show for individual clients. Usually reserved for menus. Do not check this since you are making a playable stage.
- ``Stage Order``  indicates the stage's spot in the loop. This value is only used to order stages in the logbook by default, but if you are using R2API.Stages, it will also be used when registering the stage into the loop. Stages that aren't part of the loop have values outside the range of 1-5.
- ``Required Expansion`` is used to lock a stage to an expansion. If your stage is part of a larger mod and said mod has an expansion, you can put your mod's ExpansionDef to this field.
#### User-Facing Name
- ``Name Token`` is the token for the stage name. "Abyssal Depths" is correlated with ``dampcavesimple``'s name token: ``MAP_DAMPCAVE_TITLE``.
- ``Subtitle Token`` is the token for the stage subtitle. "Tectonic Relics" is correlated with ``dampcavesimple``'s subtitle token: ``MAP_DAMPCAVE_SUBTITLE``.
- ``Preview Texture`` is the texture of the stage in the logbook. Although directly not correlated, the same texture is used in the ``Portal Material``.
#### Bazaar
- ``Portal Material`` is the Material shown in the bazaar seer. This field needs to be populated if your stage is part of the loop.
- ``Portal Selection Message String`` is the token for the stage bazaar seer message. "You dream of fire..." is correlated with ``dampcavesimple``'s portal selection string: ``BAZAAR_SEER_DAMPCAVESIMPLE``.
#### Logbook
- ``Should Include In Logbook`` is checked if the stage should appear in the logbook. Usually turned off for stage variants. ``golemplains2`` doesn't have this checked because there would be two "Titanic Plains" logs in the logbook if it wasn't.
- ``Lore Token`` is the token for the stage lore. I am not putting the entire ``dampcavesimple`` log here but the token is ``MAP_DAMPCAVE_LORE``.
- ``Diorama Prefab`` is the prefab / model displayed in the logbook.
#### Music
- ``Main Track`` indicates what music to play during normal play.
- ``Boss Track`` indicates what music to play during the teleporter. If there is no teleporter, you don't need to populate this.
#### Behavior
- ``Suppress Player Entry`` is checked if the player doesn't spawn in the scene. Don't check this because you aren't making a cutscene.
- ``Suppress Npc Entry`` is checked if npcs (like drones) don't spawn into the scene. This is checked in ``arena`` due to the fog.
- ``Block Orbital Skills`` is checked if you  ~~hate Captain~~ want to turn off Captain's orbital skills in the stage. Usually used in Hidden Realms.
- ``Valid For Random Selection`` is checked if you allow the stage to be used in a random stage order. Mark this accordingly in case someone wants to do modded Prismatic Trials runs or another modded gamemode.
#### Destinations
- ``Destination Group`` is the group of destinations after leaving the stage. ``dampcavesimple`` has the Stage 5 Scene Group (``sgStage5``) marked as its destination group.
- ``Destinations`` is deprecated.
#### Portal Appearance
- ``Preferred Portal Prefab`` is used by the portal dialer for the artifact teleporter (I could be wrong about this). You don't need to worry about this if you are making a normal stage.

Some fields in your ``SceneDef`` cannot be filled in directly through Unity (such as Music Tracks, Portal Material, and Destinations). This will be covered in [this section](#creating-mod-framework-and-loading-your-stage).

### Scene
There are three main pieces in the Scene's hierarchy that makes the stage work properly: the SceneInfo, Director, and GameManager. The Scene's hierarchy will also contain the world, collision, decoration, etc.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/3c74c97e-ded8-49dc-8c72-bd938e973f81)

#### SceneInfo
The SceneInfo object has two components: ``Scene Info`` and ``Classic Stage Info``. The ``Scene Info`` holds NodeGraphs and MapNodeGroups- which will be covered [here](#introduction-to-nodes)- while the ``Classic Stage Info`` holds the ``Monster Dccs Pool``, ``Interactable Dccs Pool``, and Director Credits. All of these will be discussed further into the tutorial. The ``Interactable Categories`` and ``Monster Categories`` are deprecated.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/f24810f3-b5ae-4e7d-8470-3abe54787727)

#### Director
The Director is what keeps track of monster and ally spawns in the stage. This object should have a ``Network Identity``, ``Director Core``, and ``Scene Director``. Every stage- even stages like ``bazaar``- have these components. If you are planning to have a stage with no monster spawns or teleporter progression, you can leave it be.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/9fd7001f-fc78-478a-b812-adb7f5f5cda4)

However, if you want monster spawns and teleporter progression, you will need two of ``Combat Director`` and the teleporter spawn card. There are two ``Combat Directors`` because one is for faster, weaker waves, and the other is for slower, stronger waves. The only difference between the two are the ``Min Reroll Spawn Interval`` and ``Max Reroll Spawn Interval`` fields.

- The faster, weaker combat director should look like this:

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/14f1e9ed-3f05-4018-bcbd-4a5489e08bdd)

- The slower, stronger combat director should look like this:

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/73202008-cd00-44cd-af8d-4243afd73ba7)

To inject the teleporter spawn card, you can use LoP's ``Addressable Injector`` component to inject the spawn card into the ``Scene Director``'s ``Teleporter Spawn Card`` field. You can inject ``RoR2/Base/Teleporters/iscTeleporter.asset`` for a normal teleporter and ``RoR2/Base/Teleporters/iscLunarTeleporter.asset`` for the Primordial Teleporter on Sky Meadow. Even though there is no asset populated in the ``Teleporter Spawn Card`` field, they are linked and it will populate at runtime. This applies to anything involving ``Addressable Injector``.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/5197e60f-357f-475d-8126-5f922a170f44)

These aren't hard rules on how to set up your director, **especially if you are making a stage with a unique functionality**.

#### GameManager
The GameManager has one component: ``Global Event Manager``. As the name suggests, this component handles core events in the game like handling Character Deaths and Level Ups. If you do not have this in your scene, your stage will be unplayable.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/5c1d9e6c-32e6-49d3-a495-4696d6bf35e7)

#### World and Collision
The ``World`` layer is attached to most of the game's collision. A lot of the game's collision checks use this layer, so make sure all main geometry is under this layer. Small props with no collision or skybox elements do not need to fall under the ``World`` layer. If you cannot see the ``World`` Layer, under Import Configuration in the Thunderkit Settings tab, switch ``Included Settings`` to be ``Everything``, then reimport RoR2.

![unknown](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/15f96699-fdd1-47fe-a5c0-6192b26a3927)

To block out your stage layout, you can use Blender or any other modelling program of your choice. Please be mindful of the amount of vertices in each model as you do not want your stage to drop framerate.

All collision should have some form of ``Collider`` component. All collider types can be seen [here](!https://docs.unity3d.com/2019.4/Documentation/ScriptReference/Collider.html). If you are using a ``Mesh Collider``, make sure your mesh's vertices aren't condensed enough to mess with player collision. The big terrain mesh found on any stage may have a sizable amount of vertices, but it will never mess with collision since they are so spread out.

**Never use** ``Mesh Collider`` **for detailed, smaller models**. If your detailed model can be simplified to a basic shape like a box or sphere, use Unity's primitive collider types such as ``Box Collider`` or ``Sphere Collider``. The example below showcases a set piece in ``golemplains`` from the top down. Even though the model has indents and holes, it uses a ``Box Collider`` (indicated by the green lines) to keep it from ruining player movement.

![Illustration](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/fd7f29ba-006c-407d-ad12-1eb2a8d9b732)

If your detailed model is more irregular, you will have to create a simplified mesh that matches your model and use that in your ``Mesh Collider`` instead. For example, there is this bridge. It is irregular and it cannot be simplified to primitive shapes easily.

![Illustration2](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/da169d91-fe23-4602-bb36-cc18d17c18c8)

This is the collision that overlaps said bridge.

![Illustration3](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/908074c0-b1ee-471d-8798-8527d1d7511e)

There may be minor clipping or inaccuracies when a player walks over the bridge but that is fine, the bridge is traversable and players probably will not notice those minor mistakes.

### Spawnpoints

``Spawn Point`` is a component that dictates player spawn points. However, the spawn points you place in the scene are only used if you load into the stage with a drop pod (the intro animation when you start your run; Acrid's wake up is also a "pod"). When you do a stage transition (say from stage 4 -> 5), the game will remove all the spawn points and generate new ones via the nodegraph (which will be talked about [here](#nodes)). The code below is found in ``RoR2.SceneDirector``. ``arena`` has it's own way to generate spawn points as well.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/3dd0fb7b-faad-4933-b120-3989e689cd2c)

Regardless if your stage is a stage 1, is in a different part of the loop, or a hidden realm, **place spawn points**. Loop stages usually have four sets of four spawn points, making sixteen spawn points total. The reason why we place spawn points even if the stage isn't a stage 1 is to account for unpredictability: other mods most notably. To place a spawn point, make an empty object and attach the ``Spawn Point`` component to it. You will know it works because there will be a wireframe of the commando model. This is also a cheap way to get good size reference without importing a model.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/478d4916-b592-4f5d-aaef-706f40aadd57)

Keep in mind if you load your stage without a nodegraph via ``next_stage`` or stage transition, you will spawn at the world origin.

## Creating Mod Framework and Loading Your Stage

Considering you already know how to [set up your BaseUnityPlugin and load assetbundles](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Thunderkit/Using-Scripts-with-Assetbundles/), creating the code for your stage is simple- that is until you make unique gimmicks for the stage such as the Abandoned Aqueduct door, Void Fields cells, or even a final boss.

You can either load your SceneDef from your assetbundle as an asset or create a SceneDef at runtime like so:

```
public static SceneDef ExampleModStageDef;
```
Setting up / completing your SceneDef will look something like this:
```
public void AddSceneDef(){

    //The pseudocode below is only for those who are making their SceneDef in code

    ExampleModStageDef = ScriptableObject.CreateInstance<SceneDef>();

    string identificationStr = "example_examplestage";

    ExampleModStageDef.cachedName = identification;
    ExampleModStageDef.baseSceneNameOverride = identification;
    ExampleModStageDef.sceneType = SceneType.Stage;
    ExampleModStageDef.isOfflineScene = false;
    ExampleModStageDef.stageOrder = 2; //Stage 2

    ExampleModStageDef.nameToken = "EXAMPLE_MAP_EXAMPLESTAGE_NAME";
    ExampleModStageDef.subtitleToken = "EXAMPLE_MAP_EXAMPLESTAGE_SUBTITLE";
    ExampleModStageDef.previewTexture = YourLoadedPreviewTexture;

    ExampleModStageDef.portalSelectionMessageString = "EXAMPLE_BAZAAR_SEER_EXAMPLESTAGE";

    ExampleModStageDef.shouldIncludeInLogbook = true;
    ExampleModStageDef.loreToken = "EXAMPLE_MAP_EXAMPLESTAGE_LORE";
    ExampleModStageDef.dioramaPrefab = YourLoadedDioramaPrefab;

    ExampleModStageDef.suppressPlayerEntry = false;
    ExampleModStageDef.suppressNpcEntry = false;
    ExampleModStageDef.blockOrbitalSkills = false;
    ExampleModStageDef.validForRandomSelection = true;

    //The pseudocode below is applicable no matter where your SceneDef is created.
    
    //This is "The Raindrop that Fell to the Sky"
    ExampleModStageDef.mainTrack = Addressables.LoadAssetAsync<MusicTrackDef>("RoR2/Base/Common/muSong13.asset").WaitForCompletion();

    //This is "Thermodynamic Equilibrium"
    ExampleModStageDef.bossTrack = Addressables.LoadAssetAsync<MusicTrackDef>("RoR2/Base/Common/muSong05.asset").WaitForCompletion();

    ExampleModStageDef.portalMaterial = StageRegistration.MakeBazaarSeerMaterial(YourLoadedPreviewTexture.texture);

    StageRegistration.AddSceneDef(ExampleModStageDef, ExampleModPluginInfo)
    StageRegistration.RegisterSceneDefToLoop(ExampleModStageDef);
}
```
This pseudo-code showcases some of R2API's stage utilities such as ``MakeBazaarSeerMaterial``, ``AddSceneDef``, and ``RegisterSceneDefToLoop``.
- ``MakeBazaarMaterial`` returns a usable bazaar seer material using the texture passed into it.
- ``AddSceneDef`` adds your SceneDef to your ContentPack.
- ``RegisterSceneDefToLoop`` automatically adds your stage to the loop depending on your stage order. If your stage order is set to ``2`` like the example above, it will add your ``SceneDef`` to the appropriate ``SceneCollection`` (``sgStage2``) and set your ``Destinations Group`` to the next ``SceneCollection`` (``sgStage3``). If you are registering a stage 5, the ``Destinations Group`` will be set to ``sgStage1``.

R2API.Stage's true power comes from its weight rebalancing system. If you register a stage with a ``Base Scene Name`` of ``wispgraveyard``, it will automatically be classified as a variant of "Scorched Acres" and rebalance the weights of all ``wispgraveyard`` variants to add up to 1. If there are two variants total, each variant will have a weight of 0.5; if there are three variants total, each variant will have a weight of 0.3334. This also applies to modded stages such as "Fogbound Lagoon" (``FBLScene``). This is to allow smooth implementation of stage variants without worry of modded variants. It is heavily encouraged to make your mod dependent on R2API.Stages if you are creating a variant.

Currently this system only applies to stages in the loop. At some point there will be a system to implement variants of stages such as ``moon2``, ``arena``, ``goldeshores``, etc. To see all internal stage names please look [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Developer-Reference/Scene-Names/).

To assist in building the mod, LoP comes with standard Manifests, Pipelines, and PathReferences. Copy ``Manifest`` from ``LocationsOfPrecipitation/Editor/Manifest`` and fill it in appropriately. Every field can be explained under Thunderkit's documentation window under ``Tools`` > ``Thunderkit`` > ``Documentation``. Make sure your Scene asset and the rest of your mod's assets are in different bundles.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/ea495029-16c5-49f2-813c-0493a6df30df)

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/2830f7d6-827e-447d-a9a2-eb34b64ae96d)

![Illustration](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/6d1d0e58-3d31-4b1c-babe-b47cfcd4a707)

Copy ``ContributorBuild`` from ``LocationsOfPrecipitation/Editor/Pipelines`` and create a new ``PathReference`` by doing right click > ``Thunderkit`` > ``PathReference``. Attach your manifest to your pipeline's ``Manifest`` field and your pipeline's ``Manifests`` field under the ``Copy`` job. The ``Destination`` field will be the name of your Path Reference in ``<>``. Both should look something like this:

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/b929aff2-7934-4b0c-8034-8f0fbcad0419)

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/807c0d5b-5fbe-4c04-b10f-7600c33389f1)

Afterwards you should be able to hit Execute and it will build the mod. If you have trouble building your mod feel free to consult the thunderkit or map-creation channels in the RoR2Modding Discord server.

By now your stage should be loadable- buggy but loadable. You should be able to execute ``next_stage [Name of SceneDef / Scene]`` and (if applicable) load into the stage at the appropriate loop placement. Make sure to use ``noclip`` as it is likely you will fall out of bounds.

## Introduction to Nodes

Nodes and pathfinding are both important aspects to all stages. Even for stages without combat like the Bazaar, a nodegraph is still present, albeit a small one. Nodes are the backbone for player spawning, monster spawning, interactable placement, teleporter placement, pathfinding, gates, etc.

There are three types of nodes: Ground, Air, and Rail. This tutorial will only be using Ground and Air nodes because Rail nodes have no presence in the game. Air and Ground nodes both dictate the spawning and navigation of aerial and ground bodies respectively. Ground nodes also dictate interactable and teleporter placement. Interactables placed manually into the scene such as Newt Statues, Preon Chest in Ralleypoint, and Froggy in Commencement are exempt from this rule and will be detailed later.

To start placing nodes, you will need a node placer / painter. You can either use the one in RoR2EditorKit or make your own. The one in RoR2EditorKit will allow you to place nodes into your scene with set keybinds and raycasts: ground nodes will raycast directly to the ground and air nodes will raycast a set distance above the ground. The raycast will only work when its aimed at collision on the ``World`` layer. To use the node placer, create two empty game objects in the scene hierarchy and attach the ``Map Node Group`` component to both of them (if you are using the Stage Wizard, these should be generated already). One object will hold our Air Nodes and the other will hold our Ground Nodes. In each of their ``Map Node Group`` components, set their ``Graph Type`` to the correct node type: Air for the Air Nodes and Ground for the Ground Nodes.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/73520c02-b86c-4af4-9273-1eaca00a4f99)

Next, you want to create two ``NodeGraph`` assets. Right click in the Project window and go to ``Create`` > ``RoR2`` > ``NodeGraph``. The purpose of the Node Graphs is to hold any baked node data once you have finalized your node placement and properties. One Node Graph will be for Air Nodes, the other will be for Ground Nodes. Attach each to the ``Node Graph`` field in each respective ``MapNodeGroup`` component. The last thing you need to do is attach your Node Groups and Node Graphs to your ``SceneInfo``. Now you are ready to start placing nodes.

While having one of your Node Groups selected, you can hover over your terrain and use the displayed keybinds to place or remove nodes. By default, B places nodes at your mouse's raycasted position, M removes the nearest map node, and N places a node at your camera (the last feature is currently unavailable but it will come back). You can enable ``Use Painter`` on the Node Group to enable a painter that should make the process a bit quicker. The ``Parent Game Object`` field allows you to place nodes under another Game Object automatically; this allows you to maintain some sense of organization as there will be a lot of nodes in your map when you are finished.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/e13cb300-3da2-4cb5-a539-42a4223fd42a)

As you place your nodes, you will notice the green links between them. This indicates that they will have a link between them when they are baked: a core mechanism that powers pathfinding. Make sure to polish your node placements so they are properly spaced out and cover the land you want to be playable.

 **<p style='color:red'>Be wary of the ``Clear Nodes`` and ``Remove Node Excess`` buttons, both of these delete nodes.</p>** The ``Clear Nodes`` button removes all nodes under your Node Group; the ``Remove Node Excess`` button attempts to remove nodes that are overlapping each other too much.

Each node has properties that allow different behaviors: ``Forbidden Hulls``, ``Flags``, and ``Gates``. Gates will be covered in its own section as it opens up for more creative use of nodes. The ``Forbidden Hulls`` defines what size of monsters are allowed to spawn on that node: ``Human`` (player sized monsters such as Lemurians and Wisps), ``Golem`` (bigger enemies from Golems to Titans), and ``Beetle Queen`` (huge enemies such as Beetle Queen and Grandparent). The ``Flags`` have miscellaneous behaviors such as ``Teleporter OK``, ``No Ceiling``, ``No Character Spawn``, ``No Chest Spawn``, and ``No Shrine Spawn``. The Hulls and Flags can be automatically generated via the ``Update No Ceiling Masks``, ``Update Teleporter Masks``, and ``Update Hull Masks`` buttons. You can also manually choose the flags and hulls you want via the Node Group or on the placed node. This opens up the ability for further polish as you can adjust teleporter spots or forbid chests and shrines from spawning on a jump pad- of which will be covered later.

Once you are confident with your node placements, it is time to bake your node graph. You can do this by clicking on the ``Bake Node Graph`` button on your Node Group. You can tell it baked when there are pink lines in between all of your nodes.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/6431825e-a9bb-4956-9d53-d15bd5a89d7b)

When rebuilding your mod and loading your stage again, you should spawn on your terrain and there should be a teleporter (if you intended there to be one).

## Addressable Prefabs and Newt Shrines

Similarly to injecting addressable assets into fields such as what was done with the Teleporter interactable card, you can instantiate RoR2 prefabs into your scene. To do this, you attach a GameObject with the ``Instantiate Addressable Prefab`` component from LoP. You can then input the addressable key into the ``Address`` field and mess with the options the component comes with:

- ``Set Position And Rotation To Zero`` makes the prefab position set to the **World origin** (0,0,0) and resets any rotations.
- ``Use Local Position and Rotation`` sets the prefab to that of its parent GameObject. Set the values of the parent to set the values of the prefab. Checking this value overrides ``Set Position And Rotation To Zero``
- ``Reset in Editor`` allows instantiation of the prefab in the editor. The prefab does **NOT** save in the scene so the script is still needed to instantiate the prefab at runtime. Report any GUID errors on your scene as that is a direct effect of trying to save an addressable prefab to the scene.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/739d395b-1c78-45a1-9fc1-22fa03b867ef)

Instantiating Newt Shrine uses the same process as instantiating any other Addressable Prefab. To instantiate a Newt Shrine, input this address:

```RoR2/Base/NewtStatue/NewtStatue.prefab```

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/69429abc-0097-4e09-8646-cfcbde97a8ae)

This script does have limitations: you cannot do any editing to the prefab instantiated. This includes jump pads. This is an issue because you need to edit the trajectory of the jump pad for them to be usable. LoP comes with another component to instantiate RoR2's jump pads- which will be covered in the next section.

## Node Links and Jump Pads

A lot of stages have Jump Pads or "Geysers" to help tie together a stage's traversal options. All of the prefabs shown below are examples of jump pads you may have seen scattered throughout the game.

![Geysers](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/117772c2-680c-4d20-b975-a5a265c6945a)

When it comes to using jump pads you have two options: making them from scratch or reusing the ones already in game. If you want to make unique looking jump pads, it is recommended to go with the former.

To create your own jump pad you will need four parts: the holder, Jump Volume, target, and visuals. The holder, which is called "Geyser" in this case, is the parent object holding the entire jump pad. It will be on the ``Pickups`` layer and have the ``Occupy Nearby Nodes`` component.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/00418df1-d4ea-4504-ae6c-4865b6f02767)

The Jump Volume will be parented under "Geyser" and have the ``Jump Volume`` component and any type of ``Collider``. The ``Collider`` needs to have ``Is Trigger`` bool enabled. The Jump Volume is also under the ``Pickups`` layer.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/65bc8898-e749-4899-a2d4-d9deae051917)

The ``Jump Volume`` component is also where you will be operating the jump pad's launch vector. You could operate this manually by editing the ``Jump Velocity`` and the ``Time``, however, RoR2EditorKit has an inspector utility to help autocalculate it for you by using a target. The target is an empty GameObject under the Jump Volume that is only used for its position. Just put the target into the ``Target Elevation Transform`` field in the ``Jump Volume`` then position the target where you want it to be. After setting the ``Time`` to any value above 0, check ``Autocalculate Jump Velocity`` and it should draw the trajectory of the jump pad towards your target. It can also be helpful putting temporary visuals where your target is located as you are prototyping your stage. Do keep in mind that the math to calculate the trajectory does not account for opposing forces such as shifts in gravity (Fogbound Lagoon's water) or knockback.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/be33d94e-775d-4d0f-a60f-3bb07992b75a)

Visuals are important to help communicate where the jump pad is located. In this case, a normal capsule mesh is used. However, it is advised to make your own unique visuals to enforce distinction from the rest of the environment.

LoP also has a util to help speed up the creation of custom jump pads. When right clicking on the scene hierarchy, there is a new menu item called ``Risk of Rain 2``. When expanding this menu item, there is ``Geyser``. This will create a "primitive" jump pad with all the parts detailed above.

There are more parts to making a jump pad beyond these details; it is suggested to search "Geyser" in Thunderkit's Addressable Browser and inspect any of the jump pads listed to study how they are constructed.

If you want to reuse the jump pads in game, LoP has the ``Instantiate Geyser Prefab`` component to allow you to instantiate any of the jump pads shown above. The Geyser type ``Fan`` has special attributes which will be covered [here](#gates-and-toggle-groups) as it is intertwined with gates and toggle groups. Do note that LoP does support the unused ``moon`` jump pads but not the newer ``moon2`` jump pads. The ``moon2`` jump pads have a unique condition to be activated hence why LoP doesn't support it- at least at the moment. When instantiating this component, it will also instantiate a ``Jump Volume``. This Jump Volume's values will be copied over to the pad's Jump Volume at runtime. You can then edit this Jump Volume like normal. Beyond the normal ``Instantiate Addressable Prefab`` options, ``Instantiate Geyser Prefab`` has ``Conserve Sound String`` if you want to keep the sound effect of being launched on the original jump pad.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/d012b7ad-7d9a-4412-96f9-32539e5026ae)

With both approaches to making jump pads there is an apparent issue: enemies do not know this jump pad exists. You may notice that grounded enemies will use jump pads to get to you as fast as possible. If enemies were to be spawning in your stage right now, they would not do this. To fix this, you need to make use of the ``Map Node Link`` component. This component allows you to manually link two nodes together no matter how far apart they are. Place a **ground** node on both the jump pad and the target. View the node you placed on the jump pad and attach a ``Map Node Link``. Then, attatch the target node to the ``Other`` field. There should now be a yellow line connecting the two nodes together. ``Min Jump Height`` should also be 0. Make sure the node on the jump pad has these flags:
- Teleporter OK is unchecked
- No Chest Spawn
- No Shrine Spawn
- No Character Spawn

Ignoring the ``Gate Name`` fields for now, it should look something like this:

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/7cd466f0-58fa-4f90-b65e-82bb577db98f)

Once you rebake your node graph, enemies should be able to interact with these jump pads intentionally.

![risk-of-rain2-risk-of-rain](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/d505297f-5749-4e27-82a0-c1e6fa1677fa)

## Map Zones

You now need to make sure players cannot get soft-locked and enemies die out of bounds. To do this, you will need to make use of the ``Map Zone`` component. To use this component, create an empty GameObject and put it on the ``CollideWithCharacterHullOnly`` layer. Give it any Collider but Mesh Collider and the ``Map Zone`` component. Turn on ``Is Trigger`` on the Collider. Now you need to configure the ``Map Zone`` component to your liking:

- **Trigger Type**: Trigger Exit will trigger the zone type if the CharacterBody leaves the Collider. Trigger Enter will trigger if the CharacterBody enters the Collider.
- **Zone Type**: Out of Bounds will teleport players and kill monsters. KickOutPlayers just teleports players.
- **Explicit Destination**: An explicit spawn point players get teleported back to.
- **Explicit Spawn Effect Prefab**: Vfx when players get teleported.
- **Destination Ideal Radius**: The ideal radius of positions the player gets teleported to. In stages in the loop this is normally 0.

The last thing you need to do is change the size of your collider to fit the space you want. There can be multiple colliders with the ``Map Zone`` component.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/dcb10f8c-5186-47af-a6ea-3ee743b45109)

## Gates and Toggle Groups

You may have noticed that a lot of the stages in RoR2 has an RNG aspect to them: the Distant Roost door on both alts sometimes being closed, the ``dampcavesimple`` cave sometimes being closed off, and the newt statues' positions being swapped around. These are all a result of the ``Scene Object Toggle Group`` component. This component allows you to enable and disable certain objects at random when you enter the scene. To use this component all you need to do is create an empty object (usually under the SceneInfo for consistency) and add the ``Scene Object Toggle Group`` component to it.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/de7dcf6a-84a6-41cb-b7a0-6217df12c17c)

Once you have the component, you can start adding the objects you want to be randomized. For example, element 1 in the image below holds the newt shrine randomization. It holds both newt shrines and the range of shrines that can be enabled at once. ``Min Enabled`` = 0 means that there can be no newt shrines at minimum. ``Max Enabled`` = 2 means that both newt shrines can be enabled at maximum. If you only wanted one shrine to be enabled at a time, you set ``Min Enabled`` to 1 and ``Max Enabled`` to 1.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/25eb1a65-fbbb-4ed7-b1f0-6a8da6da7b66)

However, when you apply this approach to the level geometry and jump pads, there is an issue of having extraneous or missing nodes. This is an issue because of two reasons:

- **Extraneous Gameplay Space** - When interactables and monsters can spawn and function in inaccessible or unconventional spaces. Imagine the ``blackbeach`` door being closed but there are still chests or even a teleporter behind it. This is never good as it can waste interactable and monster credits at best and completely kill the run at worst.
- **Invalid Gameplay Space** - When interactables and monsters cannot spawn or function in intended gameplay spaces. Imagine if the garunteed newt / artifact area in ``wispgraveyard`` was supposed to be an intended combat space. There is no loot up there and if monsters somehow got up there, they would freeze up and not be able to path find. **However**, since it isn't intended combat space in our universe, it is fine for monsters and interactables to not spawn and function up there. This concept also includes not properly setting up your jump pad's node links detailed [here](#node-links-and-jump-pads).

This is where gates come in. Gates allow you group together nodes and enable and disable them under any condition. First, you need to label your nodes under a gate. To do this you simply fill in the ``Gate Name`` field in your Node Group and place nodes.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/b2d283dc-abb1-464a-ad9c-1670b4740a0e)

 If you want to edit the ``Gate Name`` of already existing nodes, you can shift click nodes you want to edit and fill in ``Gate Name``.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/edfcc6c8-0953-4dab-9eaf-9ec648916f26)

To toggle certain gates using the ``Scene Object Toggle Group``, you would need another component called ``Gate State Setter``. What this component does is diable or enable certain gates depending on the activity of the GameObject it is attached to. In the image below, if the GameObject named "GateToggler" were to be enabled, ``Gate1`` will enable and ``Gate2`` will disable.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/60d62a31-ea70-4a9d-a11d-3f2fc349c471)

A "GateTogglerInverse" was also made with the opposite properties: when "GateTogglerInverse" is enabled, ``Gate1`` will disable and ``Gate2`` will enable.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/e7cc41d7-c46a-415e-a799-146ffa9b22d5)

Both of these objects can be used in the ``Scene Object Toggle Group`` to effectively make a switch where one gate or the other is enabled at a time.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/ebc7ab84-3f78-4eef-9145-baecfea1c377)

However, there are more use cases to gates beyond just initial stage RNG. As mentioned in the [Node Links and Jump Pads](#node-links-and-jump-pads) section, the ``Fan`` jump pad has unique properties due to it being purchasable. When ``Gate Toggle On Purchase`` is enabled, it opens up the ``Gate To Enable When Purchased`` and ``Gate To Disable When Purchased`` fields. These work exactly like the ``Gate State Setter`` except now it enables and disables the given gates on purchase. In the example below, the fan enables the ``FanIsland`` gate on purchase.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/03d11a30-8558-4d97-bcd1-2d8450e790dd)

By using knowledge on setting up node links, you can now repeat the same process but the jump pad and target nodes are now under a gate. The reason why gates are important to the ``Fan`` jump pad is because you want enemies to recognize when the fan is operational: you do not want enemies to ignore the fan when it is enabled nor do you want them to clump near the fan thinking its enabled.

Gates can be enabled and disabled under any under condition- assuming you are willing to put in more code. This also means, yes, the Contact Light from Risk of Rain 1 is possible. Gates will allow you to extend the creativity of your stage and make it into something truly special. However, you do not need gates to make a good stage.

## Monsters, Interactables, and Unlocks

Every stage in the game has a unique selection of monsters and interactables. Each monster and interactable is represented as a card: ``CharacterSpawnCard`` (CSC) and ``InteractableSpawnCard`` (ISC) respectively. If you care about stage balance, the most important property in each card is the ``Director Credit Cost``; self-evidently, this is the value used to deduct director credits whenever this card is chosen. This value can be used as a factor to gauge what interactables and monsters are appropriate for your stage. Referencing other stage pools is also a good idea. Using both CSC and ISC, you can compile your monster and interactable pools in a ``DirectorCardCategorySelection`` (DCCS). The DCCS can hold both CSC and ISC but you will need to make a DCCS for each: one for monsters and one for interactables. You can create a DCCS by right clicking on the Project window > ``Create`` > ``RoR2`` > ``DCCS`` > ``DirectorCardCategorySelection``.

Due to how the DLC works for RoR2, every non-DLC stage has two DCCS for the monster and interactable pools. Both DCCS are named the same except the DLC monster pool has a ``DLC1`` suffix. For reference, here are the naming schemes of the ``skymeadow`` DCCS:

- ``dccsSkyMeadowInteractables`` - The non-DLC DCCS for interactables
- ``dccsSkyMeadowInteractablesDLC1`` - The DLC DCCS for interactables
- ``dccsSkyMeadowMonsters`` - The non-DLC DCCS for monsters
- ``dccsSkyMeadowMonstersDLC1`` - The DLC DCCS for monsters

After creating your desired amount of DCCS, you will need to feed them into a ``DccsPool`` (DP). The DP is responsible for choosing the appropriate DCCS depending on what expansions are enabled. Back to the ``skymeadow`` example, if DLC1 was disabled, ``dccsSkyMeadowInteractables`` would be chosen; if DLC1 was enabled, ``dccsSkyMeadowInteractablesDLC1`` would be chosen.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/591f452f-3601-4bfb-8273-5afded1193dc)

Other than the DCCS, the DP can also hold Family Events. The game usually has two seperate categories for family events: "Family" and "VoidInvasion". "Family" has all the standard family events while "VoidInvasion" has the Void themed family event specifically.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/4ae63001-85fd-4139-919b-a19fa2dedfbb)

Overall, the "Standard" category (the DCCS holder) should have a Category Weight of 0.98 while the Family Event categories should have a weight of 0.2.

You may notice that you don't have direct access to monster/interactable/family/expansion assets to feed into the DCCS or DP. You have two options: loading all the assets into the DCCS or DP at runtime with code or make use of R2API's scriptable objects, ``AddressableDirectorCardCategorySelection``(ADCCS) and ``AddressableDCCSPool``(ADP). If you are doing the former, you can load the desire assets and populate them into desired fields similarly to what is done with the SceneDef. If you are doing the latter, you will want to right click into the project window > ``Create`` > ``R2API`` > ``DirectorAPI`` > and both objects should be listed there. These scriptable objects act as a holder for any values inputted, then populate them into the target DCCS or DP at runtime. These scriptable objects have a unique property of being able to hold addressable keys; for example, if you wanted your stage's monster pool to have beetles, you can grab its CSC address and input it as follows:

You can then expand the amount of categories the DCCS has to put your cards in. In stages in the loop, the DCCS follow a pattern. The monster DCCS has these categories:

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/b7310e77-8512-4d8f-b13b-931bc4d0a7ca)

**A few disclaimers if you choose to use R2API's director scriptable objects:**

- The target will be completely overriden with whatever is in the ADCCS/ADP. The target should be completely empty.
- If you have custom monster or interactable cards, you can input them into the ADCCS. To do this you right click on the context menu next to the desired field (The small RoR2 icon) and click "Use Direct Reference".
- When you want to reference something off of your DCCS/DP at runtime, reference off of the target DCCS/DP not the ADCCS/ADP. The target will already be populated at runtime. This also applies to attaching your DP to the Classic Stage info detailed later on.

Now you are ready to populate your DCCS. To remain consistent with vanilla, it is encouraged to organize your DCCS in a particular way. The monster DCCS has these categories:

- **Champions** - Bosses (Beetle Queen, Magma Worm, etc.)
- **Minibosses** - Bigger, credit-heavy monsters (Greater Wisp, Golems, Elder Lemurians, etc.)
- **Basic Monsters** - Smaller, less credit-heavy monsters (Lemurians, Wisps, Vultures, etc.)
- **Special** - Scavenger

The interactable DCCS has these categories:

- **Chests** - Chests, Large Chests, Category Chests, Trishops, Equipment Barrels, Lunar Pods
- **Barrels** - Money Barrels
- **Shrines** - Shrine of Combat, Woods, Blood, Chance, Order, Mountain
- **Drones** - Healing Drones, Emergency Drones, Gunner Drones, etc.
- **Misc** - Gunner Turret
- **Rare** - Shrine of Gold, Radar Tower, Cloaked Chest, Legendary Chest
- **Duplicator** - Printers and Scrapper
- **Void Stuff** (Only Present in DLC1 DCCS) - Void Cradle and Void Seed

After filling out your DCCS you only have to figure out how your stage's log will be unlocked. You will first need to create an ``UnlockableDef`` by right clicking in the project field > ``Create`` > ``RoR2`` > ``UnlockableDef``. Then, you will want base the name of your Unlock Def after this naming scheme:

- ``Logs.Stages.``*Internal Stage Name*

For example, Abyssal Depth's logbook unlock is named ``Logs.Stages.dampcavesimple``. For Waffle House its ``Logs.Stages.waffle_wafflehouse``.

Afterwards, you will want to set up your Unlock Def like this:

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/3041693a-0ba8-47f5-a673-739c2bf54637)

Note that the ``Name Token`` field is the token for the text that appears when you unlock the Stage log entry.

Now you need to choose the condition your log book entry unlocks under. Realistically, you can unlock the stage's logbook entry under any condition like any other Unlockable. However, the game only uses two ways to unlock the stage log entry:

- Radio Tower - Used for stages in the loop.
- Logbook Pickup - Used for hidden realms, and the final stage.

If you want to use the Radio Tower, all you need to do is attach your Unlock Def to the ``Forbidden Unlockable Def`` field under the Radio Tower entry. Again, right click the context menu to use direct reference.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/f80cc0db-d1f5-4fe1-9bdc-01e0397d6e66)

If you want to use the Logbook Pickup, it is a bit tricky since you will not only need to instantiate the prefab into the scene, but you will also need to populate all the values into the pickup so it unlocks the stage log entry. The good news is that, if you are using LoP, you can use the ``Instantiate Logbook Prefab`` component to easily create the pickup. The component comes with fields to populate the ``Display Name Token`` and ``Display Name Token`` (the ``Display Name Token`` should be the same as the one in the Unlockable Def). The component also comes with the ``Enable Gravity`` boolean and a ``Velocity Random On Start`` component. Both of these will help assist customizing the movement of your pickup. Another feature of the component is the purple outline that signifies the pickup's position and orientation. This should help aid in your placement of the pickup.

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/cb4382bd-40f6-4153-8992-9cc5c7685041)

After this, your DCCS and DP should be ready to be implemented into your scene. To do this, you will just need to attach the interactable and monster DPs into the ``Interactable Dccs Pool`` and ``Monster Dccs Pool`` fields in your Classic Stage Info.

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/e0353fc8-7508-4907-ac5d-0cb1d0a24e26)

With that, your stage should be fully functioning by now. However, there are still more topics to help improve your stages.

## Optimization

Optimization is **extremely** important to making your stage accessible to players. Currently, this topic isn't as fleshed out as I (JaceDaDorito) want it to be. Please, if you have any good information about stage optimization, message me @JaceDaDorito on discord and I will add it here. That being said, I will present what I know.

**Keep track of your polygon count!** Especially on the stage's main terrain. As said earlier, you do not want the stage's collision to have condensed polygons. Not only that but higher polygon counts will drop framerate. If it helps, Fogbound Lagoon's main terrain has about 24k Triangles and 12k Verticies. Don't take this as golden rule though.

**Keep track of visual effects and light sources!** A lot of tiny light sources can easily destroy framerate, make sure to condense them into bigger light sources. Make sure to use LODs on objects that need to be disabled with lower graphics settings (like grass).

**Occlusion Culling** allows you to "turn off" scene objects out of view. These videos go into great detail of how it works. For reference, Fogbound Lagoon uses a ``Backface Threshold`` of 90, but it isn't known if that is the actual value RoR2 stages use. Putting ``Backface Threshold`` on 100 will make scene objects flash rapidly if the player camera is put against collision (like if you are looking straight up). If you find the "perfect" ``Backface Threshold`` value, let me know.

https://www.youtube.com/watch?v=DoHPx5RQ7P4

https://www.youtube.com/watch?v=hv2CUi2eeBY

## Visuals

This tutorial may detail visuals later down the line but it isn't important to making your stage function. However, LoP has the ``ShaderSwap`` class where you can feed a material into ``ConvertShader`` and it will swap stubbed shaders for the real shader. However, this function assumes you are using an updated set of stubbed shaders, of which are not officially distributed publicly as of 11/1/2023. We are still discussing the best way to distribute them. At the moment, the only way you can get them is by joining the RoR2 Modding Discord and downloading them from one of the pins in the #development channel. It will be this one: 

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/619a32a8-a6d6-46d1-b420-cbaedf183e3b)

Other than that, study how the base game implements its visuals.

## Inspecting Vanilla Stages in Unity

There is a simple way to view vanilla RoR2 stages in editor and it makes it very easy to disect them. The directions are in the screenshot present below:

![image-1](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/de8ca218-ea88-421a-967a-c3143f6e7209)

Line mentioned in screenshot:

```
"com.passivepicasso.thunderkit": "https://github.com/PassivePicasso/ThunderKit.git#1aeb51e18e41f65801f49f7ed290de5f54a90ebb",
```

![image](https://github.com/risk-of-thunder/R2Wiki/assets/66852270/b199bd72-5557-47d1-ad8e-1c5a3c7ad98c)

## Credits

- **JaceDaDorito** - Wrote this documentation, made LoP, made R2API.Stages, and lead person pushing for easier stage making.
- **IDeathHD** - Helped get this started with initial code and general advice.
- **Nebby** - Helped create and clean code. Implemented a lot of the utilities detailed here into RoR2EditorKit and R2API.
- **GrooveSalad** - Made initial addressable DCCS/DP implementation, made cleaner stubbed shaders, and gave a lot of advice.
- **TeamMoonstorm** - General feedback and guinea pig for a lot of these utilities.
- **Nerd Head** - Videos linked about Occlusion Culling.
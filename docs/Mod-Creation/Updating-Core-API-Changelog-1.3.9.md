# Core API Changelog 1.3.9 ‐ Memory Optimization Update

# Memory Optimization

## Overview
1.3.9 focuses on overarching memory optimizations for assets relating to:
  - Stage Assets
  - Character Meshes, Materials, Animations, etc.
  - Soundbanks

Each of these assets are situationally preloaded, loaded, and unloaded during runtime to reduce memory uptake.

- The folowing (as of now) still take up memory during the game instance:
  - Effects
  - Projectiles
  - Items
  - Equipment
  - Character Masters and Bodies
  - Anything with a catalog

To account for modding, this system is intended to be opt-in; mods should still load despite them not using these new systems. Utilization of these new systems are detailed later on in the document.

These systems request for an Asset's GUID instead of its AddressablePath. All of the game's GUID strings can be found in ``/StreamingAssets/lrapi_returns.json ``. A core benifit to using an Asset GUID is that it is far less likely to change compared to an Addressable path: an Asset's GUID is only dependent on the asset itself whilst its Addressable path depends on its file path in the source project.

**Misc Notes**:
Some Addressable Paths have changed in this update. Any Beetle related content paths got changed.

## Character Optimizations

### Skins

``SkinDef`` has been overhauled to not contain direct asset references. Instead, they contain ``SkinDefParams`` -a ScriptableObject that holds Addressable references to relevant skin assets. The following have been migrated to ``SkinDefParams``.
- Meshes
- Materials
- Additional assets like Projectile Ghost replacements

Characters **DO NOT** need to use the new ``SkinDef`` system to appear in game. The downside to not using the new system is higher memory uptake- the same amount of memory a skin would take before 1.3.9. The intention is to maintain backwards compatibility.

Avatars and RuntimeAnimatorControllers are now kept as Addressable References inside the ``ModelSkinController``

Display Prefabs now contain ``ModelSkinController``s, Skins added to a survivor's body's ``ModelSkinController`` should be added to the Survivor's display's ``ModelSkinController`` and match the body's skin  order.

### Misc

New ``CharacterMaster.PreloadAssets()``. Used to request a master to start preloading its assets.

## Asset Management

### RoR2.ContentManagement.AssetOrDirectReference

``AssetOrDirectReference<T>`` is a class to help direct references towards a source reference whilst also handling Addressable cleanup.
As the name implies, this class can be used to hold a reference to an asset, wether this asset is loaded dynamically using Addressables or has a direct reference.

Most of the classes below now contain an ``AssetReferenceT<T>`` for the addressable reference option. The systems in the game take the address as the ideal asset to use, if no valid address is specified, then the direct reference is used.

- ModelSkinController
- SkinDef / SkinDefParams
- ModelPanel
- ArtifactDef (``pickupModelPrefab`` (Obsolete) -> ``pickupModelReference``)
- DirectorCard (``spawnCardReference``)
- EquipmentDef (``pickupModelPrefab`` (Obsolete) -> ``pickupModelReference``)
- ItemDef (``pickupModelPrefab`` (Obsolete) -> ``pickupModelReference``)
- SceneDef 

Reminder: developers must use the GUID address, not the  Addressable path.

DirectorCard now uses ``GetSpawnCard()``, which utilizes ``AssetOrDirectReference<T>``. ``GetSpawnCard()`` returns the direct asset/reference from the DirectorCard field ``spawnCardReference``, a field of type ``AssetOrDirectReference<T>``. This feature can now used in constructing DCCS from scratch in editor. Modders can now input GUIDs into a DCCS as a substitute to a direct card reference.

### RoR2.ContentManagement.AssetAsyncReferenceManager

``AssetAsyncReferenceManager<T>`` is a new static class that handles and releases given Asset GUIDs. This class is powerful in the sense that it allows access to a specific asset with a given GUID and it is personalized to RoR2's specific use cases. An important note is that ``T`` needs to be a specific type, there cannot be assumptions on what type of asset a given GUID is refering to.

**LoadAsset**

Signature:

``AssetAsyncReferenceManager<T>.LoadAsset(AssetReference<T> reference,
AyncReferenceHandleUnloadType unloadType = AsyncReferenceHandleUnloadType.AtWill)
``

Return type: ``AsyncOperationHandle<T>``

``LoadAsset`` requires a GUID reference to an asset (``reference``) and UnloadType (covered later). The returned operation handle is asynchronous and will perform no operations if the targeted asset is already loaded. The operation handle can be paired with ``.WaitForCompletion()`` to force immediate asset access.

**UnloadAsset**

Signature:

``AssetAsyncReferenceManager<T>.UnloadAsset(AssetReference<T> reference)``

Return type: void

``UnloadAsset`` requires a GUID reference to an asset (``reference``). A targetted asset will not be unloaded from memory if it is still referenced from different sources.

### RoR2.ContentManagement.AsyncReferenceHandleUnloadType
The ``AsyncReferenceHandleUnloadType`` defines how long a reference will exist will exist in memory. Each UnloadType has ranking priority; for example, the unload priority of ``OnRunEnd`` is higher priority than ``AtWill``. Behaviors for each UnloadType is listed below in ascending priority:

- ``Preload``:
  - Latches onto an asset in memory until its promoted to a higher UnloadType or the scene changes.
- ``PreloadInMenu``:
  - Same as above, except it only unloads memory on scene transition when the scene is not a menu scene. Especially useful for loading Characters in the lobby screen: Character memory doesn't get dumped when you back out to the main menu.
- ``AtWill``:
  - If there are no more active references, unload this asset from memory.
  - Default UnloadType for ``LoadAsset``
  - Reference count is significant for this type and beyond. Make sure to call ``UnloadAsset`` to allow the asset to be released.
- ``OnSceneUnload``:
  - Unload on next scene load.
  - Useful for Monsters.
- ``OnRunEnd``:
  - Unload on run end; more specifically from a scene with a ``Run`` instance to a scene without one.
  - Useful for Characters.

An asset has a buffer window of around **5 seconds** between it being a candidate for unloading and it actually unloading. Asset unloading can be intercepted by rerequesting the asset or changing UnloadType priority.

An asset's UnloadType cannot be demoted. You cannot change an asset to go from ``OnRunEnd`` -> ``AtWill``.

## ItemDisplayRuleSet
IDRS items can now be loaded on demand- however, they still use direct references at the present moment. ItemDisplayRuleSets can now also accept a direct or addressable reference for both Key Assets and Display Prefabs.

Do note that if you choose to remain on old system of loading assets directly into followerPrefab via Addressables for base game items then set `followerPrefabAddress` to `new AssetReferenceGameObject("")` otherwise game will throw an error on item pickup and no display will be shown.

## NetworkPreloadManager
A networked singleton that handles preload requests.
- Survivors
  - ``SendScenePreloadMessage(SceneDef sceneDef)``: Preloads the survivors in the transition to the lobby.
- Scene Preload Requests
  - ``SendScenePreloadMessage(SceneDef sceneDef)``: Sends an asyncronous request all clients to start preloading a scene.
  - ``StartNewScenePreload(SceneDef sceneDef, bool preserveSceneInMenus)``: Preloads a local scene such as a menu. ``preserveSceneInMenus`` controls if a Scene gets released in a transition.
- Characters
  - ``SendMasterPreloadRequest(List<MasterCatalog.MasterIndex> masterIndices)``: Given certain indices of the MasterCatalog, this will send a networked request to clients to load certain CharacterMasters.

## Soundbank Optimizations

Most sounds are now loaded when needed instead of all times. Stages and Survivors now load their own banks (``AkBank`` component on the SceneInfo and CharacterBody respectively)

About 2/3rds of the soundbanks where cut from being in memory at all times. This includes ALL survivor and stage soundbanks, plus the ones listed below. Items where not touched.

A list of affected sound bank groups:
-	Boss_Artifact
-	Boss_FalseSon
-	Boss_Grandparent
-	Boss_Brother
-	Boss_majorConstruct
-	Boss_Gravekeeper
-	Boss_RoboBall
-	Boss_Scav
-	Boss_voidRaid
-	ArtifactShellBody
-	Mob_child
-	Mob_clayBruiser
-	Mob_clayGrenadier
-	Mob_halcyonite
-	mob_lunarGolem
-	Mob_lunarWisp
-	Mob_voidBarnacle
-	Mob_Vulture
-	Mob_voidJailer

# Other Changes

- New Attribute for executing on game startup: ``[InitDuringStartupPhase(GameInitPhase initPhase, int order)]`` (under namespace ``RoR2.``).
  - ``RoR2.GameInitPhase`` is a new enum describing the "phase" of the bootup sequence
  - ``order`` is the sorted execution order. An attribute with order "0" will execute before an attribute with order "2".
- Duplicate ``[SearchableAttribute]`` that did nothing has been removed.
- New ``AddChild`` function to ``ChildLocator``
- New ``RoR2.PlaySoundOnEvent`` class.

# Important note:
I (Jace) haven't had time to fully look through some of the more miscellaneous changes. If you feel like a change should be listed here, either ping "jacecascade" on discord or append it here directly. If you edit the document directly with changes, make sure to add yourself to the Credits.

# Credits
- JaceCascade
- Nebby1999
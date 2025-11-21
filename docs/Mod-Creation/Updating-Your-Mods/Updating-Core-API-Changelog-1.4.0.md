# Core API Changelog 1.4.0 - Alloy Collective

## Items

There have been widespread inventory api changes to support normal and temp items. `GetItemCount`, `GiveItem`, `RemoveItem`, etc exist for backwards compatibility, but the codebase now uses `GiveItemPermanent`, `GiveItemTemp`, etc. Any IL hook that used to match for those method names will now fail to match.

- The game now contains different "containers" for types of items, including Permanent and Temporary
- These are summed and eventually put into an "effective item count" container, which contains the finalized amount of items for stat/behaviour purposes.
- The int[] array representing the amount of items is no longer utilized by the game internally.
- GetItemCount: Takes from the Effective Item Count.
- Add Item: Redirects to adding specifically a Permanent Item.
- Remove Item: Reditects to removing specifically a Permanent Item.
- Temporary items are given as a normalized float, representing how "decayed" the item is.
    - For example, giving 0.5f of a temporary item will grant a new item that's 50% decayed. while giving 1.5f of a temporary item will grant two temporary items (where one of them has a 50% decayed value)

## Equipment

`DLC3Content.Items.ExtraEquipment` (Functional Coupler) allows each equipment slot to hold multiple equipment that you can cycle through. As a result, the array that used to store your various equipment has changed from `equipmentStateSlots[slot]` to `_equipmentStateSlots[slot][set]`.

While the old `GetEquipment` and `SetEquipment` still exist for backwards compatibility, they are obsolete as new overloads exist for an explicit `set` argument.

## Pickups

`UniquePickup` is the new generalised way to handle "instance" pickups (such as information from within the droplet) instead of `PickupIndex`.

In order to convert a pickup index to the new system use `new UniquePickup(pickupIndex)`.

Numerous classes have changed field and method signatures. Expect IL Hooks that were trying to match for the old ldfld/call to fail.

- BossGroup: changed type for `bossDrops`
- ChestBehavior: `dropPickup` has no setter anymore; use `currentPickup` in general
- OptionChestBehavior: `generatedDrops` -> `generatedPickups`
- PickupDisplay: `pickupIndex` -> `pickupState`
- PickupDropletController: `NetworkpickupIndex` -> `NetworkpickupState`
- PickupIndexNetworker: `NetworkpickupIndex` has been changed to `pickupIndex` and is also obsolete. Use `NetworkPickupState` instead.
- PickupPickerController
- RouletteChestController: anything referencing `PickupIndex` type has been changed to `UniquePickup`
- ShopTerminalBehavior
- Various changes in the droptable classes: BasicPickUpDropTable, FreeChestDropTable, etc

## Enum value changes

Some enums have inserted new values in the middle of the list order, effectively shifting some values. For any affected enums you'll need to recompile your mod and reserialize any assets.

- The enums in `BaseActivity`
- InterruptPriority: added `Taunt` and `Immobilize`
- CharacterBody.BodyFlags:
    - Renamed `IgnoreKnockback` -> `IgnoreKnockup`
    - Changed values for `UsesAmbientLevel` and `IgnoresRecordDeathEvent`
- DamageType:
    - Removed `Frost`
    - Changed value for `OutOfBounds`
- DamageTypeExtended
    - Changed value for `ChefSource`
    - Removed `Amputate`
- ItemTier
    - Added `FoodTier`
    - Since `AssignedAtRuntime` has been shifted, this will cause issues for custom item tiers
- VisibilityLevel: added `Untargetable`

## BodyPrefabs and DroneDefs in the content packs

These fields have been retroactively added to all content packs. You can now get a body index with, for example, `RoR2Content.BodyPrefabs.WispBody.bodyIndex` instead of having to cache that value after the catalog has been initialized or by using `BodyCatalog.FindBodyIndex`.

## Hook errors

If after updating MMHOOK you see a hook highlighted with a red line in your project, or the following error is logged when launching the game

```
TypeLoadException: Could not resolve type with token <address> from typeref (expected class 'hook_<name>' in assembly '')
```

it means the hook can't be found. While this can be simply because the original method has been removed, it is more likely to be due to all the various method overloads this DLC has added for backwards compatibility.

Let's take `CombatDirector.SpendAllCreditsOnMapSpawns` for example. In the past there used to be only one such method with that name, so the hook was simply called `On.RoR2.CombatDirector.SpendAllCreditsOnMapSpawns`. Now that there are two overloads for uniqueness they also include the method argument types, e.g,

```cs
On.RoR2.CombatDirector.SpendAllCreditsOnMapSpawns_Transform
On.RoR2.CombatDirector.SpendAllCreditsOnMapSpawns_Transform_WeightedSelection1
```

while the original simple hook name is nowhere to be found. While fixing the hook means updating the name, you should look at the methods in the source code to see which is now obsolete and use the other one. That's because the obsolete one is simply a wrapper for the general method which is always called.

Methods that are known to have been affected (this is not an exhaustive list, feel free to expand):

- CombatDirector.SpendAllCreditsOnMapSpawns
- DotController.AddDot
- SkinDef.RuntimeSkin.ApplyAsync

## Various field and method changes

- EntityStates.ArtifactShell.ArtifactShellBaseState
    - `DoOnPurchase`, `OnPurchase` signature change
        - This also affects inherited states, such as `WaitForKey`

- Removed EntityStates.Chef.IceCube

- EntityStates.EngiTurret.EngiTurretWeapon.FireBeam
    - Fixed an issue where a specific method was not being called, causing issues with inheriting states (see Xi Construct mega laser)

- EntityStates.FalseSon.LaserFather
    - Removed `laserPrefab`

- Entitystates.Vulture.FlyToLand
    - Changed return type for GetFootPosition

- InterfaceInstanceTracker has been added. It works like InstanceTracker but interfaces.

- CharacterAI.AIFireEquipmentOnOwnerDeath
    - Renamed `delayBeforeFiringEquipment` -> `SetGameOverTimer`
    - Removed `WaitToFireEquipment`

- Items.ContagiousItemManager
    - Removed `itemsToCheck`

- Convert any `ComboSkillDef` to `ComboSkillDefBase`. Not breaking, but the former is marked as obsolete and likely not working.

- UI.ItemIcon
    - `itemIndex` is now a property and the original field has changed to `_itemIndex`

- UI.Nameplate
    - `SetBody` -> `Initialize`

- UI.PingIndicator
    - Removed `pingHighlight` and added `pingTargetRenderers`; multiple highlight renderers are now supported.

- AimAnimator
    - Renamed `Start` -> `TryInitializeAnimator`

- ArtifactTrialMissionController
    - Changed signature for `GenerateDrop`

- BuffDefStockThresholdIcon
    - Moved the icon override and threshold to a struct; possible overrides are now possible at various thresholds

- BulletAttack
    - Changed `direction` from field to property
    - TODO access the rest

- CharacterBody
    - The `TransmitItemBehavior` methods have changed signature

- CharacterMotor
    - Changed signature for `ModifyGravity`

- CombatDirector
    - Spawn changed signature

- CostTypeCatalog
    - Changed signature for `PayCost`, `PayCostItems`, `PayCostEquipment`

- CostTypeDef
    - Changed signature for `PayCostDelegate` and `PayCost`
    - `PayCostContext` has changed from class to struct

- DamageInfo
    - Removed `canRejectForce`

- Glyphs
    - Changed signature for `GetGlyphString`

- GrandParentSunController
    - Removed `minimumStacksBeforeApplyingBurns`

- GravitatePickup
    - Removed `Start` (empty method)

- HealthComponent
    - Added `TakeDamageForce(PhysForceInfo)` and the other `TakeDamageForce` methods call that internally (may cause IL hooks to break)

- Inventory
    - Added separate containers for Temporary Items and Permanent Items
    - Added a system to lessen the amount of OnInventoryChanged calls.
    - Bunch of changes related to EquipmentStates to accomodate the new yellow boss item
    - Added the ability to disable inventories. where any items within the inventory have an effective item count of 0

- ItemStealController
    - A bunch of method signature changes

- JumpVolume
    - Changed signature for `onJump`

- MeridianEventTriggerInteraction
    - Removed `SetPlayersInvulnerable`

- MultishopController
    - Changed signature for `OnPurchase`

- OutlineHighlight
    - Removed `highlightQueue`

- OutsideInteractableLocker
    - `LockInfo` removed and effectively fused with `Candidates`
    - Changed type for `eggLockInfoMap`

- OverlapAttack
    - Changed signature for `PerformDamage`

- PhysForceInfo
    - Changed `flags` -> `_flags`
    - Removed `ignoreGroundStickFlag`, `disableAirControlUntilCollisionFlag`, and `massIsOneFlag`

- PickRandomObjectOnAwake
    - Moved `Awake` -> `OnEnable`

- PlayerCharacterMasterController
    - Removed `GetForward`

- PortalDialerController
    - Changed signature for `OnActivationServer`

- PreloadContent
    - It now uses `PreloadGameObjects` and `PreloadSkinDefParams` instead of asset references

- Run
    - Removed `stagesPerLoop`, `onLoopEvent`
    - Added `LoopBeginArgs`

- SceneExitController
    - Changed `GetDestinationSceneToPreload` -> `TryDetermineDestinationScene`

- ScrapperController
    - Removed `lastScrappedItemIndex`, `itemsEaten`, `Start`, `Update`
    - The code for `BeginScrapping(int)` has moved to `BeginScrapping(UniquePickup)` which can lead to IL hook fails

- SpawnReporter
    - Changed signature for `IncrementSpawnCount` and `IncrementCount`

- SphereSearch
    - Changed signature for `SearchData`

- TargetNearbyHealthComponents
    - Removed `sphereSearch`

- TeleportHelper
    - Added `TeleportBodyArgs` and changed the signature for `cachedTeleportRequestsForPlayersInVehicles` and `RequestVehicleExitAndWaitForCallback`

- TeleporterInteraction
    - Replaced `bossShrineIndicator` with `_bossShrineCounter`

- Util
    - `CleanseBody` is obsolete now; use `CleanseSystem.CleanseBody` instead

- WorkQueue
    - Changed `CanAffordProjectile` -> `CanDoWorkNow`

- UnseenHandIndicator
    - Changed type for `pairs`

## Credits

Chinchi, Nebby
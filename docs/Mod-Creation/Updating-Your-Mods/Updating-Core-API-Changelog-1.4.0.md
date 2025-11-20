# Core API Changelog 1.4.0 - Alloy Collective

## Big changes

- Widespread inventory api changes to support normal and temp items. GetItemCount, GiveItem, RemoveItem, etc exist for backwards compatibility, but the codebase now uses GiveItemPermanent, GiveItemTemp, etc. Any IL hook that used to match for those method names will now fail to match.
  - The game now contains different "containers" for types of items, including Permanent and Temporary
  - These are summed and eventually put into an "effective item count" container, which contains the finalized amount of items for stat/behaviour purposes.
  - The int[] array representing the amount of items is no longer utilized by the game internally.
  - GetItemCount: Takes from the Effective Item Count.
  - Add Item: Redirects to adding specifically a Permanent Item.
  - Remove Item: Reditects to removing specifically a Permanent Item.
  - Temporary items are given as a normalized float, representing how "decayed" the item is.
    - For example, giving 0.5f of a temporary item will grant a new item that's 50% decayed. while giving 1.5f of a temporary item will grant two temporary items (where one of them has a 50% decayed value)
- UniquePickup is the new generalised way to handle "instance" pickups (such as information from within the droplet) instead of PickupIndex. Affects numerous classes:
  - BossGroup: changed type for `bossDrops` to List<UniquePickup>
  - ChestBehavior: `dropPickup` has no setter anymore; use `currentPickup` in general
  - OptionChestBehavior: `generatedDrops` -> `generatedPickups`
  - PickupDisplay: `pickupIndex` -> `pickupState`
  - PickupDropletController: `NetworkpickupIndex` -> `NetworkpickupState`
  - PickupIndexNetworker: `NetworkpickupIndex` has been changed to `pickupIndex` and is also obsolete. Use `NetworkPickupState` instead.
  - PickupPickerController
  - RouletteChestController: anything referencing `PickupIndex` type has been changed to `UniquePickup`
  - ShopTerminalBehavior
  - Various changes in the droptable classes: BasicPickUpDropTable, FreeChestDropTable, etc
  - Expect any IL hooks that try to match for Pickup methods/fields to fail to match.
- IContentProvider classes now have BodyPrefabs and DroneDefs. Purely an addition, nothing breaking here.
- Some enums have inserted new values in the middle of the list order, effectively shifting some values:
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
  - VisibilityLevel: added `Untargetable`

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
  - Changed `bossShrineIndicator` -> `_bossShrineIndicator`

- Util
  - `CleanseBody` is obsolete now; use `CleanseSystem.CleanseBody` instead

- WorkQueue
  - Changed `CanAffordProjectile` -> `CanDoWorkNow`

- UnseenHandIndicator
  - Changed type for `pairs`

- DotController
  - Added `AddDot(GameObject attackerObject, float duration, HurtBox hitHurtBox, DotController.DotIndex dotIndex, float damageMultiplier, uint? maxStacksFromAttacker, float? totalDamage, DotController.DotIndex? preUpgradeDotIndex)` method and deprecated `AddDot(GameObject attackerObject, float duration, DotController.DotIndex dotIndex, float damageMultiplier, uint? maxStacksFromAttacker, float? totalDamage, DotController.DotIndex? preUpgradeDotIndex)` method

- SkinDef.RuntimeSKin
  - Added `ApplyAsync(GameObject modelObject, List<AssetReferenceT<Material>> loadedMaterials, List<AssetReferenceT<Mesh>> loadedMeshes, List<AssetReferenceT<GameObject>> loadedGameObjects, AsyncReferenceHandleUnloadType unloadType = AsyncReferenceHandleUnloadType.OnSceneUnload)` IEnumerator and deprecated `ApplyAsync(GameObject modelObject, List<AssetReferenceT<Material>> loadedMaterials, List<AssetReferenceT<Mesh>> loadedMeshes, AsyncReferenceHandleUnloadType unloadType = AsyncReferenceHandleUnloadType.OnSceneUnload)` IEnumerator
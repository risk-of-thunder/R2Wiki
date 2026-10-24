# Core API Changelog 1.5.0 - Hallowed Concepts

## Memory Optimisation 2 (memop2) changes

### IDRS

**WIP**

### JunkContent

This content pack only loads `ItemDef`, `EquipmentDef`, `BuffDef`, and `EliteDef` types. Everything else is not registered in catalogs, e.g., bodies, projectiles, entity states, scenes, etc. While in code it has been set to load all content when `RoR2Application.isModded`, in order to preserve vanilla compatibility, BepInExPack overrides this to respect the ignored types. If you want to make use of such content, and effectively restore how JunkContent used to be, simply add the following line to the Awake of your plugin.

```cs
RoR2BepInExPack.RoR2BepInExPack.LoadJunkContent();
```

### Projectile Ghosts

Vanilla Projectiles have moved their `ProjectileController.GhostPrefab` direct reference to an address instead in `ProjectileController.GhostPrefabAddress`.

If you were loading a vanilla projectile such as
```cs
GameObject projectile = Addressables.LoadAssetAsync<GameObject>("path/to/projectile").WaitForCompletion();
ProjectileController projectileController = projectile.GetComponent<ProjectileController>();
GameObject ghostPrefab = projectileController.ghostPrefab;
//do modify ghost prefab
//plug it into our projectile 
```
That ghost prefab is going to be null.  
You can change it to
```cs
GameObject ghostPrefab = projectileController.ghostPrefabAddress.LoadAssetAsync().WaitForCompletion();
```

If you were simply using the ghost prefab wholesale, you simply change
```cs
myProjectileController.ghostPrefab = loadedProjectile.ghostPrefab
```

to
```cs
myProjectileController.ghostPrefabAddress = loadedProjectile.ghostPrefabAddress
```

## ItemTag shift

The following item tags have shifted their values:

```
ObjectiveRelated
Technology
HiddenForDroneBuffIcon
SacrificeBlacklist
```

If you're referencing them in code, recompiling your mod will suffice. However, if you're using them in serialised assets, you'll need to manually correct that. This includes:

- `ItemDef.tags`
- Any `PickupDropTable` classes that use `requiredTags` and `forbiddenTags`
- `ShopTerminalBehavior.bannedItemTag`

## DamageType changes

The following `DamageTypeExtended` values have been removed and replaced by new ones:

- `LunarRuin`, `Drone`, `Grounded`, `ChefSource`, `Warp`, `IgniteChefOilBoosted`

Furthermore, `DamageTypeCombo` has removed `IsChefFireDamage` and its skill types now are handled by the new `DamageMacroAttribute` system. This allows to define niche case data to pass to the `DamageType.attackIdentityIndex` instead of having to constantly define new `DamageType` values, which has led to the situation of running out of enum values. In the case of CHEF, this is now handled in `EntityStates.Chef.Sear.DamageMacroChefFireAndIce`.

## Inventory equipment set array

The structure that handles this data has been reworked. While various methods that pass the `slot` and `set` arguments explicitly, e.g., `Inventory.SetEquipmentIndexForSlot` still exist for backwards compatibility, prefer using the new methods utilising the `EquipmentLocation` struct. The main breaking change here is that `_equipmentSetState[][]` has been removed and replaced by `_equipmentSlots[]`, where the set count is the same for all slots.

`activeEquipmentLocation` is a convenience getter for the current slot/set EquipmentLocation, while for an arbitrary pair you can construct it with

```cs
new EquipmentLocation
{
    slot = slotValue,
    set = setValue,
};
```

`equipmentSlotCount`/`equipmentSetCount` and the int versions `GetEquipmentSlotCount`/`GetEquipmentSetCount` can be used to iterate all the slot/set data.

## GlobalEventManager.OnCharacterDeath

Switch any calls from `GlobalEventManager.instance.OnCharacterDeath` to `GlobalEventManager.instance.HandleCharacterDeath`. Nothing breaks if you dont, but the latter is more accurate due to some extensions.

The death events are now also split into `GlobalEventManager.onCharacterDeathServer` and `GlobalEventManager.onDeathProcServer` to handle the above distinction. `onCharacterDeathGlobal` still works as before by pointing to `onDeathProcServer`, just be aware of the difference in usage moving forward.

## CharacterBody.ItemBehavior

This class has been officially marked obsolete and any items that were using it have been migrated to `BaseItemBodyBehavior`, which has existed for a long time. While `ItemBehavior` is still functional, this is a good opportunity to say to stop using it since there are a few edge cases where it doesn't behave as accurately as the other implementation.

## Signature changes

Very much WIP.

TLDR; Some methods have added new arguments and a recompilation suffices. OnHook signatures of such methods have to be fixed manually though. Other methods have left the original (obsolete) alone and added a new overload, however this can lead in a situation where the hook names must now disambiguate, e.g.,

```diff
- On.RoR2.CharacterMaster.GiveMoney
+ On.RoR2.CharacterMaster.GiveMoney_uint
+ On.RoR2.CharacterMaster.GiveMoney_uint_bool
```

A more fleshed out list of member changes/removals will hopefully come in a few weeks.
# Core API Changelog 1.4.1 - Alloyed Collective Patch

## Enum value changes

Some enums have inserted new values in the middle of the list order, effectively shifting some values. For any affected enums you'll need to recompile your mod and reserialize any assets.

- BaseActivity.ActivitySurvivor: added `VoidSurvivor`, `Seeker`, `False Son` (`Chef` still missing I guess)
- DamageTypeExtended: added `BypassDamageCalculations` and `NoGravity`
- ItemTier: `AssignedAtRuntime` has been moved from the next natural value (11) to 1000
- ProcType: removed ExplodeOnDeathVoid

## ContentPack additions

- DLC1Content.Buffs.ExplodeOnDeathVoidHiddenCooldown
- DLC3Content.Buffs.UltimateMealDecaying

## Various field and method changes

- EntityStates.Distributor.DistributorIdle
    - Removed `effectInstance` and `effectInstanceEFH`. The newly addd `muzzleObject` now seems to fill that role.

- EntityStates.Drifter.BluntForceTornado
    - Removed `OnMeleeHitAuthority`. It only served to impact force, which is now taken care in `AuthorityModifyOverlapAttack`.

- EntityStates.Drifter.ChargeTornadoSlam
    - Renamed `startSpeed` -> `startSpeedXZ`

- EntityStates.Drifter.CubeSlam
    - Removed `consoleCubePrefab`, `cube`, and `SetCube`

- EntityStates.Drifter.Repossess
    - Replaced `SetUpRepossessHighlight` with `GetOrConfigureHighlight`

- EntityStates.Drifter.TornadoSlam
    - Removed `bounceTuah`
    - Removed `OnMeleeHitAuthority` and added `ModifyOverlapInfo`. The latter one now modifies the collected hitboxes, which is set as a callback in `AuthorityModifyOverlapAttack`
    - Changed the signature of `GenerateJunk(Transform)` -> `GenerateJunk(Transform, int)`

- EntityStates.Vehicles.ZiprailCharacterVehicleSeated
    - Removed `Update` (it was an empty method anyway)

- Moved `RoR2.Achievements.Merc.DroneTechJuggleLemurianAchievement` from `Merc` -> `DroneTech`

- RoR2.Artifacts.PrestigeBulwarkManager
    - Removed `isArtifactEnabled`. Use `missionController.currentArtifact != DLC3Content.Artifacts.Prestige` instead.

- RoR2.Items.PhysicsProjectileBehavior
    - Removed `hasSpawnedAlly`; `canSpawnAlly` takes care of this check now.

- CharacterMaster
    - The logic for revival items has moved from `OnBodyDeath` to `TryReviveOnBodyDeath` so any hooks relevant to that may need updating.

- CostTypeDef
    - Added back `results` that 1.4.0 had removed

- ItemDef
    - Changed the signature of `HandleDuplicator(ItemIndex, CharacterBody)` -> `HandleDuplicator(ItemIndex, CharacterBody, float)`

- Moved `TriggerOnPlayerDeath` -> `TriggerOnNoPlayersLeft`

## Credits

Chinchi
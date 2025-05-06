# Core API Changelog 1.3.8 - Seekers of the Storm 2.0 Phase 3

## Disclaimer
Modders who participated in the 1.3.8 beta have no responsibility for any of the changes listed below. 

Most, if not all EntityStates for each of the DLC2 characters were changed to some degree; if a certain state isn't listed here, it does not mean it wasn't changed.

## Most Important
- RoR2.GenericSkill
  - `AssignSkill(SkillDef)` -> `AssignSkill(SkillDef, bool)`
- EntityStates/GenericCharacterMain
  - `ProcessJump()` now internally calls `ProcessJump(bool)`. This mostly affects ILHooks

## Core

- RoR2.CameraRigController
  - Removed some music related fields and methods
- RoR2.CameraTargetParams
  - Removed `aimRequestStack` and `RemoveRequest`
- RoR2.CharacterBody
  - Removed tamperedHeart-related fields. These are now handled by `ComputeTamperedHeartBonus`
- RoR2.CharacterMaster
  - Removed `SEEKER_REVIVE_STATUS`, `seekerUsedRevive`, and `getSeekerUsedRevive`
- RoR2.CharacterModel
  - `invisibilityCount` -> `_invisibilityCount`
- RoR2.DLC2Content
  - Removed `CookingFlambe`
- RoR2.SceneDef
  - Added `preventStageAdvanceCounter`. This seemingly just prevents a scene from bumping the stage counter. (Tooltip describing the field is wrong)


## Chef
- ChefController
    - `yesChefHeatActive` renamed to `yesChefActive`
    - `Dictionary<ushort, CleaverProjectile> cleaverDictionary` -> `Dictionary<NetworkInstanceId. CleaverProjectile> cleaverDictionary`
    - `SetYesChefHeatState` renamed to `SetYesChefState`
    - `AddLocalProjectileReference` now only takes a `CleaverProjectile`
    - `RemoveLocalProjectileReference` now only takes a `NetworkInstanceId`
- EntityStates.Chef.Dice
    - Removed `recallAnimationTransitionTime`, `approximateCleaverDistance`, and `recallInputPressed` fields.
- EntityStates.Chef.RolyPoly
    - Removed `chargeDamageCoefficientLvlUp`, `impactSoundString`, `startSoundString`, `knockbackDamageCoefficient`, `massthresholdForKnockback`, `chestIndex`, `temporaryInstantiationTransform`, `midEffectInstance`, `chefController`, `hasBoost`, and `charge` fields.
- EntityStates.Chef.Sear
    - `Update` -> `FixedUpdate`
- EntityStates.Chef.YesChef
    - `YesChef : BaseState` -> `YesChef : GenericCharacterMain`
    - Removed `heatDamageCo` and `blastEffectPrefab`
- `YesChefHeatProjectileTimer` -> `YesChefProjectileTimer`
- RoR2.Projectile.CleaverProjectile
    - Now inherits `IProjectileSpeedModifierHandler`
    - Removed `transitionDuration`, `maxFlyStopwatch`, `fireAge`, `fireFrequency`, `travelTime`, `stoppedStopwatch` and `predictionId`
- Removed EntityStates.Chef.ChargeRolyPoly
- Chef Oil now synergizes with IgnitionTank. When an oiled enemy is hit with the Ignition Tank debuff, it is treated as if there is +1 stack of the item.

## False Son
- EntityStates.FalseSon.ClubGroundSlam
    - `ClubGroundSlam : BaseState` -> `ClubGroundSlam : ClubGroundSlamBase`
    - Removed `normalizedVFXPositionBetweenFootAndClub` and `normalizedBlastPositionBetweenFootAndClub`
- EntityStates.FalseSon.LaserFatherBurst
    - Removed `blastRadius`, `force`, `minSpread`, `bulletCount`, `baseDuration`, `laserTimerPercent`, `laserDirection`, `charge`, `maxSecondaryStock`, `secondaryStock`, `modelTransform`, `muzzleTransform`, `rayDistance`, and `firedLaser`
- EntityStates.FalseSon.LaserFatherCharged
    - Removed `fireFrequency` and `lunarSpikeCount`
- EntityStates.FalseSon.LunarSpikes
    - Removed `procCoefficient`, `force`, `attackSpeedAltAnimationThreshold`, `attackSoundString`, `bloom` and  `hasFiredSpike`.
- Removed `EntityStates.FalseSon.ClubForsaken`, `EntityStates.FalseSon.ClubForsaken2`, and `EntityStates.FalseSon.ClubSwing2`.
- RoR2.FalseSonController
  - Was completely rewritten

## Seeker
- EntityStates.Seeker.Meditate
  - Completely Rewritten
  - Seems to no longer have network gated inputs
- EntityStates.Seeker.Sojourn
  - Various fields removed, they have been moved to the new `SojournBase`
- RoR2.SeekerController
  - Removed `currentChakraGate`. This is now handled by CharacterMaster.seekerChakraGate`
- RoR2.SojournVehicle
  - A lot of its functionality and fields were moved to `SojournVehicleBase`
- Removed `EntityStates.SeekerBaseActive`, `EntityStates.Seeker.BaseScopeState`, `EntityStates.Seeker.BaseWindDown`, `EntityStates.Seeker.BaseWindUp`, and `EntityStates.Seeker.MeditationUI`


## MUL-T
- EntityStates.Toolbot.BaseNailGunState / FireBuzzsaw / FireGrenadeLauncher / FireSpear
  - Assigns `Primary` or `Secondary` damage sources depending on the skill's slot. This is mainly for Power Mode. (Example: if Buzzsaw is my secondary in Power Mode, it won't activate Breaching Fin.)

## Prime Meridian / False Son Boss
- EntityStates.FalseSonBoss.SkyJumpDeathState
  - Field type of `halcyonSeed` PickupIndex -> ItemIndex
- EntityStates.PrimeMeridian.LunarGazeLaserFire
  - Removed `lunarGazeDamageType`
- EntityStates.PrimeMeridian.LunarGazeLaserFirePlus
 - Removed `plusDamageCoefficient` and `plusProcCoefficientPerTick`

## Misc
- EntityStates.Missions.MiniGeodeTracker.Listening
  - `maxStageClearCount` -> `maxStageTierOrder`
  - `bannedEventFlag` -> `bannedGeodesBrokenEventFlag`
- Added `JitterBoneBlacklist`. Used if certain characters break when picking up the Malachite Aspect.
- RoR2.TeleportOnLowHealthBehavior
  - `TryProc(HealthComponent, int)` -> `TryProc(HealthComponent)`
- RoR2.WokController
  - Fields and method changes in regards to networking

## Credits
- ChinChi, JaceCascade, Moffein
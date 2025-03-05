# Core API Changelog 1.3.7 - Seekers of the Storm 2.0 Phase 2

## Disclaimer
Modders who recieved the 1.3.7 Beta are not affiliated with Gearbox. Said modders are not responsible nor liable for these changes or any frustration.

If there are any significant changes that are worth being documented that aren't already, simply leave a post in the ``1.3.7 API Changelog`` thread in ``#development`` in the RoR2 Modding Discord. It is possible we missed something.

## DCCSBlender
__Prior to 1.3.7__, each ``DccsPool`` (DP) with monsters/interactables from both DLCs would need a ``DirectorCardCategorySelection`` (DCCS) for the base game, DLC1, DLC2, and DLC1+2. For example, ``dpSkyMeadowMonsters`` would need:

- ``dccsSkyMeadowMonsters`` (The base pool)
- ``dccsSkyMeadowMonstersDLC1`` (The pool for DLC1)
- ``dccsSkyMeadowMonstersDLC2`` (The pool for DLC2)
- ``dccsSkyMeadowMonstersDLC1+2`` (The pool for DLC1+2)

This approach had some side effects where the latter three DCCS would compete with each other (``dccsSkyMeadowMonsters`` doesn't compete because it is in the ``Always Include`` field of the ``DccsPool``).  It explains why, for example, Magma Worm would be able to spawn in Sky Meadow even though DLC1 was enabled. It also explains why Void Cradles cannot roll sometimes.

A con with this approach is scalability. To support both DLCs in a modded/new stage, you would need 4 monster DCCS and 4 interactable DCCS. There are a lot of repeated entries across all of these assets and it results in a massive time sink to implement them all.

__Post 1.3.7__, a given DP would only need 1 DCCS per expansion. ``dpSkyMeadowMonsters`` now only needs:

- ``dccsSkyMeadowMonsters``
- ``dccsSkyMeadowMonstersDLC1``
- ``dccsSkyMeadowMonstersDLC2``

Additionally, the ``DCCSBlender`` class will automatically blend all of the dccs together. There are no longer repeated card entries across a DP's DCCS:

- ``dccsSkyMeadowMonsters`` only contains cards tied to the base game.
- ``dccsSkyMeadowMonstersDLC1`` only contains cards tied to DLC1.

![](https://i.imgur.com/8s88fRL.png)

There are new fields in the ``ClassicStageInfo`` of each RoR2 map called ``contentSourceMixLimitOverride`` and ``contentSourceMixLimitOverrideValue``. Currently, the game only mixes a maximum of 2 DCCS into the base dccs provided. This means if there was a third expansion (a mod or a hypothetical DLC3) with its own DCCS, it will compete with DLC1 and DLC2 dccs. The ``contentSourceMixLimitOverride`` fields overrides that behavior per scene to allow more or fewer mixes.

### What needs to be done:

**For developers that add monster entries via R2API:** wait. R2API will update to account for the DCCS changes.

**For developers that make new stages or monster pools**:

- Backup your stage's DCCS and DP in case of future changes.
- Remove any DLC1+2 DCCS from the associated DP.
- Remake any DLC1 or DLC2 DCCS to make it only contain respective DLC content. For example: Fogbound Lagoon's DLC1 Monster DCCS should only contain Xi Construct, Alpha Construct, Gup, etc.
- Your DP should reflect this:

![](https://i.imgur.com/ATgHccz.png)


## False Son Entity States
Various entity states related to the boss fight make changes in fields and methods available which need to be adapted if accessed previously.

- ``EntityStates.FalseSonBoss.FissureSlam`` removed ``minimumDuration``, ``swingEffectInstance``, ``detonateNextFrame``, ``amServer``, ``totalFissuresFired``, and ``timeToGenerateNextFissure``.
- ``EntityStates.FalseSonBoss.FissureSlamWindup`` removed ``gauntlet``, ``soundID``, ``chargeAnimPlayed``, and ``stateAborted``.
- ``EntityStates.FalseSonBoss.LunarRaine`` removed ``nodesToTeleportTo``, ``fissureLocation``, and ``lunarRainStartTime``. ``fisurreLocs`` changed from a ``List<NodeGraph.NodeIndex>`` to a ``List<Vector3>``.
- ``EntityStates.SwatAwayPlayerSlam`` removed ``damageCoefficient``, ``hitEffectPrefab``, ``procCoefficient``, ``pushAwayForce``, ``forceVector``, ``impactSound``, ``minimumDuration``, ``blastRadius``, ``blastProcCoefficient``, ``blastDamageCoefficient``, ``blastForce``, ``blastBonusForce``, ``blastImpactEffectPrefab``, ``blastEffectPrefab``, and ``fistEffectPrefab``. ``hitBoxGroupName`` is now static. ``swingEffectInstance`` is now not static. 
- ``EntityStates.FalseSonBoss.LunarGazeFire`` and ``EntityStates.FalseSonBoss.LunarGazeFirePlus`` completely removed.
- ``RoR2/MeridianEventTriggerInteraction`` massive changes.



## Item References
- The following items have been renamed:
  - ``LowerHealthBody`` -> ``AttackSpeedPerNearbyAllyOrEnemy`` (and similar for the related buff, collider, and item behavior)
  - ``GoldOnStageStart`` -> ``BarrageOnBoss`` (and similar for the related buff and item behavior)
  - ``ResetChests`` -> ``ItemDropChanceOnKill``
- Note that this changes the Addressable Keys for any of the associated item assets.

## Character Body Flag UseAmbientLevel
- ``CharacterBody.BodyFlags.UsesAmbientLevel`` has been added.

## Damage Source Equipment
- There is now a new ``DamageSource`` type called ``Equipment``. This is a flag that tracks damage done by an equipment.
- This is not included in the already existing ``SkillMask``.

## Other API Changes
- ``RoR2.CharacterBody.OnShardDestroyed`` changed its method signature.
- ``RoR2.Stage.FixedUpdate`` has been renamed to ``Update``.
- ``RoR2.TeleportOnLowHealthBehavior`` removed ``timesAddedThisLevel``.
- ``RoR2.UI.ChatBox.Show`` changed its method signature.

## Credits
- Writing: JaceCascade and Chinchi
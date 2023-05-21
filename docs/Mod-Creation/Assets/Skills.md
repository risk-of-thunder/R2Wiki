# Skills

This walk-through assumes you know how to make a mod and implement it using BepInEx, if not go to [First Mod](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Getting-Started/First-Mod/)

We will be creating a custom skill for the Commando, with as little fuss as possible

### Step 1: Creating Our SkillDef

To begin your mod, copy and paste the code below, substituting for your own names where suitable.

```csharp
using System;
using BepInEx;
using EntityStates;
using R2API;
using RoR2;
using RoR2.Skills;
using UnityEngine;
using UnityEngine.AddressableAssets;

namespace CustomSkillsTutorial
{
    [BepInDependency(R2API.ContentManagement.R2APIContentManager.PluginGUID)]
    [BepInDependency(LanguageAPI.PluginGUID)]

    [BepInPlugin(
        "com.MyName.IHerebyGrantPermissionToDeprecateMyModFromThunderstoreBecauseIHaveNotChangedTheName",
        "IHerebyGrantPermissionToDeprecateMyModFromThunderstoreBecauseIHaveNotChangedTheName",
        "1.0.0")]
    public class CustomSkillTutorial : BaseUnityPlugin
    {
        public void Awake()
        {
            // First we must load our survivor's Body prefab. For this tutorial, we are making a skill for Commando
            // If you would like to load a different survivor, you can find the key for their Body prefab at the following link
            // https://xiaoxiao921.github.io/GithubActionCacheTest/assetPathsDump.html
            GameObject commandoBodyPrefab = Addressables.LoadAssetAsync<GameObject>("RoR2/Base/Commando/CommandoBody.prefab").WaitForCompletion();

            // We use LanguageAPI to add strings to the game, in the form of tokens
            // Please note that it is instead recommended that you use a language file.
            // More info in https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Localization/
            LanguageAPI.Add("COMMANDO_PRIMARY_SIMPLEBULLET_NAME", "Simple Bullet Attack");
            LanguageAPI.Add("COMMANDO_PRIMARY_SIMPLEBULLET_DESCRIPTION", $"Fire a bullet from your right pistol for <style=cIsDamage>300% damage</style>.");

            // Now we must create a SkillDef
            SkillDef mySkillDef = ScriptableObject.CreateInstance<SkillDef>();

            //Check step 2 for the code of the CustomSkillsTutorial.MyEntityStates.SimpleBulletAttack class
            mySkillDef.activationState = new SerializableEntityStateType(typeof(CustomSkillsTutorial.MyEntityStates.SimpleBulletAttack));
            mySkillDef.activationStateMachineName = "Weapon";
            mySkillDef.baseMaxStock = 1;
            mySkillDef.baseRechargeInterval = 0f;
            mySkillDef.beginSkillCooldownOnSkillEnd = true;
            mySkillDef.canceledFromSprinting = false;
            mySkillDef.cancelSprintingOnActivation = true;
            mySkillDef.fullRestockOnAssign = true;
            mySkillDef.interruptPriority = InterruptPriority.Any;
            mySkillDef.isCombatSkill = true;
            mySkillDef.mustKeyPress = false;
            mySkillDef.rechargeStock = 1;
            mySkillDef.requiredStock = 1;
            mySkillDef.stockToConsume = 1;
            // For the skill icon, you will have to load a Sprite from your own AssetBundle
            mySkillDef.icon = null;
            mySkillDef.skillDescriptionToken = "COMMANDO_PRIMARY_SIMPLEBULLET_DESCRIPTION";
            mySkillDef.skillName = "COMMANDO_PRIMARY_SIMPLEBULLET_NAME";
            mySkillDef.skillNameToken = "COMMANDO_PRIMARY_SIMPLEBULLET_NAME";

            // This adds our skilldef. If you don't do this, the skill will not work.
            ContentAddition.AddSkillDef(mySkillDef);

            // Now we add our skill to one of the survivor's skill families
            // You can change component.primary to component.secondary, component.utility and component.special
            SkillLocator skillLocator = commandoBodyPrefab.GetComponent<SkillLocator>();
            SkillFamily skillFamily = skillLocator.primary.skillFamily;

            // If this is an alternate skill, use this code.
            // Here, we add our skill as a variant to the existing Skill Family.
            Array.Resize(ref skillFamily.variants, skillFamily.variants.Length + 1);
            skillFamily.variants[skillFamily.variants.Length - 1] = new SkillFamily.Variant
            {
                skillDef = mySkillDef,
                unlockableName = "",
                viewableNode = new ViewablesCatalog.Node(mySkillDef.skillNameToken, false, null)
            };
        }
    }
}
```

### Step 2: Coding Our Skill

The actual code that governs the behavior of the skill exists in an entity state class.

To create a new skill it needs to be inside a `public class` with its base class being `BaseState`; for this it must have `using RoR2`.

The namespace of this class shouldn't be in EntityStates.

Each skill class **must** contain the following methods

```csharp
public override void OnEnter()
{
    base.OnEnter();
}

public override void OnExit()
{
    base.OnExit();
}

public override void FixedUpdate()
{
    base.FixedUpdate();
}

public override InterruptPriority GetMinimumInterruptPriority()
{
    return InterruptPriority.ChooseYourPriorityHere;
}
```

`OnEnter()` is run once at the start of the skill.

It **needs** to begin with `base.OnEnter()` after that you can add any code you like to the function an example being `Chat.AddMessage("IT'S ALIVE")` which shows "IT'S ALIVE" when it's called.

`OnExit()` is run once at the end of the skill. It **needs** to end with `base.OnExit()` and before that you can add any code you like.

`FixedUpdate()` is run (almost) every frame of the skill.

Example usage of this would be to add a delay to an attack, or to count down the attack interval for multi-hit moves like Commando's Barrage or Merc's Eviscerate.

This **needs** to begin with `base.FixedUpdate` and needs to contain a block of code which checks if the skill can end, otherwise the character will get stuck in the skill state.

Almost all skills in the game have the following block of code, or something similar:

```csharp
if (fixedAge >= this.totalDuration && isAuthority)
{
    outer.SetNextStateToMain();
    return;
}
```

This requires a variable in the skill `float totalDuration`.

`GetMinimumInterruptPriority()` controls which skills get a priority over others.

When a skill is activated, if it has the same or higher priority than the priority returned by this method, it calls `OnExit()` in the canceled skill function and calls its own `OnEnter()`.

Skills with lower priority do nothing while the skill is running.

It is usually recommended that the `InterruptPriority` returned by this method be higher than the `InterruptPriority` defined in the `SkillDef`, otherwise a skill will be able to cancel itself on frame 1 as long as the button is held down.

`GetMinimumInterruptPriority()` **needs** to contain `return InterruptPriority.yourpriority;`

For this tutorial, we will simply be firing a single bullet from Commando's right pistol.

Our skill state class will look like this:

```csharp
using EntityStates;
using RoR2;
using UnityEngine;
//Since we are using effects from Commando's Barrage skill, we will also be using the associated namespace
//You can also use Addressables or LegacyResourcesAPI to load whichever effects you like
using EntityStates.Commando.CommandoWeapon;

namespace CustomSkillsTutorial.MyEntityStates
{
    public class SimpleBulletAttack : BaseSkillState
    {
        public float baseDuration = 0.5f;
        private float duration;

        public GameObject hitEffectPrefab = FireBarrage.hitEffectPrefab;
        public GameObject tracerEffectPrefab = FireBarrage.tracerEffectPrefab;

        //OnEnter() runs once at the start of the skill
        //All we do here is create a BulletAttack and fire it
        public override void OnEnter()
        {
            base.OnEnter();
            this.duration = this.baseDuration / base.attackSpeedStat;
            Ray aimRay = base.GetAimRay();
            base.StartAimMode(aimRay, 2f, false);
            base.PlayAnimation("Gesture Additive, Right", "FirePistol, Right");
            Util.PlaySound(FireBarrage.fireBarrageSoundString, base.gameObject);
            base.AddRecoil(-0.6f, 0.6f, -0.6f, 0.6f);
            if (FireBarrage.effectPrefab)
            {
                EffectManager.SimpleMuzzleFlash(FireBarrage.effectPrefab, base.gameObject, "MuzzleRight", false);
            }

            if (base.isAuthority)
            {
                new BulletAttack
                {
                    owner = base.gameObject,
                    weapon = base.gameObject,
                    origin = aimRay.origin,
                    aimVector = aimRay.direction,
                    minSpread = 0f,
                    maxSpread = base.characterBody.spreadBloomAngle,
                    bulletCount = 1U,
                    procCoefficient = 1f,
                    damage = base.characterBody.damage * 3f,
                    force = 3,
                    falloffModel = BulletAttack.FalloffModel.DefaultBullet,
                    tracerEffectPrefab = this.tracerEffectPrefab,
                    muzzleName = "MuzzleRight",
                    hitEffectPrefab = this.hitEffectPrefab,
                    isCrit = base.RollCrit(),
                    HitEffectNormal = false,
                    stopperMask = LayerIndex.world.mask,
                    smartCollision = true,
                    maxDistance = 300f
                }.Fire();
            }
        }

        //This method runs once at the end
        //Here, we are doing nothing
        public override void OnExit()
        {
            base.OnExit();
        }

        //FixedUpdate() runs almost every frame of the skill
        //Here, we end the skill once it exceeds its intended duration
        public override void FixedUpdate()
        {
            base.FixedUpdate();
            if (base.fixedAge >= this.duration && base.isAuthority)
            {
                this.outer.SetNextStateToMain();
                return;
            }
        }

        //GetMinimumInterruptPriority() returns the InterruptPriority required to interrupt this skill
        public override InterruptPriority GetMinimumInterruptPriority()
        {
            return InterruptPriority.Skill;
        }
    }
}
```

After building the solution and putting the DLL in your plugins folder, the Commando should now have an additional alt primary skill.

# Addendum

## Creating New Skill Families

If you want to create a skill that does not fall into a survivor's existing skill families (primary, secondary, etc.), you can do so using the following code, inserted after you have added the `SkillDef` with the `ContentAddition` API.

```csharp
//First we add a new GenericSkill component to the survivor's body prefab
GenericSkill uniqueSkill = commandoBodyPrefab.AddComponent<GenericSkill>();
uniqueSkill.skillName = commandoBodyPrefab.name + "UniqueSkill";

//Then we create a new skill family
SkillFamily uniqueFamily = ScriptableObject.CreateInstance<SkillFamily>();
(uniqueFamily as ScriptableObject).name = commandoBodyPrefab.name + "UniqueFamily";
uniqueSkill.SetFieldValue("_skillFamily", uniqueFamily);
uniqueFamily.variants = new SkillFamily.Variant[0];

//Now we add our skill to the new skill family's variants, same as before
Array.Resize(ref uniqueFamily.variants, uniqueFamily.variants.Length + 1);
uniqueFamily.variants[uniqueFamily.variants.Length - 1] = new SkillFamily.Variant
{
    skillDef = mySkillDef,
    unlockableName = "",
    viewableNode = new ViewablesCatalog.Node(mySkillDef.skillNameToken, false, null)
};

//Finally we add our new SkillFamily using the ContentAddition API
ContentAddition.AddSkillFamily(uniqueFamily);
```

# Creating an Elite 

## Choosing colors for Elites
The following is an excerpt taken from a conversation between Nebby and Buns on Risk of Rain 2 modcord. Original message chain starts [here](https://discord.com/channels/562704639141740588/562704639569428506/1169002235368317000)


Ideally this page gets updated to be more official.

Elite Ramps are just shaders 
![image](https://github.com/risk-of-thunder/R2Wiki/assets/15069763/e8620328-2d00-4ec9-87c6-8a3186f25a10)


Shader wise, all a ramp in the game is doing is that transforms the RGBA of whatever diffuse texture into a grayscale texture and then uses the X&Y values as the UV for the ramp

<img src="https://github.com/risk-of-thunder/R2Wiki/assets/15069763/96253e53-26ff-4126-809f-7513a9a17e82" alt="ramp example" width="512" height="512"/>

as a result, what basically happens is:

>Lighter colors in the diffuse texture will use the colors on the right side of the ramp

>Darker colors in the diffuse texture will use the colors on the left side of the ramp

Important thing to remember is that when setting up import setting in Unity for ramp texture you need to untick "Generate Mip Maps" and "Wrap Mode" should be "Clamp". Otherwise it would use colours all over the place. Also don't forget about "Alpha is Transparency" if your ramp has transparency.

![image](https://github.com/user-attachments/assets/78a5c54a-eca2-444f-a938-44922ff7f807)


Example elite ramp:

![texRampEliteVolatile](https://github.com/risk-of-thunder/R2Wiki/assets/15069763/5c901966-b824-47f8-b6f1-d3825917e620)

Elder Lemurian:

<img src="https://github.com/risk-of-thunder/R2Wiki/assets/15069763/4171c84f-9699-4b7c-b472-87d563685de3" alt="elder lemurian" width="512" height="512"/>

Example Elite ramp + elder lemurian:

<img src="https://github.com/risk-of-thunder/R2Wiki/assets/15069763/396f16fd-dfbc-4ab6-9082-98cef44c3475" alt="elder lemurian with elite ramp" width="512" height="512"/>

notice how the darkest parts of the elder lemurian (forearms and lower legs) are colored blue while the more neutral colors are tinted orange
and the lightest color, is tinted white-ish
so yeah, the base color of the diffuse map is what determines the color picked on the ramp.

another example using huntress' default skin and the color ramp above:

<img src="https://github.com/risk-of-thunder/R2Wiki/assets/15069763/6a3f7ae8-c7ac-4c12-8840-8b50aac4b949" alt="huntress" width="512" height="512"/>

here's another one, leeching elites from Lost In Transit:

<img src="https://github.com/risk-of-thunder/R2Wiki/assets/15069763/8bf6dfe4-641a-4388-be47-0482c7ed38ec" alt="leeching elite" width="512" height="512"/>

the leeching elite ramp shader:
![texRampEliteLeeching](https://github.com/risk-of-thunder/R2Wiki/assets/15069763/53e8630c-bd74-44a3-a0f9-78a627a1648f)


Overall the most important thing on the ramp is knowing how to properly choose harmonious colours so the colors themselves dont clash, having a grasph on color theory (such as complementary colours, monochrome, ) works quite well.

## Example Elite code

The following code snippet is taken from Nuxlar's MoreElites mod. Repo can be found [here](https://github.com/FocusedFault/MoreElites/tree/main)

Empowering Elite code:
```
using R2API;
using RoR2;
using System.Collections.Generic;
using System.Linq;
using UnityEngine;
using UnityEngine.AddressableAssets;

namespace MoreElites
{
  public class Empowering
  {
    public static Color AffixEmpoweringColor = new Color(1f, 0.5f, 0.0f);
    public static EquipmentDef AffixEmpoweringEquipment;
    public static BuffDef AffixEmpoweringBuff;
    public static EliteDef AffixEmpoweringElite;
    public static float healthMult = 4f;
    public static float damageMult = 2f;
    public static float affixDropChance = 0.00025f;
    private static GameObject EmpoweringWard = PrefabAPI.InstantiateClone(Addressables.LoadAssetAsync<GameObject>("RoR2/Base/EliteHaunted/AffixHauntedWard.prefab").WaitForCompletion(), "EmpoweringWard");
    private static Material empoweringMat = Addressables.LoadAssetAsync<Material>("RoR2/Base/WardOnLevel/matWarbannerBuffRing.mat").WaitForCompletion();
    private static Texture2D eliteRamp = Addressables.LoadAssetAsync<Texture2D>("RoR2/Base/Common/ColorRamps/texRampMagmaWorm.png").WaitForCompletion();
    private static Sprite eliteIcon = Addressables.LoadAssetAsync<Sprite>("RoR2/Base/EliteIce/texBuffAffixWhite.tif").WaitForCompletion();
    // RoR2/Base/Common/ColorRamps/texRampWarbanner.png 

    public Empowering()
    {
      EmpoweringWard.transform.GetChild(0).GetChild(0).GetComponent<MeshRenderer>().material = empoweringMat;
      this.AddLanguageTokens();
      this.SetupBuff();
      this.SetupEquipment();
      this.SetupElite();
      this.AddContent();
      EliteRamp.AddRamp(AffixEmpoweringElite, eliteRamp);
      ContentAddition.AddEquipmentDef(AffixEmpoweringEquipment);
      On.RoR2.CharacterBody.OnBuffFirstStackGained += CharacterBody_OnBuffFirstStackGained;
      On.RoR2.CharacterBody.OnBuffFinalStackLost += CharacterBody_OnBuffFinalStackLost;
      On.RoR2.CombatDirector.Init += CombatDirector_Init;
    }

    private void CombatDirector_Init(On.RoR2.CombatDirector.orig_Init orig)
    {
      orig();
      if (EliteAPI.VanillaEliteTiers.Length > 2)
      {
        // HONOR
        CombatDirector.EliteTierDef targetTier = EliteAPI.VanillaEliteTiers[2];
        List<EliteDef> elites = targetTier.eliteTypes.ToList();
        AffixEmpoweringElite.healthBoostCoefficient = 2.5f;
        AffixEmpoweringElite.damageBoostCoefficient = 1.5f;
        elites.Add(AffixEmpoweringElite);
        targetTier.eliteTypes = elites.ToArray();
      }
      if (EliteAPI.VanillaEliteTiers.Length > 1)
      {
        CombatDirector.EliteTierDef targetTier = EliteAPI.VanillaEliteTiers[1];
        List<EliteDef> elites = targetTier.eliteTypes.ToList();
        AffixEmpoweringElite.healthBoostCoefficient = 4f;
        AffixEmpoweringElite.damageBoostCoefficient = 2f;
        elites.Add(AffixEmpoweringElite);
        targetTier.eliteTypes = elites.ToArray();
      }
    }

    private void CharacterBody_OnBuffFirstStackGained(
       On.RoR2.CharacterBody.orig_OnBuffFirstStackGained orig,
       CharacterBody self,
       BuffDef buffDef
       )
    {
      orig(self, buffDef);
      if (buffDef == AffixEmpoweringBuff)
      {
        GameObject gameObject = Object.Instantiate<GameObject>(EmpoweringWard);
        BuffWard component = gameObject.GetComponent<BuffWard>();
        gameObject.GetComponent<TeamFilter>().teamIndex = self.teamComponent.teamIndex;
        component.buffDef = RoR2Content.Buffs.Warbanner;
        component.Networkradius = 25f + self.radius;
        gameObject.GetComponent<NetworkedBodyAttachment>().AttachToGameObjectAndSpawn(self.gameObject);
      }
    }

    private void CharacterBody_OnBuffFinalStackLost(
  On.RoR2.CharacterBody.orig_OnBuffFinalStackLost orig,
  CharacterBody self, BuffDef buffDef)
    {
      orig(self, buffDef);
      if (buffDef == AffixEmpoweringBuff)
      {
        BuffWard buffWard = self.gameObject.GetComponentInChildren<BuffWard>();
        Object.Destroy(buffWard);
      }
    }

    private void AddContent()
    {
      ItemDisplays itemDisplays = new ItemDisplays();
      ContentAddition.AddEliteDef(AffixEmpoweringElite);
      ContentAddition.AddBuffDef(AffixEmpoweringBuff);
    }

    private void SetupBuff()
    {
      AffixEmpoweringBuff = ScriptableObject.CreateInstance<BuffDef>();
      AffixEmpoweringBuff.name = "EliteEmpoweringBuff";
      AffixEmpoweringBuff.canStack = false;
      AffixEmpoweringBuff.isCooldown = false;
      AffixEmpoweringBuff.isDebuff = false;
      AffixEmpoweringBuff.buffColor = AffixEmpoweringColor;
      AffixEmpoweringBuff.iconSprite = eliteIcon;
    }

    private void SetupEquipment()
    {
      AffixEmpoweringEquipment = ScriptableObject.CreateInstance<EquipmentDef>();
      AffixEmpoweringEquipment.appearsInMultiPlayer = true;
      AffixEmpoweringEquipment.appearsInSinglePlayer = true;
      AffixEmpoweringEquipment.canBeRandomlyTriggered = false;
      AffixEmpoweringEquipment.canDrop = false;
      AffixEmpoweringEquipment.colorIndex = ColorCatalog.ColorIndex.Equipment;
      AffixEmpoweringEquipment.cooldown = 0.0f;
      AffixEmpoweringEquipment.isLunar = false;
      AffixEmpoweringEquipment.isBoss = false;
      AffixEmpoweringEquipment.passiveBuffDef = AffixEmpoweringBuff;
      AffixEmpoweringEquipment.dropOnDeathChance = affixDropChance;
      AffixEmpoweringEquipment.enigmaCompatible = false;
      AffixEmpoweringEquipment.pickupModelPrefab = PrefabAPI.InstantiateClone(Addressables.LoadAssetAsync<GameObject>("RoR2/Base/EliteFire/PickupEliteFire.prefab").WaitForCompletion(), "PickupAffixEmpowering", false);
      foreach (Renderer componentsInChild in AffixEmpoweringEquipment.pickupModelPrefab.GetComponentsInChildren<Renderer>())
        componentsInChild.material = empoweringMat;
      AffixEmpoweringEquipment.nameToken = "EQUIPMENT_AFFIX_EMPOWERING_NAME";
      AffixEmpoweringEquipment.descriptionToken = "EQUIPMENT_AFFIX_EMPOWERING_DESC";
      AffixEmpoweringEquipment.pickupToken = "EQUIPMENT_AFFIX_EMPOWERING_PICKUP";
      AffixEmpoweringEquipment.loreToken = "EQUIPMENT_AFFIX_EMPOWERING_LORE";
      AffixEmpoweringEquipment.name = "AffixEmpowering";
    }

    private void SetupElite()
    {
      AffixEmpoweringElite = ScriptableObject.CreateInstance<EliteDef>();
      AffixEmpoweringElite.color = AffixEmpoweringColor;
      AffixEmpoweringElite.eliteEquipmentDef = AffixEmpoweringEquipment;
      AffixEmpoweringElite.modifierToken = "ELITE_MODIFIER_EMPOWERING";
      AffixEmpoweringElite.name = "EliteEmpowering";
      AffixEmpoweringElite.healthBoostCoefficient = healthMult;
      AffixEmpoweringElite.damageBoostCoefficient = damageMult;
      AffixEmpoweringBuff.eliteDef = AffixEmpoweringElite;
    }

    private void AddLanguageTokens()
    {
      LanguageAPI.Add("ELITE_MODIFIER_EMPOWERING", "Empowering {0}");
      LanguageAPI.Add("EQUIPMENT_AFFIX_EMPOWERING_NAME", "Empowering Aspect");
      LanguageAPI.Add("EQUIPMENT_AFFIX_EMPOWERING_PICKUP", "Become an aspect of Empowering");
      LanguageAPI.Add("EQUIPMENT_AFFIX_EMPOWERING_DESC", "Become an aspect of Empowering");
      LanguageAPI.Add("EQUIPMENT_AFFIX_EMPOWERING_LORE", "Empowering Aspect Lore");
    }
  }
}
```
# Cookbook

Welcome to the Risk of Rain 2 modding cookbook, explore modding best practices with common code snippets. 

==Refer to the Table of Contents to the right of the page to jump to a specific snippet.==

## Loading Assets
After the Memory Optimization Update, modders now have an opt-in system to keep load times fast and keep your mod's memory usage in check. To use the `GameAssetPaths` you'll need a reference to the `RoR2BepInExPack` package in your `.csproj`. [Link to the latest version in the modding discord](https://discord.com/channels/562704639141740588/562704639569428506/1303101282894090240).
```
// Example for how to properly load in assets to be used later. It's divided up for better readability, but you can put it all in one-line.
public Material moonMat;
AssetReferenceT<Material> moonMatRef = new AssetReferenceT<Material>(RoR2BepInExPack.GameAssetPaths.RoR2_Base_moon.matMoonTerrain_mat);
AssetAsyncReferenceManager<Material>.LoadAsset(moonMatRef).Completed += (x) => moonMat = x.Result;
```

```
// Example for how to properly load in an asset then edit it. You can save it to a variable before/after editing inside of the .Completed.
AssetReferenceT<Material> starPickupRef = new AssetReferenceT<Material>(RoR2BepInExPack.GameAssetPaths.RoR2_DLC2_Items_LowerPricedChests.PickupSaleStar_prefab);
AssetAsyncReferenceManager<GameObject>.LoadAsset(starPickupRef).Completed += (x) =>
{
  MeshCollider collider = obj.Result.transform.find("SaleStar")?.GetComponent<MeshCollider>();
  if (collider)
  {
    collider.convex = true;
  }
};
```

## Creating Skills
There are multiple parts of a skill, the actual definition (SkillDef), the SkillFamily it belongs to (Primary/Secondary/Utility/Special), the SkillVariant within the family (if you have multiple SkillDefs under a family), and the GenericSkill which holds it all together. Usually the GenericSkill and SkillFamily already exists (if you're adding a skill to an existing body) so you'd need to create the SkillDef and SkillVariant.
```
  private void CreateSkill(GameObject body)
    {
      // Creating the SkillDef
      SkillDef skillDef = ScriptableObject.CreateInstance<SkillDef>();
      skillDef.skillName = "SKILL_NAME_TOKEN";
      (skillDef as ScriptableObject).name = "SKILL_NAME_TOKEN";

      skillDef.activationState = new SerializableEntityStateType(typeof(YourEntityState));
      skillDef.activationStateMachineName = "Body"; // This is usually either "Body" or "Weapon" depending on your skill
      skillDef.interruptPriority = InterruptPriority.Skill; // If the priority is the same or "higher" it can interrupt this skill

      skillDef.baseMaxStock = 1;
      skillDef.baseRechargeInterval = 4f;

      skillDef.rechargeStock = 1;
      skillDef.requiredStock = 1;
      skillDef.stockToConsume = 1;

      skillDef.dontAllowPastMaxStocks = true;
      skillDef.beginSkillCooldownOnSkillEnd = false;
      skillDef.canceledFromSprinting = false;
      skillDef.forceSprintDuringState = true;
      skillDef.fullRestockOnAssign = true;
      skillDef.resetCooldownTimerOnUse = true;
      skillDef.isCombatSkill = false;
      skillDef.mustKeyPress = false; // This can stay false
      skillDef.cancelSprintingOnActivation = false;
      
      // Creating a SkillFamily
      SkillFamily newFamily = ScriptableObject.CreateInstance<SkillFamily>();
      (newFamily as ScriptableObject).name = body.name + "Secondary" + "Family";
      ;
      newFamily.variants = new SkillFamily.Variant[1] { new SkillFamily.Variant { skillDef = skillDef } };

      // Creating a GenericSkill
      GenericSkill skill = body.AddComponent<GenericSkill>();
      skill._skillFamily = newFamily;
      body.GetComponent<SkillLocator>().secondary = skill;

      // Adding your new SkillDef and SkillFamily with R2API.ContentManagement
      ContentAddition.AddSkillFamily(newFamily);
      ContentAddition.AddSkillDef(skillDef);
    }
```

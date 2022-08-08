# Character

Here is a link to rob's character template. This is the recommended method of adding a custom survivor as it is the most up to date and involves the least amount of jank.
https://github.com/ArcPh1r3/HenryTutorial

While HenryTutorial is very complete, there also exists ThunderHenry, which allows you to create most of the Character using Thunderkit and it's tools.
https://github.com/Vale-X/Thunderkit-Henry

If you are newer to modding, then I recommend that you start out just working on the skills before you work out anything relating to custom models or animations. Doesn't matter if your character is a commando clone, so long as they're fun to play. To this end you should check out the custom skills page on the wiki since this will give you a working template that's a little more simple to understand than the full character template. It's a good place to start before you move on to a fully fledged character mod. Additionally, if you have all of your skill states in their own .cs files, then when it's time to switch templates it will be as easy as dragging the files from one project to the other.

If you are interested in the difference between rob's template and matarra's project, the biggest difference is how they get the model in game. Matarra uses a "model swap." This means making your character a commando clone, making the commando model invisible, and then displaying your custom model on top of it. While this method is relatively simple, it makes a lot of things more difficult (animations, skins, item displays) since your model will only be there for show. Rob's template actually replaces the model.

Another thing to note, rob uses a different method of asset bundle embedding than is used elsewhere on the wiki. If you are unfamiliar with loading from stream, then feel free to replace the entirety of the assets static class in robs template with the method that's on the wiki (just make sure to use the same variable names).

---

---

**Old Page**
Until someone add detailed explanation and examples, here is a link to an unfinished but already a very good concept on how to make a fully working custom character integrated into the game with a quick overview.

https://github.com/MatarraNA/PlayableSephiroth
The Github project can be split into 3 parts :

-   C# Code Solution - BepinEx Plugin where all the logic happens to integrate all the character parts into the game.
-   Unity Project with an assetbundle exporter (so that the BepinEx Plugin loads the character into the game) which contains : Character Model / Animations / SFX.
-   WWise Project for sounds.

---

---

**Even Older page :**
This page contains current knowledge on how custom characters can be added to the game, including assets and code.

Page needs to be filled in at some point...!

```csharp
using BepInEx;
using R2API;
using RoR2;
using System;
using System.Reflection;
using UnityEngine;

namespace name
{
    [R2APISubmoduleDependency("SurvivorAPI")]
    [BepInDependency("com.bepis.r2api", BepInDependency.DependencyFlags.HardDependency)]
    [BepInPlugin("com.new.skill", "newskill", "1.0.0")]
    public class BanditMod : BaseUnityPlugin
    {
        public GameObject myCharacter;

        public void Awake()
        {
            myCharacter = Resources.Load<GameObject>("Prefabs/CharacterBodies/ScavBody").InstantiateClone("Prefabs/CharacterBodies/ScavPlayerBody");
               //Note; if your character cannot interact with things, play around with the following value after uncommenting them
              //gameObject.transform.localScale *= 0.25f;
              //myCharacter.GetComponent<CharacterBody>().aimOriginTransform.Translate(new Vector3(0, -6, 0));
              //myCharacter.GetComponentInChildren<KinematicCharacterController.KinematicCharacterMotor>().SetCapsuleDimensions(behaviour.Capsule.radius * 0.25f, behaviour.Capsule.height * 0.25f, -3f);

BodyCatalog.getAdditionalEntries += delegate (List<GameObject> list)
                {
                    list.Add(myCharacter);
                };

CharacterBody component = myCharacter.GetComponent<CharacterBody>();
                component.baseDamage = 10f;
                component.baseCrit = 10f;
                component.levelCrit = 0.75f;
                component.baseMaxHealth = 400f;
                component.levelMaxHealth = 40f;
                component.baseArmor = 20f;
                component.baseRegen = 2f;
                component.levelRegen = 0.2f;
                component.baseMoveSpeed = 8f;
                component.levelMoveSpeed = 0.25f;
                component.baseAttackSpeed = 1f;
                component.name = "MyCharacterName";

  myCharacter.GetComponent<CharacterBody>().preferredPodPrefab = Resources.Load<GameObject>("Prefabs/CharacterBodies/toolbotbody").GetComponent<CharacterBody>().preferredPodPrefab;

                SurvivorDef survivorDef = new SurvivorDef
                {
                    bodyPrefab = myCharacter,
                    descriptionToken = "MyDescription",
                    displayPrefab = gameObject,
                    primaryColor = new Color(0.8039216f, 0.482352942f, 0.843137264f),
                    name = "MyCharacterName",
                    unlockableName = "Logs.Stages.limbo"
                };
                SurvivorAPI.AddSurvivor(survivorDef);
        }
    }
}
```

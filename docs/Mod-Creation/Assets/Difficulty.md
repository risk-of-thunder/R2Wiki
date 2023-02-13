# A beginners guide to making Risk of Rain 2 modded difficulties.
![image](https://user-images.githubusercontent.com/68250081/201125347-7c9adc79-fc02-4760-af55-611aa070e8c5.png)

We're going to be starting out with this nearly empty project. Make sure you already know the modding **basics** for this tutorial.

A difficulty needs a **DifficultyIndex** and a **DifficultyDef** to function. We add those to our class and make them **public** for other mods to access easier.
```csharp
        public static DifficultyDef ExampleModDiffDef;

        public static DifficultyIndex ExampleModDiffIndex;
```

After that, we can add our difficulty to the game, in a somewhat playable state:
```csharp
        public void AddDifficulty()
        {
            ExampleModDiffDef = new(3f,
                                   "EXAMPLEDIFFICULTYMOD_NAME",
                                   "EXAMPLEDIFFICULTYMOD_ICON",
                                   "EXAMPLEDIFFICULTYMOD_DESCRIPTION",
                                   new Color32(255, 255, 255, 255),
                                   "ed",
                                   true);

            ExampleModDiffDef.iconSprite = null;
            ExampleModDiffDef.foundIconSprite = true;
            ExampleModDiffIndex = DifficultyAPI.AddDifficulty(ExampleModDiffDef);
        }
```
* The first value passed is our **difficulty scaling value** - 3f is the same as Monsoon scaling, also known as 150% or +50% of Rainstorm. This is linear, so 6f is the expected 300% difficulty scaling.
* * In addition, [**here's**](https://docs.google.com/spreadsheets/d/1MsFV3lkA0N5g7mdNmA1cXHC18zCt_NwFWogUk_OxtnE/edit?usp=sharing) a reference as to what you could expect with X difficulty scaling. Feel free to make a copy and experiment.
* * Difficulties in the lobby are sorted by this value.
* The next three values are our **language tokens**, that we need to fill in order to have a proper name and description.
* The color indicates the color when hovering over our difficulty, made it RGB here for simplicity sake.
* "ed" is what is displayed in the lobby listing if people are playing on your difficulty.
* And finally, the last bool indicates whether the difficulty can unlock Monsoon specific achievements and whether it has Monsoon's -40% health regeneration modifier.

We're leaving the difficulty icon sprite null for now.


_Found icon sprite always true, don't ask me why - I don't know._


Now to fill the tokens.

```csharp
        public void FillTokens()
        {
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_NAME", "Example Difficulty");
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_DESCRIPTION", "My example difficulty description.\n\n" +
                                                                "<style=cStack>>Player Health Regeneration: <style=cIsHealth>-40%</style>\n" +
                                                                ">Difficulty Scaling: <style=cIsHealth>+50%</style></style>");
        }
```
Not really much to talk here, we supply `LanguageAPI` with our token, then set its value.

Our new code should look like this:
```csharp
using BepInEx;
using BepInEx.Logging;
using R2API;
using R2API.Utils;
using RoR2;
using UnityEngine;

namespace ExampleDifficultyMod
{
    [BepInDependency(R2API.R2API.PluginGUID)]
    [BepInDependency(LanguageAPI.PluginGUID)]
    [BepInDependency(DifficultyAPI.PluginGUID)]
    [BepInPlugin(PluginGUID, PluginName, PluginVersion)]
    public class Main : BaseUnityPlugin
    {
        public const string PluginGUID = PluginAuthor + "." + PluginName;

        public const string PluginAuthor = "ExampleModAuthor";
        public const string PluginName = "ExampleDifficultyMod";
        public const string PluginVersion = "1.0.0";

        public static ManualLogSource EDMLogger;

        public static DifficultyDef ExampleModDiffDef;

        public static DifficultyIndex ExampleModDiffIndex;

        public void Awake()
        {
            EDMLogger = Logger;
            AddDifficulty();
            FillTokens();
        }

        public void FillTokens()
        {
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_NAME", "Example Difficulty");
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_DESCRIPTION", "My example difficulty description.\n\n" +
                                                                "<style=cStack>>Player Health Regeneration: <style=cIsHealth>-40%</style>\n" +
                                                                ">Difficulty Scaling: <style=cIsHealth+50%</style></style>");
        }

        public void AddDifficulty()
        {
            ExampleModDiffDef = new(3f, "EXAMPLEDIFFICULTYMOD_NAME", "EXAMPLEDIFFICULTYMOD_ICON", "EXAMPLEDIFFICULTYMOD_DESCRIPTION", new Color32(255, 255, 255, 255), "ed", true);
            ExampleModDiffDef.iconSprite = null;
            ExampleModDiffDef.foundIconSprite = true;
            ExampleModDiffIndex = DifficultyAPI.AddDifficulty(ExampleModDiffDef);
        }
    }
}
```

At this point, our difficulty should appear in-game, much like this:
![image](https://user-images.githubusercontent.com/68250081/201138608-9638be88-bb05-42a0-b0ae-52b2f1d82b98.png)


## But what if we want our Scaling to be configurable and update in-game?
This is actually pretty simple, but for adding an **in-game mod settings menu**, it's a little more complicated. I will now go over both.

First of all, we make a ConfigEntry in our class,
```csharp
public static ConfigEntry<float> Scaling { get; set; }
```
then Bind it in `Awake()`.
```csharp
Scaling = Config.Bind("First Section", "Difficulty Scaling", 150f, "Percentage of difficulty scaling, 150% is +50%. Default is 150");
```

Now, we have to go back to our `AddDifficulty()` method, and replace the 3f with this below, in order for our difficulty to update when restarting the game.
```csharp
ExampleModDiffDef = new(Scaling.Value / 50f, // ...
```

As for realtime updating...

In `Awake()`, we hook RoR2.Language.GetLocalizedStringByToken:
```csharp
On.RoR2.Language.GetLocalizedStringByToken += // Hit tab here, should autocomplete.
```

In our hook method, we set our `DifficultyDef's scalingValue` again, to the same value in `AddDifficulty()`.
We check if the token equals our description token and return our actual description, the same exact one as our `LanguageAPI's` additions.
Of course, we replace the shown difficulty scaling in both of our descriptions as well.

**Don't forget to call orig!**

Our code should now look like this:
```csharp
// ...
        public static ConfigEntry<float> Scaling { get; set; }

        public void Awake()
        {
            EDMLogger = Logger;
            Scaling = Config.Bind("General", "Difficulty Scaling", 150f, "Percentage of difficulty scaling, 150% is +50%. Difficulty order does not update visually. Vanilla is 150");
            AddDifficulty();
            FillTokens();
            On.RoR2.Language.GetLocalizedStringByToken += Language_GetLocalizedStringByToken;
        }

        private string Language_GetLocalizedStringByToken(On.RoR2.Language.orig_GetLocalizedStringByToken orig, Language self, string token)
        {
            ExampleModDiffDef.scalingValue = Scaling.Value / 50f;
            if (token == "EXAMPLEDIFFICULTYMOD_DESCRIPTION")
            {
                return "My example difficulty description.\n\n" +
                       "<style=cStack>>Player Health Regeneration: <style=cIsHealth>-40%</style>\n" +
                       ">Difficulty Scaling: <style=cIsHealth>+" + (Scaling.Value - 100f) + "%</style></style>";
            }
            return orig(self, token);
        }

        public void FillTokens()
        {
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_NAME", "Example Difficulty");
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_DESCRIPTION", "My example difficulty description.\n\n" +
                                                                "<style=cStack>>Player Health Regeneration: <style=cIsHealth>-40%</style>\n" +
                                                                ">Difficulty Scaling: <style=cIsHealth>+" + (Scaling.Value - 100f) + "%</style></style>");
        }

        public void AddDifficulty()
        {
            ExampleModDiffDef = new(Scaling.Value / 50f, "EXAMPLEDIFFICULTYMOD_NAME", "EXAMPLEDIFFICULTYMOD_ICON", "EXAMPLEDIFFICULTYMOD_DESCRIPTION", new Color32(255, 255, 255, 255), "ed", true);
            ExampleModDiffDef.iconSprite = null;
            ExampleModDiffDef.foundIconSprite = true;
            ExampleModDiffIndex = DifficultyAPI.AddDifficulty(ExampleModDiffDef);
        }
// ...
```

And we're just missing our in-game mod settings menu now. Head on over to [**this site**](https://thunderstore.io/package/Rune580/Risk_Of_Options/) and download it. Now extract it somewhere, and add the .dll file as a Project Reference like so:

![image](https://user-images.githubusercontent.com/68250081/201145133-ba47d89f-1f4d-4092-8245-7201387e6612.png)

Add our imports:
```csharp
using RiskOfOptions;
using RiskOfOptions.Options;
using RiskOfOptions.OptionConfigs;
```

Add our dependency (should be above your class):
```csharp
[BepInDependency("com.rune580.riskofoptions")]
```

Add a StepSlider in our `Awake()` like so:
```csharp
ModSettingsManager.AddOption(new StepSliderOption(Scaling, new StepSliderConfig() { increment = 25f, min = 0f, max = 300f }));
```

The mod page has a **guide**, so I highly recommend reading that if you're confused.

When running the mod now, make sure to have `Risk of Options` installed. In your settings menu, there should be a new tab called Mod Settings, where your mod's Scaling config is displayed.


## How do I change the ambient level cap?


Use the event `Run.onRunSetRuleBookGlobal`:
```csharp
Run.onRunSetRuleBookGlobal += // *tab*
```

In it, we perform a simple ternary or if else statement:
```csharp
Run.ambientLevelCap = (arg1.selectedDifficulty == ExampleModDiffIndex) ? 1234 : Run.ambientLevelCap;
```
As you can see, we don't actually need the second overload. You can rename the overloads however you want, the **1234** here is our desired cap.


## Hmm, how can I change monster stats or give them items?


Now this is where other modders would argue, but I'm gonna be showing my way of doing it. And no, I'm not sure if this is the best or most optimized way, so please bear with me.

We can use the run events to add and remove our hooks, depending on the difficulty.
Declare a bool in your Class like so:
```csharp
        public static bool shouldRun = false;
```

Then, write a base in `Awake()` like this:
```csharp
Run.onRunStartGlobal += (Run run) =>
            {
                shouldRun = false;
                if (run.selectedDifficulty == ExampleModDiffIndex)
                {
                    shouldRun = true;
                    /* Here is where our logic should run.
                    This is nice, because even On.Hooks work greatly and there's not much hassle after setting up this base.
                    It's worth to note that some things don't properly unhook, leaving changes after playing the difficulty and switching off of it.
                    However, you can run a cleanup hook in destroy that resets the values to vanilla (though you will have to know them, either by logging or a unity rip of the game.
                    You also remove the cleanup hooks here with -=.
                    */
                }
            };
            Run.onRunDestroyGlobal += (Run run) =>
            {
                shouldRun = false;
                // Here is where we can remove our hooks with -= for example.
                // We can also run our cleanup hooks here, with +=.
            };
```
This makes all of your code only run at the start of a run, when your difficulty is selected.
After ending a run, it always removes your modifiers, no matter the difficulty selected.


The performance impact should be **small**, unless you're doing something incorrectly. At most, every hook **increases load times** slightly after hitting the "Ready" button.


There's a couple more useful events we can use right now, let's go over them.


`CharacterMaster.onStartGlobal` is useful for changing AI and giving items.


`CharacterBody.onBodyAwakeGlobal` is useful for changing stats in a more "precise" way - potentially per body.

For this tutorial, we're going to be using both + a hook, to give enemies items, change AI, a skill and their stats.

We use our event in our base like so:
```csharp
if (run.selectedDifficulty == ExampleModDiffIndex)
                {
                    shouldRun = true;
                    CharacterMaster.onStartGlobal += // *tab*
                }
```


And in our newly created method, we check if the `CharacterMaster's` teamindex, to see whether it's a player or not.
If it isn't, we give them a Cautious Slug.


```csharp
        private void CharacterMaster_onStartGlobal(CharacterMaster obj)
        {
            if (obj.teamIndex != TeamIndex.Player && obj.inventory)
            {
                obj.inventory.GiveItem(RoR2Content.Items.HealWhileSafe, 1);
            }
        }
```

It is important to **null check** things (obj.inventory here), as not every master has an inventory for example. If you try to add a Cautious Slug to a Pot, the game will likely break.

[**Here**](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Developer-Reference/Items-and-Equipments-Data/) is a quick reference to internal item names used in Risk of Rain 2.


### For the AI change...


...let's say I want to make enemies more accurate with their aim and want to make Lemurians shoot from farther away. I'm also gonna make the change exclusive to enemies.

```csharp
        private void CharacterMaster_onStartGlobal(CharacterMaster obj)
        {
            if (obj.teamIndex != TeamIndex.Player)
            {
                if (obj.inventory) obj.inventory.GiveItem(RoR2Content.Items.HealWhileSafe, 1);

                if (obj.GetComponent<BaseAI>() != null)
                {
                    var ba = obj.GetComponent<BaseAI>();
                    ba.aimVectorDampTime = 0.03f; // lower value is better aim
                    ba.aimVectorMaxSpeed = 250f; // higher value is better aim
                }
            }
        }
```


### Now to get a bit more in-depth...


...I'd recommending knowing how `AISkillDrivers` work. This is what we'll be using to change how far away a Lemurian shoots from. It is the way I do it, again it might not be perfect.

The code below is still in our team check.
```csharp
                switch (obj.name)
                {
                    case "LemurianMaster(Clone)":
                        AISkillDriver LemurianShoot = (from x in obj.GetComponents<AISkillDriver>()
                                                       where x.customName == "StrafeAndShoot"
                                                       select x).First();
                        LemurianShoot.minDistance = 10f;
                        LemurianShoot.maxDistance = 100f;

                        AISkillDriver LemurianStrafe = (from x in obj.GetComponents<AISkillDriver>()
                                                        where x.customName == "StrafeIdley"
                                                        select x).First();

                        LemurianStrafe.minDistance = 10f;
                        LemurianStrafe.maxDistance = 100f;
                        break;
                }
```
This code searches in the `Scene Hierarchy` for a given Object name, in this case it's "LemurianMaster(Clone)". You can see the Hierarchy by getting mods such as [**this one**](https://thunderstore.io/package/Twiner/RuntimeInspector/) or [**this one**](https://thunderstore.io/package/sinai-dev/UnityExplorer/).

After that, it iterates through every `AISkillDriver` on the Master, and finds one with a specific name.
These make them shoot from a minimum distance of 10m, and a maximum of 100m.


In general, I recommend using switches for **more than 2** if else cases and **caching** values. It can make the difference between a lag spike every monster spawn and smooth gameplay.


### The skill hook...
...is also gonna be smooth sailing, thanks to our base. Remember to do everything in our base, under our DifficultyIndex.

Say I want to change how many times a second a Lemurian shoots its fireball, I need to lower its fireball charging duration.
```csharp
On.EntityStates.LemurianMonster.ChargeFireball.OnEnter += // *tab*
```

Generates us a method, in which we can change its duration like so:
```csharp
        private void ChargeFireball_OnEnter(On.EntityStates.LemurianMonster.ChargeFireball.orig_OnEnter orig, EntityStates.LemurianMonster.ChargeFireball self)
        {
            EntityStates.LemurianMonster.ChargeFireball.baseDuration = 0.9f;
            orig(self);
        }
```

Now this is a bit deceptive. DnSpy clearly shows the baseDuration as 1, yet it's slower than it used to be...
What you can do is Log it in the method to get the actual value like so:
```csharp
Logger.LogWarning("ChargeFireball Base Duration is " + EntityStates.LemurianMonster.ChargeFireball.baseDuration);
```
**Don't forget to call orig!**

Oh nice, now it shows 0.6 (seconds) in the **BepInEx console** every time a lemurian charges its fireball.


In the case that
```csharp
EntityStates.SomeMonster.SomeState.someProperty
```
doesn't exist, also try doing this:
```csharp
self.someProperty
```


It's important to note that not every field is readily accessible. I would recommend joining the **Risk of Rain 2 Modding discord** and using the `!publicize` command in the `#bot-chat` channel to know how to bypass this.


### And for the last part, the stats.


We use the event that I provided earlier to edit individual stats.
Let's say I want to make lemurians and beetles extremely fast.
```csharp
CharacterBody.onBodyAwakeGlobal += // *tab*
```

This is done in a similar way to our AI/Master changes, where we can utilize a switch and check for the Object name:
```csharp
        private void CharacterBody_onBodyAwakeGlobal(CharacterBody cb)
        {
            switch (cb.name)
            {
                case "LemurianBody(Clone)":
                    cb.baseMoveSpeed = 25f;
                    break;

                case "BeetleBody(Clone)":
                    cb.baseMoveSpeed = 40f;
                    break;
            }
        }
```

We can also make monsters gain additional stats with levels this way, as the `CharacterBody` has fields such as **levelMoveSpeed** (aside from health and damage, which we'd just be overwriting).
This scales with the Ambient Level, the formula is:


`Total added stat = levelStat * (Ambient Level - 1)`


For the second way, we can make particular types of enemies (such as elites, bosses, etc) gain stats, but they aren't as "precise".


Again in our base, we want to use the `RecalculateStatsAPI` event:
```csharp
RecalculateStatsAPI.GetStatCoefficients += // *tab*
```

**Remember to add `RecalculateStatsAPI` as a BepInDependency!**

In our generated method we check if the body exists, and its team. After that, we can start customizing, for example like so:
```csharp
        private void RecalculateStatsAPI_GetStatCoefficients(CharacterBody sender, RecalculateStatsAPI.StatHookEventArgs args)
        {
            if (sender && sender.teamComponent.teamIndex != TeamIndex.Player)
            {
                if (sender.isElite)
                {
                    args.moveSpeedMultAdd += 1f;
                }
                else
                {
                    args.moveSpeedMultAdd += 0.25f;
                }
            }
        }
```

This gives **+100% movement speed** to elite enemies and **+25%** to every other enemy that isn't an elite.

Don't forget to remove our hooks in `Run.onRunDestroyGlobal`.


Our _final final_ code should look like this:
```csharp
using BepInEx;
using BepInEx.Configuration;
using BepInEx.Logging;
using R2API;
using R2API.Utils;
using RoR2;
using UnityEngine;
using RiskOfOptions;
using RiskOfOptions.Options;
using RiskOfOptions.OptionConfigs;
using RoR2.CharacterAI;
using System.Linq;

namespace ExampleDifficultyMod
{
    [BepInDependency(R2API.R2API.PluginGUID)]
    [BepInDependency("com.rune580.riskofoptions")]
    [BepInDependency(LanguageAPI.PluginGUID)]
    [BepInDependency(DifficultyAPI.PluginGUID)]
    [BepInDependency(RecalculateStatsAPI.PluginGUID)]
    [BepInPlugin(PluginGUID, PluginName, PluginVersion)]
    public class Main : BaseUnityPlugin
    {
        public const string PluginGUID = PluginAuthor + "." + PluginName;

        public const string PluginAuthor = "ExampleModAuthor";
        public const string PluginName = "ExampleDifficultyMod";
        public const string PluginVersion = "1.0.0";

        public static ManualLogSource EDMLogger;

        public static DifficultyDef ExampleModDiffDef;

        public static DifficultyIndex ExampleModDiffIndex;

        public static ConfigEntry<float> Scaling { get; set; }

        public static bool shouldRun = false;

        public void Awake()
        {
            EDMLogger = Logger;
            Scaling = Config.Bind("General", "Difficulty Scaling", 150f, "Percentage of difficulty scaling, 150% is +50%. Difficulty order does not update visually. Vanilla is 150");
            ModSettingsManager.AddOption(new StepSliderOption(Scaling, new StepSliderConfig() { increment = 25f, min = 0f, max = 300f }));
            AddDifficulty();
            FillTokens();
            On.RoR2.Language.GetLocalizedStringByToken += Language_GetLocalizedStringByToken;
            Run.onRunSetRuleBookGlobal += Run_onRunSetRuleBookGlobal;

            Run.onRunStartGlobal += (Run run) =>
            {
                shouldRun = false;
                if (run.selectedDifficulty == ExampleModDiffIndex)
                {
                    shouldRun = true;
                    CharacterMaster.onStartGlobal += CharacterMaster_onStartGlobal;
                    CharacterBody.onBodyAwakeGlobal += CharacterBody_onBodyAwakeGlobal;
                    On.EntityStates.LemurianMonster.ChargeFireball.OnEnter += ChargeFireball_OnEnter;
                    RecalculateStatsAPI.GetStatCoefficients += RecalculateStatsAPI_GetStatCoefficients;
                    /* Here is where our logic should run.
                    This is nice, because even On.Hooks work greatly and there's not much hassle after setting up this base.
                    It's worth to note that some things don't properly unhook, leaving changes after playing the difficulty and switching off of it.
                    However, you can run a cleanup hook in destroy that resets the values to vanilla (though you will have to know them, either by logging or a unity rip of the game.
                    You also remove the cleanup hooks here with -=.
                    */
                }
            };
            Run.onRunDestroyGlobal += (Run run) =>
            {
                shouldRun = false;
                CharacterMaster.onStartGlobal -= CharacterMaster_onStartGlobal;
                CharacterBody.onBodyAwakeGlobal -= CharacterBody_onBodyAwakeGlobal;
                On.EntityStates.LemurianMonster.ChargeFireball.OnEnter -= ChargeFireball_OnEnter;
                RecalculateStatsAPI.GetStatCoefficients -= RecalculateStatsAPI_GetStatCoefficients;
                // Here is where we can remove our hooks with -= for example.
                // We can also run our cleanup hooks here, with +=.
            };
        }

        private void RecalculateStatsAPI_GetStatCoefficients(CharacterBody sender, RecalculateStatsAPI.StatHookEventArgs args)
        {
            if (sender && sender.teamComponent.teamIndex != TeamIndex.Player)
            {
                if (sender.isElite)
                {
                    args.moveSpeedMultAdd += 1f;
                }
                else
                {
                    args.moveSpeedMultAdd += 0.25f;
                }
            }
        }

        private void CharacterBody_onBodyAwakeGlobal(CharacterBody cb)
        {
            switch (obj.name)
            {
                case "LemurianBody(Clone)":
                    cb.baseMoveSpeed = 25f;
                    break;

                case "BeetleBody(Clone)":
                    cb.baseMoveSpeed = 40f;
                    break;
            }
        }

        private void ChargeFireball_OnEnter(On.EntityStates.LemurianMonster.ChargeFireball.orig_OnEnter orig, EntityStates.LemurianMonster.ChargeFireball self)
        {
            EntityStates.LemurianMonster.ChargeFireball.baseDuration = 0.08f;
            orig(self);
        }

        private void CharacterMaster_onStartGlobal(CharacterMaster obj)
        {
            if (obj.teamIndex != TeamIndex.Player)
            {
                if (obj.inventory) obj.inventory.GiveItem(RoR2Content.Items.HealWhileSafe, 1);

                if (obj.GetComponent<BaseAI>() != null)
                {
                    var ba = obj.GetComponent<BaseAI>();
                    ba.aimVectorDampTime = 0.03f; // lower value is better aim
                    ba.aimVectorMaxSpeed = 250f; // higher value is better aim
                }

                switch (obj.name)
                {
                    case "LemurianMaster(Clone)":
                        AISkillDriver LemurianShoot = (from x in obj.GetComponents<AISkillDriver>()
                                                       where x.customName == "StrafeAndShoot"
                                                       select x).First();
                        LemurianShoot.minDistance = 10f;
                        LemurianShoot.maxDistance = 100f;

                        AISkillDriver LemurianStrafe = (from x in obj.GetComponents<AISkillDriver>()
                                                        where x.customName == "StrafeIdley"
                                                        select x).First();

                        LemurianStrafe.minDistance = 10f;
                        LemurianStrafe.maxDistance = 100f;
                        break;
                }
            }
        }

        private void Run_onRunSetRuleBookGlobal(Run arg1, RuleBook arg2)
        {
            Run.ambientLevelCap = (arg1.selectedDifficulty == ExampleModDiffIndex) ? 1234 : Run.ambientLevelCap;
        }

        private string Language_GetLocalizedStringByToken(On.RoR2.Language.orig_GetLocalizedStringByToken orig, Language self, string token)
        {
            ExampleModDiffDef.scalingValue = Scaling.Value / 50f;
            if (token == "EXAMPLEDIFFICULTYMOD_DESCRIPTION")
            {
                return "My example difficulty description.\n\n" +
                       "<style=cStack>>Player Health Regeneration: <style=cIsHealth>-40%</style>\n" +
                       ">Difficulty Scaling: <style=cIsHealth>+" + (Scaling.Value - 100) + "%</style></style>";
            }
            return orig(self, token);
        }

        public void FillTokens()
        {
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_NAME", "Example Difficulty");
            LanguageAPI.Add("EXAMPLEDIFFICULTYMOD_DESCRIPTION", "My example difficulty description.\n\n" +
                                                                "<style=cStack>>Player Health Regeneration: <style=cIsHealth>-40%</style>\n" +
                                                                ">Difficulty Scaling: <style=cIsHealth>+" + (Scaling.Value - 100) + "%</style></style>");
        }

        public void AddDifficulty()
        {
            ExampleModDiffDef = new(Scaling.Value / 50f, "EXAMPLEDIFFICULTYMOD_NAME", "EXAMPLEDIFFICULTYMOD_ICON", "EXAMPLEDIFFICULTYMOD_DESCRIPTION", new Color32(255, 255, 255, 255), "ed", true);
            ExampleModDiffDef.iconSprite = null;
            ExampleModDiffDef.foundIconSprite = true;
            ExampleModDiffIndex = DifficultyAPI.AddDifficulty(ExampleModDiffDef);
        }
    }
}
```

# AssetBundle/Difficulty Icon


I don't have the difficulty outline template unfortunately. [This tutorial's](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Items/) Unity section covers how to get an AssetBundle. However...
* The correct unity version is currently `2019.4.26f1`, so you must install that.
* We will be loading our AssetBundle differently for better performance.


## Loading an AssetBundle externally


We declare an AssetBundle in our Class like so:
```cs
public AssetBundle exampleDifficultyModBundle;
```
After that, we have to load it in our `Awake()`:
```cs
exampleDifficultyModBundle = AssetBundle.LoadFromFile(Assembly.GetExecutingAssembly().Location.Replace("myModName.dll", "myBundleFileName"));
```
Now, make sure your AssetBundle is placed in the same folder as your .dll when launching modded.
You can also set your RiskOfOptions mod icon now, with
```cs
ModSettingsManager.SetModIcon(exampleDifficultyModBundle.LoadAsset<Sprite>("texMyModIcon.png"));
```
Remember to include the extension, and have `texMyModIcon` be set as Sprite in Unity.


# Publishing


While publishing the mod, don't forget to add RiskOfOptions as a dependency in your `manifest.json` and don't forget to include your assetbundle file. It's also nice to provide a screenshot of the lobby with your difficulty modifiers shown, and if it's big enough, an extensive list in your `readme.MD` file.
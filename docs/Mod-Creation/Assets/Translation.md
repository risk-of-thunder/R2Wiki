# Translation

This is supposed to be read sequentially, you shouldn't skip any part.

This will allow you to translate pretty much any mod.

# How to identify which of the methods I need to do

Install the mod in question and run the game.

After the game fully loads, the mod should generate a config.

Edit the config, and check if config values correspond to their written values in game.

For example - Deputy has a config for changing her Primary Skill Damage Coefficient. When you change this, save the config and run your game again,
the description should reflect the changes you've made.

If the mod doesn't have a config file at all, or doesn't have configuration for those sorts of things, you can assume you won't need to make a mod yourself, and can instead use the first method in this tutorial - `Translating mods that don't have .language files, and don't have configurable values`.

You can also inspect the mod's `.dll` file (the how-to is written down below), and see if it uses fields alongside strings when using `LanguageAPI.Add()`.

# Translating mods that don't have .language files, and don't have configurable values

## Setting up

This will require basic programming knowledge.

Make a new file with a `.language` extension.

Copy and paste this template into it:
```json
{
  "LANGUAGE": {
  "TOKEN" : "Description for this token",
  "TOKEN_TWO" : "Description for the second token"
  }
}
```

Replace `LANGUAGE` with one of the following:

`en` for translating into English

`RU` for translating into Russian

`de` for translating into German

`FR` for translating into French

`es-ES` for translating into Spanish

`IT` for translating into Italian

`ja` for translating into Japanese

`ko` for translating into Korean

`pt-BR` for translating into Portuguese

`tr` for translating into Turkish

`zh-CN` for translating into Simplified Chinese

## Finding Tokens

Download [DnSpy](https://github.com/dnSpyEx/dnSpy/releases/).

Go to [Thunderstore](https://thunderstore.io/).

Search for the mod you want to translate.

Manually download it.

Extract it somewhere.

Open its `.dll` file with `DnSpy`.

On the left, you should see the file you opened and small arrows to navigate it. Once you click it a bunch of times, you should see the `.dll's` namespaces (names in yellow), and inside those, classes (names in green). Once you click on a class' name, it will navigate to it, showing the code inside it.

1. If they're not organized

You are looking for where a type of content gets created (items, skills, etc.), and trying to look for strings like `NAME"`, `DESC"` or `DESCRIPTION"` (the single quotation mark is important). The vast majority of tokens are all uppercase. They usually have separators such as `"_"`.
We can use `Ctrl+F` inside `dnSpy` to make this process faster, by searching for those.

2. If they're organized and use Henry's Survivor Template

The majority of survivor mods use Henry's Survivor Template, which is organized well enough to make this process not too painful.
Inside the mod's `.dll` file, there should be a namespace called Modules, and inside that, a class called Tokens.
This should give us all the tokens we need.

3. If they're organized and use an abstract base

A lot of content mods use abstract bases to make it easier, faster and more organized.

We can recognize one by looking at its `.dll` file, and seeing if there is a class with `Base`, `Factory`, etc. in its name.

Once we find it, open that class and check for any strings inside. You want to find a prefix or suffix that the Base uses alongside the Item's token for its name, description, lore, etc. For example, `"ITEM_" + this.ItemLangTokenName + "_NAME"`
We got the prefix and the suffix for translating an item's name. Now we have to find the actual tokens.

To find classes that use a Base, we can quickly check what the class inherits from. It's written at the top of the class.

`RandomlyNamedClass : ItemBase`

We now know that the class inherits from ItemBase, meaning it uses its system for filling out properties and loading.

Inside the same class, we simply find a string that is named something along the lines of `Token`, or the string is fully uppercase, most likely meaning it's a token.

For example
```cs
public override string ItemLangTokenName
{
    get
    {
        return "BLEEDING_WITNESS";
    }
}
```
In the same class, we can most likely also find the description string, e.g.
```cs
public override string ItemPickupDesc
{
    get
    {
       return "Your damage over time effects heal all allies.";
    }
}
```
Keep in mind the prefixes and suffixes while translating.

This example, under this section name is ironic because Sandswept has configs for basically anything you would want out of an item but these tokens don't use configs `¯\_(ツ)_/¯`

## Replacing the token descriptions

Luckily, this is a relatively painless process

From the template up above, we had this piece of code:

`"TOKEN" : "Description for this token",`

simply replace `TOKEN` with a token you found.

You can, of course, add more of these, just make sure you separate them with commas.

The last one shouldn't have a comma.

Example `.language` file contents:
```json
{
  "RU": {
  "ITEM_SANDSWEPT_BLEEDING_WITNESS_DESCRIPTION" : "Ваши периодические эффекты восстанавливают здоровье всех союзников.",
  "ITEM_SANDSWEPT_CEREMONIAL_JAR_DESCRIPTION" : "Соедините врагов при попадании. Соединённые враги получают взрывной урон."
  }
}
```

# Translating mods that do not have .language files, and have configurable values in descriptions

## Setting up

Having the `.dll` file of the mod, you can publicize it - to be able to reference anything from it, otherwise a translation would be much more painful.

Download [NStrip](https://github.com/BepInEx/NStrip/releases).

Open Risk of Rain 2's Steam directory, then navigate to the Risk of Rain 2_Data folder, and then the Managed folder.

There should be a ton of `.dll` files in there.

Extract `NStrip.exe` from the download into this directory.

Copy the mod's `.dll` file here as well.

Open up command prompt and navigate to this same Risk of Rain 2 directory.
Use the following command:

`NStrip.exe -p -cg -cg-exclude-events -remove-readonly -n "FULL PATH TO DLL FILE"`
Replace `FULL PATH TO DLL FILE` with e.g. `"P:\Steam\steamapps\common\Risk of Rain 2\Risk of Rain 2_Data\Managed\Deputy.dll")`, quotation marks are important to include.

This should give you a new `.dll` file called `Deputy-nstrip.dll`.

I recommend putting this new `.dll` file into an organized place.

## Getting and using the template

Download [Visual Studio Community 2022](https://visualstudio.microsoft.com/vs/community/).

Once you're in the installer, make sure to check `.NET desktop development`.

Download [this file](https://www.mediafire.com/file/owkng1osd8ad5ki/TranslationModChangeThis.zip/file).

Extract it in an organized place.

Open up `Project.sln`.

Rename everything that has `ChangeThis` in its name.

## Making the mod you want to translate a hard dependency

We need to make it so our mod loads *after* the mod we want to translate, so that we don't run into any issues. This is done with the `[BepInCompatibility("com.SomeAuthor.SomeModName")]` attribute. At the top of the template file, you should see something similar.

Go back to that `.dll` file you NStripped, and open it with `dnSpy`.

Inside it, look for a `Main` class, usually titled something like `"Main"` or `"Plugin"`.

At the top of said class, you should see a `BepInPlugin` attribute declaration, e.g. `[BepInPlugin("com.Bog.Deputy", "Deputy", "0.3.0")]`
From this, we want the mod's `GUID`, which is the first string in the attribute, in our case it's `"com.Bog.Deputy"`.

Replace your `"com.SomeAuthor.SomeModName"` with that.

At the top of your Visual Studio, click on Project, then Add Project Reference. Click to browse and navigate over to your NStripped `.dll` file. Click OK once it imports and you're good to go for referencing the mod's fields, for descriptions to have config values that update in your translation as well.

Again, we need to find the tokens. After that, there is a bit more work to do.

We basically want to copy and paste the mod's code that uses `LanguageAPI.Add()` into our `public void Awake()`.

This will be ugly, you can correct it if you want. Here's an example.
```cs
string text = "BOG";
string text2 = text + "_DEPUTY_BODY_";
LanguageAPI.Add(text2 + "PRIMARY_SHOOT_DESCRIPTION", "<style=cIsUtility>Agile</style>. Fire mid-range revolvers for " + string.Format("<style=cIsDamage>{0}% damage</style>.", TriggerTap.damageCoefficient * 100f), "RU");
```

The last argument in `LanguageAPI.Add()`, `"RU"`, is the language string, refer to the list mentioned above.

`TriggerTap.damageCoefficient` most likely won't be found, you need to reference the mod's namespace as well. We need to find the namespace that belongs to.

In the mod's `.dll` file, hit `Ctrl+F`, and paste this same name. On the right of the search bar and slightly below, the yellow part of the text should tell you what namespace it belongs to.

So `TriggerTap.damageCoefficient` becomes `Skillstates.Deputy.TriggerTap.damageCoefficient`. This is case-sensitive.

You will have to keep things like `Skillstates.Deputy.TriggerTap.damageCoefficient` so that they appear in the description, and update with everyone's config settings. You can reorder them as you please, in this case the number is formatted as `{0}`. Another example of string formatting:
```cs
return (FragGrenade.Ignite ? "<style=cIsDamage>Ignite</style>. " : "") + "Throw a grenade that explodes for <style=cIsDamage>" + base.d(FragGrenade.Damage) + " damage</style>. Can hold up to 2.";
```
Replace every reference of `this.` and `base.` with the mod's. So this becomes the following:
```cs
return (HIFUCommandoTweaks.Skills.FragGrenade.Ignite ? "<style=cIsDamage>Ignite</style>. " : "") + "Throw a grenade that explodes for <style=cIsDamage>" + HIFUCommandoTweaks.TweakBase.d(HIFUCommandoTweaks.Skills.FragGrenade.Damage) + " damage</style>. Can hold up to 2.";
```

In some cases, when you can't access the method from the namespace, you might need to copy paste an entire method the mod uses to format its strings. In our case, the `d()` method cannot be accessed, because it's inside an abstract base. In the mod's .dll file, you can click on its name to navigate to the method, then simply copy and paste it into our class (so that it's outside `public void Awake()`, but inside `public class Main : BaseUnityPlugin`).

# Translating multiple mods at once
You can, of course, use one large `.language` file, or multiple smaller ones. If you want to translate multiple mods that have configurable values in descriptions, it's a bit more nuanced. You need to NStrip and reference each one in your project. You also want to use a SoftDependency.

Here's an example:
```cs
[BepInDependency("com.Bog.Deputy", BepInDependency.DependencyFlags.SoftDependency)]
```
You can, of course, have multiple of these, for all different mods.

This will make it so that your mod loads *after* each one of these, but doesn't require them to be installed, so that any combination of mods work with your translation mod.

Now, we need to set up code for detecting when a mod is loaded. Generally, I ran into some issues with SoftDependencies, and this is a fool-proof way that I know, but it could be written a bit better most likely.
```cs
public static bool DeputyLoaded = false;
```
Declare this inside your class.

The code for setting that value will be contained in our `public void Awake()`. Here's an example of it:
```cs
DeputyLoaded = BepInEx.Bootstrap.Chainloader.PluginInfos.ContainsKey("com.Bog.Deputy");
```
The string at the end is the same GUID we SoftDepend on.

Now make a new method, and call it inside `Awake()`.
```cs
[MethodImpl(MethodImplOptions.NoInlining | MethodImplOptions.NoOptimization)] // this is very important
public void DeputyTranslation()
{
    if (DeputyLoaded)
    {
        // do stuff, like LanguageAPI.Add(), this will only run if you have Deputy installed
    }
}
```
For multiple mods, you can either have one large method, or several small ones.

# Building the mod

If you needed to use Visual Studio, and couldn't simply use a .language file, you can right click on your solution and hit `Build`, or use the shortcut `Ctrl+Shift+B`.

This will generated a `.dll` file.

Right click your solution and click `Open Folder in File Explorer`.

Go to the folder, `bin`, `Debug`, `netstandard2.0`, and your `.dll` file should appear.

To test it, in your mod manager, go to `Settings`, `Browse profile folder`, and navigate over to `BepInEx`, `plugins`. Copy your `.dll` file into the `plugins` folder, then launch the game. Make sure your language is set correctly and check if the translation works.


# Uploading the mod

Refer to the [Thunderstore Guidelines](https://thunderstore.io/package/create/docs/) for uploading a mod.

The file names there are case sensitive. 

Inside your `manifest.json`, make sure to include `LanguageAPI` in your dependencies.

To know its dependency string, you can navigate over to its [mod page](https://thunderstore.io/package/RiskofThunder/R2API_Language/).

Include any and all `.language` or `.dll` files you have where your icon, manifest, readme, etc are.

It's important to note that you shouldn't, and don't need to include the NStripped or original `.dll` file of the mod you translated.

Then select all of the files and zip them. When you open your zip, those files should appear first. No folder inside, or Thunderstore won't see them. Then simply head on over to the Upload page, select the `.zip` file, fill out other data and submit.

# Updating the mod

In your project, it's important to increment the `PluginVersion` number.

Outside of that, you simply need to increment the version number in your `manifest.json` file, zip everything, and upload again.

# Why not reupload a mod, instead of dealing with all of this?

Reuploading is looked down upon, and will get taken down if you don't have the mod author's permission to do so.
# Localization Yes We Have Two Pages

# Work In Progress!!!
A beginner may start off by using hard-coded strings for text: `item.name = "My First Item"`.
Later on, it may feel necessary to instead use variables to store all the item names in a class file:
```cs
ItemNames.cs
public static string FirstItemName = "My First Item";
...
item.name = ItemNames.FirstItemName;
```
But then, only people who speak English would be able to fully understand the name of the item.

Translating your mod into different languages allows greater access by people who natively speak other languages.

## LanguageAPI (R2API)
LanguageAPI is the main way to add tokens, provided by R2API.

Add LanguageAPI as a SubmoduleDependency.
```cs
[R2APISubmoduleDependency(nameof(LanguageAPI))]
```

One could add simple strings by doing this:
```cs
LanguageAPI.Add("TOKEN", "VALUE");
LanguageAPI.Add("TOKEN", "VALUE", "es-419");
LanguageAPI.Add("TOKEN", "VALUE", "RU");
LanguageAPI.Add("TOKEN", "VALUE", "ja");
```
However, as you get more and more tokens added, it will bulk up a class file where it would be more valuable to instead have LanguageAPI load it from a `.language` file.

Create a `.language` file (essentially a .json file, but R2API will automatically load the .language file), for this example we'll name it `MyMod.language`, and put it next to your mod's `.dll` file. (WIP: DOes this need the AssetPlus dependency?)
Your language file will be formatted as such:
```json
{
  "strings": {
    "TOKEN": "ENGLISH STRING",
  }
}
```
To add other languages, copy the section and put the webapi language code where `strings` would be.
```json
{
  "strings": {
    "TOKEN": "ENGLISH STRING",
  }
  "ja": {
    "TOKEN": "JAPANESE STRING",
  }
}

```
## Other
TO-DO.



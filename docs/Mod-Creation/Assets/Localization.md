# Localization

No code is required for custom tokens, you can use R2API as a dependency of your mod for this.

Download the following template :
[mylangmod.zip](https://github.com/risk-of-thunder/R2Wiki/files/8506166/mylangmod.zip)

- Change README
- Change manifest.json

Rename folder `RENAME_ME_MOD_MAKER_BEFORE_RELEASE_MyLanguageMod` to proper name
Rename .language file to proper name (keep the .language extension ! Otherwise R2API wont load it)

Current format for `.language` file is as follow:

`strings` are for tokens that you want to appear on all languages you didnt explictly wrote tokens for.

You can then specify other languages, in the example below we do this for the french language and english.

Other languages name can be found in the `Risk of Rain 2\Risk of Rain 2_Data\StreamingAssets\Language` folder
```json
{
  "strings": {
    "TOKEN1": "CURRENT LANGUAGE Hustling Fungus",
    "TOKEN_WHATEVER": "TEST VALUE CURRENT LANGUAGE",
  },
  "FR": {
    "TOKEN1": "French Hustling Fungus",
    "TOKEN_WHATEVER": "TEST VALUE FOR FRENCH LANGUAGE",
  },
  "en": {
    "TOKEN1": "English Hustling Fungus"
  }
}
```
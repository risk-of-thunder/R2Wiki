# Localization

No code is required for custom tokens, you can use R2API as a dependency of your mod for this.

Download the following template : [mylangmod.zip](https://github.com/risk-of-thunder/R2Wiki/files/8506166/mylangmod.zip)
* Change README
* Change manifest.json

The folder and the .language file (both named "RENAME_ME_MOD_MAKER_BEFORE_RELEASE_MyLanguageMod") should be renamed to a more fitting name, for example renaming the folder to "lang" and the .language file to "en_lang.language" (make sure the file still has the .language extension so that R2API can load the file). The .language file and its parent category must then be placed in the `bepinex/plugins/{your_mod}` folder for it to be loaded and used.
* Thunderstore example: `Thunderstore Mod Manager\DataFolder\RiskOfRain2\profiles\{Profile}\BepInEx\plugins\{your_mod}`

## .language File Format

`strings` determines the default translation, or the translation that the game falls back on if no other translation is specified. Language identifiers are specified using the first two letters of that languages name (French -> fr | English -> en) and the capitalization does not matter so long as the correct id is used. A list of all supported languages can be found in the `Risk of Rain 2\Risk of Rain 2_Data\StreamingAssets\Language` folder.

Example:
```json
{
  "strings": {
    "TOKEN1": "Hustling Fungus",
    "TOKEN_DESC": "This will provide a default translation to TOKEN_DESC",
  },
  "fr": {
    "TOKEN1": "Champignons qui se bousculent",
    "TOKEN_DESC": "This will provide a French translation to TOKEN_DESC",
  },
  "en": {
    "TOKEN1": "Hustling Fungus",
    "TOKEN_DESC": "This will provide an english translation to TOKEN_DESC",
  }
}
```
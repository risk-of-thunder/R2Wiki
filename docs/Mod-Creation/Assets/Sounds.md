# Sounds

- Setup WWise with that [tutorial](https://github.com/risk-of-thunder/R2Wiki/wiki/Wwise---Custom-Sounds)

- [Get the boilerplate music project for RoR2](https://cdn.discordapp.com/attachments/562704639569428506/885995425264463872/RoR2MusicTest.zip)

- Open it. Go to the SoundBanks tab, rename the soundbank to something unique and not shared by any other mods.

  ![RenameTheBank](https://i.imgur.com/JimLxSy.png)

- Rename the state group named `gameplaySongChoice2`

  ![RenameTheGameplaySongChoice](https://i.imgur.com/1PeFf27.png)

- Rename the 3 events Play_Music_System2, Pause_Music2 and Unpause_Music2 under the default work unit in the Event tab to something unique to your mod and not shared by any other mods.

  ![RenameTheEventNames](https://i.imgur.com/oJj1rvy.png)

- The boilerplate already has 2 songs setup across 3 different states that will be played as : 

  a boss track (in a stage), a gameplay track (in a stage) and the track that plays when in the logbook :

  ![ShowTheSongs](https://i.imgur.com/yEWpK8X.png)

  The .wav files are not shipped in the boilerplate, those will show as red, when importing your own tracks, make sure they are streamed.

  ![MakeSureSongsAreStreamed](https://i.imgur.com/8tqrAdN.png)

- Once you are done setting up your states for your tracks, it's export time.

  Press F7, then Shift+F7 for generating the soundbank for current platform (Windows).

  You can then open the containing folder by right clicking the soundbank name in the tree list.

  You should end up with a folder similar to this

  ![SoundbankGeneratedFolder](https://i.imgur.com/uXKDems.png)

  Gather the state group ids, and the state ids from the Init.txt file for Unity / your plugin code.

  What need to be packaged with your mod are the .bnk (soundbanks) files and the .wem (music) files

  Rename the Init.bnk file to something unique and not shared by another mods, I repeat, the file must be named differently than other Init bank files from the game / other music mods.

  For loading that bank into the game, have a code similar to this by using the R2API submodule SoundAPI

```cs
// Above your BaseUnityPlugin class definition
[BepInDependency(R2API.R2API.PluginGUID)]
[R2APISubmoduleDependency(nameof(SoundAPI))]

...

// Called by your mod Awake()
var d = new SoundAPI.Music.CustomMusicData();
d.BanksFolderPath = System.IO.Path.Combine(Paths.PluginPath, "BankTest");
d.BepInPlugin = Info.Metadata;
d.InitBankName = "Init2";
d.PlayMusicSystemEventName = "Play_Music_System2";
d.SoundBankName = "RoR2MusicTest";

d.SceneDefToTracks = new();

var logBookScene = Resources.Load<SceneDef>("SceneDefs/logbook");

// that following block of code create a track def for a custom music in the logbook s
var mainlogBookTrackDef = ScriptableObject.CreateInstance<SoundAPI.Music.CustomMusicTrackDef>();
mainlogBookTrackDef.cachedName = "customMusic";
mainlogBookTrackDef.SoundBankName = d.SoundBankName;
mainlogBookTrackDef.CustomStates = new();
var cstate1 = new SoundAPI.Music.CustomMusicTrackDef.CustomState();
cstate1.GroupId = 1598298728U; // gathered from the Init bank txt file
cstate1.StateId = 2326263800U; // gathered from the Init bank txt file
mainlogBookTrackDef.CustomStates.Add(cstate1);
var cstate2 = new SoundAPI.Music.CustomMusicTrackDef.CustomState();
cstate2.GroupId = 792781730U; // gathered from the Init bank txt file
cstate2.StateId = 2607556080U; // gathered from the Init bank txt file
mainlogBookTrackDef.CustomStates.Add(cstate2);

d.SceneDefToTracks.Add(logBookScene, new List<SoundAPI.Music.MainAndBossTracks>() { new SoundAPI.Music.MainAndBossTracks(mainlogBookTrackDef, null) });

SoundAPI.Music.Add(d);
```
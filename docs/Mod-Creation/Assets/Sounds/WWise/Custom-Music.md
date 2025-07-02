# Custom Music

-   Setup WWise with that [tutorial](https://www.youtube.com/watch?v=QtfYsdJFty4), note that version 2023.1.4.8496 is the one currently used by the game (DLC2 SOTS Release)

-   [Get the boilerplate music project for RoR2](https://github.com/viliger2/RoR2MusicTest)

- ## WARNING! Do not use boilerplate in released mods. Only use boilerplate as a reference point, always make new Wwise project for each mod. Otherwise collisions will occur and either yours or someone else's mod (who also used boilerplate without making new Wwise project) will not have music.
 
-   Open it. Go to the SoundBanks tab, rename the soundbank to something unique and not shared by any other mods.

    ![RenameTheBank](https://i.imgur.com/JimLxSy.png)

-   Rename the state group named `gameplaySongChoice2`

    ![RenameTheGameplaySongChoice](https://i.imgur.com/1PeFf27.png)

-   Rename the 3 events Play_Music_System2, Pause_Music2 and Unpause_Music2 under the default work unit in the Event tab to something unique to your mod and not shared by any other mods.

    ![RenameTheEventNames](https://i.imgur.com/oJj1rvy.png)

-   Do note the curve of the music volume in Music_System's RTPC, it should be like this (this was given to us by Gearbox). 

![image](https://github.com/user-attachments/assets/807adbca-785f-424f-a8f9-5493634c08b1)


-   The boilerplate already has 2 songs setup across 3 different states that will be played as :

    a boss track (in a stage), a gameplay track (in a stage) and the track that plays when in the logbook :

    ![ShowTheSongs](https://i.imgur.com/yEWpK8X.png)

    The .wav files are not shipped in the boilerplate, those will show as red, when importing your own tracks, make sure they are streamed.

    ![MakeSureSongsAreStreamed](https://i.imgur.com/8tqrAdN.png)

-   Once you are done setting up your states for your tracks, it's export time.

    Press F7, then Shift+F7 for generating the soundbank for current platform (Windows).

    You can then open the containing folder by right clicking the soundbank name in the tree list.

    You should end up with a folder similar to this

    ![SoundbankGeneratedFolder](https://i.imgur.com/uXKDems.png)

    Gather the state group ids, and the state ids from the Init.txt file for Unity / your plugin code.

    What need to be packaged with your mod are the .bnk (soundbanks) files and the .wem (music) files

    Rename the Init.bnk file to something unique and not shared by another mods, I repeat, the file must be named differently than other Init bank files from the game / other music mods.

    For loading that bank into the game, have a code similar to this by using the R2API submodule SoundAPI

```csharp
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
cstate1.GroupId = 1598298728U; // Group ID, gathered from the Init bank txt file. Vanilla Group ID Music_menu
cstate1.StateId = 2326263800U; // State ID, gathered from the Init bank txt file. Denote where the track will be played, in this case it uses the vanilla state Logbook2
mainlogBookTrackDef.CustomStates.Add(cstate1);
var cstate2 = new SoundAPI.Music.CustomMusicTrackDef.CustomState();
cstate2.GroupId = 792781730U; // gathered from the Init bank txt file. Vanilla Group ID Music_system
cstate2.StateId = 2607556080U; // gathered from the Init bank txt file. Denote where the track will be played, in this case it uses the vanilla state Menu
mainlogBookTrackDef.CustomStates.Add(cstate2);

d.SceneDefToTracks.Add(logBookScene, new List<SoundAPI.Music.MainAndBossTracks>() { new SoundAPI.Music.MainAndBossTracks(mainlogBookTrackDef, null) });

SoundAPI.Music.Add(d);
```

### Explanation on "magic numbers":

RoR2 comes with its own Init.txt file that can be found inside Risk of Rain 2_Data\StreamingAssets\Audio\GeneratedSoundBanks\Windows folder.

What interests us the most are State Groups and States.

If we loot at any game's MusicTrackDef with thunderkit we will see that most of them have two states - one that references the song itself and another references where the song is used

![image](https://github.com/risk-of-thunder/R2Wiki/assets/53978306/e11b39f3-3589-4b3a-894f-3e6859832ef8)

While song reference can be pretty much anything, where it is used needs to match game's values so it can actually play.

That's exactly what we did when we created CustomMusicTrackDef with two custom states, first pair of group and state ids reference our song, while second references needed game's music system.

If we cross-reference second pair with game's Init.txt we discover that Group ID is State Group "Music_system" and State ID is Stats "Gameplay".

There are 5 states in total

* Gameplay  - 89505537
* Bossfight - 580146960
* None - 748895195
* SecretLevel - 778026301
* Menu - 2607556080

By changing second StateId to any of those values we change where the song plays. 

Also, you need to make sure whether new song is "main" track or "boss" track.

Each SceneDef has two field for music - mainTrack and bossTrack.

All main stages have both of those fields populated, however some other scenes (such as main menu, logbook, bazaar, etc) can only have mainTrack.

Depending where it plays it needs to have correct State ID.

For example, for main menu, logbook, lobby, etc. it needs to be Menu (2607556080), for stages it needs to be Gameplay (89505537), for teleporter events (those are "boss" tracks) - Bossfight (580146960). SecretLevel (778026301) is only used for bazaar as far as I know, every other "Hidden Realm" uses Gameplay for its main track.

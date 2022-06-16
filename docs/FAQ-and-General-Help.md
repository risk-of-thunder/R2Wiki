## Foreword
Hi there, and welcome to the faq page. Thank you for taking your time reading this. In here you will hopefully find answers to your questions. If you're still looking for an answer or further details on a specific question, please do not be afraid to ask in the [modding discord server]( https://discord.gg/5MbXZvd) in the channel #tech-support-read-faq.

Before anything, please remember that the team running this Discord server and the mod developers are doing this in their free time and for free. Please be respectful :smile:

## General

#### I'm on console how can I use these mods?

I am dearly sorry but currently there is no mod support for console. This would require considerable effort from hopoo and an agreement with each platform similar to what bethesda has, If you really want this on console then ask the Developers:

- [Official Risk of Rain Twitter](https://twitter.com/RiskOfRain)
- [Dev Team Member "Hopoo's" Twitter](https://twitter.com/the_hopoo)
- [HopooGames's Twitter](https://twitter.com/HopooGames)
- HopooGames's email: contact@hopoogames.com

#### What do the Risk of Rain Developers think about modding, do they support it?

They've made an official statement [here](https://steamcommunity.com/games/632360/announcements/detail/2536008342705603109) and have spoken about it in an interview with Woolie [here](https://www.youtube.com/watch?v=j0Mpqfl3L50). They haven't popped in to say hello to the modding discord nor do they provide an official modding API but they do support the modding community by trying not to break mods when updating the game and altering the code to make it more convenient for modders to slot in their mods. Their long term goal as stated in the interview is to stop mods breaking after every update.

#### Why can't I play Prismatic trials?

The Developers have asked that modded users stay out of prismatic trials since modded players likely have a different gameplay experience from vanilla players thus shouldn't get leaderboard placement.

#### Can I still play modded with my vanilla friends?

Yes you can by using the [Unmodded Version](https://thunderstore.io/package/frostycpu/UnmoddedVersion/) mod, however this limits you to client-side and sever-side/host mods. Mods that add completely new characters, enemies, skills, etc will not work.

#### Will mods affect my ability to play vanilla?

##### With a mod manager

You can still play vanilla Risk of Rain 2 by launching via steam, "Risk of Rain 2.exe", a shortcut or clicking "Start Vanilla" inside a mod manager.

##### Without a mod manager

[You can still play vanilla Risk of Rain 2 if you set up your shortcuts appropriately using this guide.](https://github.com/risk-of-thunder/R2Wiki/wiki/Running-modded-and-unmodded-game-with-shortcuts)

#### What mods should I get?

Have a quick google or ask around on the discord's. There will be some varied opinions on what are the best mods and recommend mods but guides and some suggestions should help. Just open up the Thunderstore and start looking. Compile a list of what looks interesting, and if you still feel stuck, post that list to the Discord for more feedback.

#### Haven't played in a good while, what's new?

Look on Thunderstore sorting by last updated.

#### How do I configure my mods?

Configs are located in "\BepInEx\config" and can be opened with any text editor.

#### I have no config files in "\BepInEx\config" how do I get them?

You must launch the game with mods at least once for it to generate any configuration files so.. do that!

#### I think I broke my config file. How can I fix it?

Simply delete the config file and launch the game with mods again, It will generate a fresh copy for you to edit.

## Installation

#### How do I get started with mods?

[Please use the Beginners Guide](https://github.com/risk-of-thunder/R2Wiki/wiki/Beginner's-Guide-for-Modding-Risk-of-Rain-2).

#### How can I be sure my install works?
When you start Risk of Rain 2, a console/command prompt should appear. If there is a red wall of text, then something is wrong and, hopefully, we can solve that once we get to the common errors.

## Common errors

#### There is a lot of red text in the BepInEx console! What do I do?
If the red text is along the lines of "Viewable not defined", you can ignore it.
     If the red text says "Missing dependencies for [NameOfMod] : -com.bepis.r2api", then you're missing R2API. Please install it by following the instructions above.
     If the red text says "FileNotFoundException : Could not load file or assembly 'mmhook ...", then you're missing a piece of R2API. You're probably missing the file in /BepInEx/monomod, which can be found in the R2API archive.
     If this doesn't fix most of your errors, try deleting your managed folder and verifying the game through steam.
     Lastly, if there is still excessive red text you can drop a message in the [modding discord server]( https://discord.gg/5MbXZvd) in the channel #tech-support-read-faq.

#### I'm locked behind the Steam UI!?
Yeah , sadly we don't even know why that happens. Sometimes it goes away by itself and then comes back. You can still invite your friends through the lobby IDs though.

#### "ModName" is throwing errors!
Before taking the pitchforks out, make sure you have all the dependencies and that every file is in the right folder. The mod might also just be outdated due to an update from the base game.
     If you're 100% sure you installed the mod properly, you can tag the mod creator in #tech-support-read-faq and report this error.

#### Somehow all damage over time effects are way too OP
You're probably using SeikoML compat layer, it's quite buggy and to be fair most mods that would require you to have this have bepinex equivalents , switch to those.

#### Everyone in the party just dies instantly!
Most likely an error with a mod that tries to mess with dead players such as turning them into drones or other monsters.

#### There's no teleporter/chests!
You most likely installed a mod that is trying to spawn something or edit something that should spawn but doesn't exist. The game really doesn't like that and stops working at that point.

#### When I try to play with my friends it says "Version Mismatch"
Both of you need the same mod installation , alternatively you can install an ID spoofing mod to trick the game into thinking you are Vanilla and letting your friend connect. If you all use mods in the group , you won't need an ID spoofing mod , remove those or configure them to allow modded clients

#### How do I unlock every character after installing a survivor mod?
This is due to a badly installed R2API , probably missing the monomod part

## Development

#### How do I start modding this game?
You're on the modding wiki right now! Go click around and see some stuff. I'd recommend checking out the [first mod tutorial page](https://github.com/risk-of-thunder/R2Wiki/wiki/First-Mod) for starting out coding mods.

#### Where do I ask my development related questions?
In the [modding server]( https://discord.gg/5MbXZvd)'s development channel, #development. Don't forget people might be a little busy but they usually respond quickly.

#### I want to create a mod that uses custom Assets
It is extremely recommended that you use the Tool "ThunderKit" for developing with assets for the game, a guide regarding what thunderkit is and how it works can be found [here](https://github.com/risk-of-thunder/R2Wiki/wiki/Thunderkit:-First-Steps)
Regardless, Most of the content will still require code, so be sure to also check out the [first mod tutorial page](https://github.com/risk-of-thunder/R2Wiki/wiki/First-Mod) for starting out coding mods.

## Thunderstore

#### The site is blocked and I can't download anything
Ah yeah, BackBlaze (our host) has nodes that are sometimes blocked in Russia, sadly as of now all we can say is that you can still access it with a Proxy/VPN

#### The "install with Mod Manager" button doesn't work
You need a mod manager that supports that functionality, there's a few on the [Thunderstore](https://thunderstore.io/).
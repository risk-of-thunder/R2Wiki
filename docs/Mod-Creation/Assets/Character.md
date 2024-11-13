# Character

## Henry Tutorial
[Rob's character template, called HenryTutorial](https://github.com/ArcPh1r3/HenryTutorial), is the recommended method of adding a custom survivor as it is the most up to date, and most complete. It does its best to abstract as much as possible under the hood that you don't have to worry about.

## Thunderkit.
If you are more Unity-minded, and would like to get your hands dirty with the nitty gritty of creating characters, Thunderkit may be for you.  
Currently, no such template like the Henry Tutorial exists for Thunderkit, but that isn't to say it is impossible. Reach out in the modding discord for help along the way.
- [See here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Unity-Editor-Usage/ThunderKit/Crash-Course-and-Getting-Started/) to get started with thunderkit.
- RoR2EditorKit has a wizard for easily creating a CharacterBody.  
  - Provided your Thunderkit Import went smoothly and you have RoR2EditorKit installed, go to the toolbar at the top, and hit `Tools/RoR2EditorKit/Wizards/Character Body`

[ThunderHenry](https://github.com/Vale-X/Thunderkit-Henry) was a Thunderkit-oriented version of the henry template, but it is currently out of date. It does however, have a good wiki of general RoR2 information that applies to any character, which we have yet to move to this wiki.

## Before You Begin
If you are newer to modding, then I recommend that you start out just working on the skills before you work out anything relating to custom models or animations. Doesn't matter if your character is a commando clone, so long as they're fun to play.

To this end you should check out the [custom skills page on the wiki](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Skills/) since this will give you a working template that's a little more simple to understand than the full character template.

It's a good place to start before you move on to a fully fledged character mod.

Additionally, if you have all of your skill states in their own .cs files, then when it's time to switch templates it will be as easy as dragging the files from one project to the other.
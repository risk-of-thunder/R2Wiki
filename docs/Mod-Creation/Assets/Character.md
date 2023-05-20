# Character

[Rob's character template, called HenryTutorial](https://github.com/ArcPh1r3/HenryTutorial), is the recommended method of adding a custom survivor as it is the most up to date and involves the least amount of jank.


[ThunderHenry](https://github.com/Vale-X/Thunderkit-Henry) is another template, which utilize Thunderkit, even though that template is mostly complete too, be warned though as most people who made a custom character used the regular HenryTutorial rather than this project, so getting help from other may be more difficult.

If you are newer to modding, then I recommend that you start out just working on the skills before you work out anything relating to custom models or animations. Doesn't matter if your character is a commando clone, so long as they're fun to play.

To this end you should check out the [custom skills page on the wiki](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Skills/) since this will give you a working template that's a little more simple to understand than the full character template.

It's a good place to start before you move on to a fully fledged character mod.

Additionally, if you have all of your skill states in their own .cs files, then when it's time to switch templates it will be as easy as dragging the files from one project to the other.

Another thing to note, rob uses a different method of asset bundle embedding than the [current recommended way, which is to just put the assets next to the .dll file (instead of embedding the assets directly in the .dll)](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Loading-Assets/). If you are unfamiliar with loading from stream, then feel free to replace the entirety of the assets static class in robs template with the method that's on the wiki (just make sure to use the same variable names).
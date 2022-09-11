# Getting Started

## Mod Manager

 There are four managers that you can choose from currently. Three are for windows and one is for Linux. For now we will focus on [r2modman](https://thunderstore.io/package/ebkr/r2modman/), if you use Overwolf, you can use [TMM](https://www.overwolf.com/app/Thunderstore-Thunderstore_Mod_Manager)

## R2MODMAN - Recommended
 
### Installation

1. Go to the [Thunderstore download page](https://thunderstore.io/package/ebkr/r2modman/).
2. Click "Manual Download"
![image](https://user-images.githubusercontent.com/72328339/163709493-60331866-158f-406d-a395-965744cd8753.png)

3. Extract the contents of the downloaded .zip file.
4. Run the "r2modman Setup X.X.X.exe" (The "X.X.X" is representative of the version number e.g. v1.1.1) and follow the steps in the installer (**Do not install r2modman inside the Risk of Rain 2 folder.**).

### Updating

r2modman will automatically download any available updates during use.
If an update has been downloaded, it will be installed once you have closed the application.

***

### Installing Mods

Installing mods on r2modman is very simple once you have created and selected a profile you can:

- Click "Online" then browse the mods that have appeared(I recommend sorting by "Last Updated"), click on a mod, click download, confirm "Download with dependencies" and you're done.

OR

- Browse [Thunderstore](https://thunderstore.io/) and click "Install with Mod Manager", confirm "open r2modman?" (Tick the box for always allow if you never want to see the prompt again) and you're done.

"A dependency in programming is an essential functionality, library or piece of code that's essential for a different part of the code to work. For example, a specific library that a given line of code depends on."<sup>[source](https://airfocus.com/glossary/what-is-a-dependency/)</sup> All mods are dependent on BepInEx (allows execution of custom code). An example would be a mod that adds a skin for a modded character, it would have to have the dependency on the modded character, otherwise the code wouldn't be able to run properly.

***

### Updating Mods

Updating is once again very simple and can be done in two ways.

One by one:

![image](https://user-images.githubusercontent.com/72328339/163709755-d4491ec6-efef-4022-9b06-a250fdfd1da5.png)

1. Open the profile for which mods you wish to update.
2. Click "Installed" if it is not already open.
3. Click on the mod you wish to update (Mods with updates available will be indicated by a cloud icon).
4. Click "Update"
5. Confirm "Download with dependencies"

Alternatively, you can click on the cloud icon to update the mod without expanding the info.

OR

All at once:

![image](https://user-images.githubusercontent.com/72328339/163709686-ca686707-a21d-4be0-ad38-9d0d165c0275.png)

1. Open the profile for which mods you wish to update.
2. Click "Settings"
3. Scroll down to the bottom of the "All" tab.
4. Click "Update all mods" 
5. Confirm "Update all"

**It is recommended you update one by one so you can confirm which mods are being updated, mod changes and for general security.**

***

### Configuring Mods

While some mods may have configuration in-game most have configuration files stored in "\BepInEx\config", You can alter the settings in these files by:

**r2modman Config Editor**
1. Select the Profile whos mod settings you wish to configure.
2. Click on "Config editor". ![image](https://user-images.githubusercontent.com/72328339/163709779-652afcd8-3a6b-4642-baee-1d06cd291878.png)
3. Click on the mod you wish to configure.
4. Confirm "Edit Config"

Remember to "Save changes" when you are done.

**Directly**
1. Select the Profile whos mod settings you wish to configure.
2. Click on "Settings".
3. Click on the "Locations" tab.
4. Click "Browse profile folder"
5. Navigate to "\BepInEx\config"
6. Open the appropriate config for the mod you wish to configure with any text editor.

Remember to save when you are done.

***

### Playing
Simple:

1. Open the profile you wish to use.
2. Click "Start modded"
3. Wait a short while until the title screen appears.
4. Play!

***

### Sharing your Mod List

Do you wish to share your list of mods and the configuration tweaks you've done for each one without laboriously telling your friends what to do? Well here is how:

What you do:

1. Select a Profile.
2. Click on "Settings".
3. Click on the "Profile" tab.
4. Click "Export profile as a code".
5. Share the code with your friend; Ctrl+v.

What they do:

If they are currently on their profile screen, they can go to Settings > Profile > Change profile
![image](https://user-images.githubusercontent.com/72328339/163709841-0714ce4c-73b9-4c12-9271-e7123cb28aa4.png)

1. Click "Import"
2. Click "From code"
3. Enter the code provided and confirm "Import"
4. Wait for the mods and configs to download and you're done.

**The code will only last for an hour, if you desire to have a longer lasting way to share then use the file option instead**

***

### Switching Profiles

**Help, I'm trapped in the purgatory of this profile I selected!**

Don't worry, you change profile easily.
1. Click on "Settings".
2. Click on the "Profile" tab.
3. Click "Change profile" and you're free.

***

### Other/Miscellaneous

**Dark Mode**

This very bright UI hurts my eyes make it stop!
1. Select a Profile.
2. Click on "Settings".
3. Click on the "Other" tab.
4. Click "Switch theme".

***

### Troubleshooting
If you are facing any errors with r2modman check here.
https://github.com/ebkr/r2modmanPlus/wiki/Why-aren%27t-my-mods-working%3F

**I have previously installed mods manually before using r2modman**

This will likely cause problems, I highly recommend you use r2modman with a clean Risk of Rain 2 install, so delete your "steamapps\common\Risk of Rain 2" folder and verify your game files by:
1. Navigate to "\steamapps\common".
2. Delete your "Risk of Rain 2" folder.
3. Open Steam.
4. Click the "LIBRARY" tab.
5. Find and right click "Risk of Rain 2".
6. Click "Properties".
7. Click on the "LOCAL FILES" tab.
8. Click "VERIFY INTEGRITY OF GAME FILES...".
9. Wait and you're done, you can go back to r2modman now.

**Nothing works**

If issues persist and all the solutions fail:
1. Select the Profile you're experiencing producing problems with.
2. Click on "Settings". `Settings -> Debugging -> Toggle download cache`
3. Go back to profile selection and delete all your profiles so you can start over fresh.
4. In your first new profile repeat steps 2-3 then click "Clean mod cache".
5. Continue as normal with installing mods.
6. If everything is now working repeat steps 2-4, the cache should be fine now.

## Manual Installation - Not Recommended - Average user please use a [mod manager](#Mod-Manager)

You can install mods manually but it will cause you and developers great pain in the long run so for the sake of everyone's sanity we implore you to use a mod manager instead.

If you happen to get issues with mods while you are doing a manual install and that you request for help in the [modding discord](https://discord.gg/5MbXZvd), people will most likely to use a mod manager instead and come back only if you have issues while using one.

This guide assumes that you are familiar with basic Windows OS use such as the file and folder systems and with the file system of your own computer. It also assumes you know how to download and install programs and unzip archive files.

Most mods require [BepInEx (The Modloader)](https://thunderstore.io/package/bbepis/BepInExPack/) and [R2API](https://thunderstore.io/package/tristanmcpherson/R2API/) typically, both of which can be found on the [Thunderstore](https://thunderstore.io/).

Some mods might require other mods or libraries to work which can be seen in the "This mod requires the following mods to function" section of [Thunderstore](https://thunderstore.io/) but we shall focus on these two for your manual installation.

![image](https://user-images.githubusercontent.com/72328339/163709400-1f5b6523-c443-4777-863f-f0f515514455.png)

### BepInEx

1. Go to the [Thunderstore download page](https://thunderstore.io/package/bbepis/BepInExPack).
2. Click "Manual Download"
3. Navigate to "SteamLibrary\steamapps\common\Risk of Rain 2".
3. Extract the contents of the downloaded .zip to the "Risk of Rain 2" folder.

[If you are installing on Linux please use this guide.](https://github.com/risk-of-thunder/R2Wiki/wiki/Getting-BepInEx-Console-Working-on-Linux)

### R2API

1. Go to the [Thunderstore download page](https://thunderstore.io/package/tristanmcpherson/R2API/).
2. Click "Manual Download"
3. Navigate to "SteamLibrary\steamapps\common\Risk of Rain 2\BepEx".
3. Extract the contents of the downloaded .zip to the "BepInEx" folder.

### Mod Installation & Updating

**Installation**

1. Firstly check the "This mod requires the following mods to function" section of the mods Thunderstore page and ensure you have all the required mods, if not then download those in addition to the mod.
1. Navigate to "SteamLibrary\steamapps\common\Risk of Rain 2\BepEx\Plugins".
2. Extract contents of the downloaded mods .zip to the "Plugins" folder (If the mod's README.md/Thunderstore page contains instructions for a different install location please install there instead).

**Updating**

For updating mods repeat the same process as Installation and overwrite the existing files.

### Disabling Mods

To disable a single mod you must remove the .dll from the Plugins folder.

To disable all your mods rename the winhttp.dll in your Risk Of Rain 2 folder to winhttp.dll.dis. Should you want the mods back, you just rename the file back to winhttp.dll.

[You can set up steam shortcuts to play both vanilla and modded by following this Guide!](https://risk-of-thunder.github.io/R2Wiki/Playing/Running-modded-and-unmodded-game-with-shortcuts/).

### Mod Configuration

While some mods may have configuration in-game most have configuration files stored in "\BepInEx\config", You can alter the settings in these files with any text editor.

## Reporting Bugs for Mods

**Before you do anything check the "Configuring Mods" section; Your mod(s) may have settings you can change to solve your problems**

In order for someone to help you with your problem they will require:

- A clear and concise description of what you were doing and what happened. You may additionally provide a screenshot, gif or video clip if you think this will help.
- A "LogOutput.log" file:

 1. Select the Profile you are experiences bugs or problems with.
 2. Click on "Settings".
 3. Click on the "Debug" tab
 4. Click "Copy LogOutput contents to clipboard"
 5. Pasting(Ctrl+v) this directly into Discord or a .txt file then send that in Discord.

ALTERNATE METHOD:

 1. Select the Profile you are experiences bugs or problems with.
 2. Click on "Settings".
 3. Click on the "Locations" tab.
 4. Click "Browse profile folder"
 5. Navigate to "profiles\X\BepInEx" (X will instead be the name of your profile).
 6. Drag or Copy+Paste(Ctrl+c > Ctrl+v) this into Discord.

**If the files are too big for Discords 8mb limit then store the .txt/.log in a .zip archive(It is very good at compressing text file types) and send that**

Once you have both of these things please use the #tech-support section of the [Official Risk of Rain 2 Modding Discord](https://discord.gg/TvM8H8E) or and @ the developer (assuming you have a suspicion it's their mod causing it) with these otherwise post it without the @ and wait for a helpful user to respond OR contact the mod developer directly instead.
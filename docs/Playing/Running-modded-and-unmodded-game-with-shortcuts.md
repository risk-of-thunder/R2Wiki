# Running modded and unmodded game with shortcuts

This guide explains how to set up BepInEx to allow to run the game modded and unmodded with only shortcuts. 

This guide is only necesary if youre running a setup without a mod manager, If you are using a mod manager (Such as Thunderstore's official mod manager, or R2ModMan) the mod manager should have a button for booting up the vanilla game without deleting your modded setup .

This way you can easily swap between vanilla and modded game without deleting or modifying any files.

This guide requires you to have BepInEx installed.

**Do the following:**

1. Go to Risk of Rain 2 installation folder (`<steam directory>\SteamApps\common\Risk of Rain 2`) and delete `doorstop_config.ini`.

2. Add Risk of Rain 2 shortcut to Steam.

    In Steam, select `Add a game > Add a Non-Steam Game`:
    
    ![Add a non-steam game](https://i.imgur.com/mYIJQbI.png)

    In the opened dialog, click `Browse...` and select `Risk of Rain 2.exe` found in `<steam directory>\SteamApps\common\Risk of Rain 2`. After selecting the executable, click `Add selected programs`, which will add a new shortcut to RoR 2 in your Steam library:

    ![Risk of Rain 2 shortcut](https://i.imgur.com/ajPETXU.png)

3. Modify command line arguments of the shortcut.

    Select the newly created shortcut, right click it and select `Properties`.  
    You can now change the name of the shortcut to something else (i.e. `Risk of Rain 2 Modded`).

    Next, click `Set Launch Options` and paste in the following:

    ```
    --doorstop-enable true --doorstop-target BepInEx\core\BepInEx.Preloader.dll
    ```

   You should now have the following set:

   ![Example of the correct set up](https://i.imgur.com/0OJryOO.png)

   Finally, click `OK` and close the properties.

If you have done everything correctly, you should now have two versions of Risk of Rain 2 in your library: the original one and the shortcut that enables BepInEx (and all the mods).
# Testing Multiplayer Alone

> ***source**: `!localnetwork` and `!exeshortcut` in the Risk of Rain 2 Modding Discord server.*

## Connecting two local instances of the game

1. Add the following line to the `Awake()` function of your `BaseUnityPlugin` subclass:
    ```cs
    // Remove this line before publishing/releasing your mod!
    On.RoR2.Networking.NetworkManagerSystemSteam.OnClientConnect += (s, u, t) => {};
    ```
    - Alternatively, you can install the mod [***Nuxlar-MultiplayerModTesting***](https://thunderstore.io/package/Nuxlar/MultiplayerModTesting/source/) instead *(read the README for usage)*
2. Launch [two instances](#create-an-executable-shortcut-for-modded-profiles) of the game
3. Host a private lobby with one instance
4. Open the console on the other instance and input `connect localhost:7777`

## Create an executable shortcut for modded profiles

You can create a shortcut that points to the Risk of Rain 2 executable that, when clicked, will open an instance that corresponds to a desired r2modman profile. This is especially useful in scenarios where you need two instances of the game open, such as when testing networking locally.

### Steps

1. Create a shortcut of your Risk of Rain 2 executable
    - *Windows: right-click `Risk of Rain 2.exe`, then "Create shortcut" in the context menu*
2. Open the properties of the new shortcut
    - *Windows: right-click the shortcut, then select "Properties" in the context menu*
3. Copy your preferred r2modman profile's launch parameters
    1. *Open r2modman*
    2. *Select profile*
    3. *"Settings" section > "Debugging" tab > "Set launch parameters" option*
    4. *Copy the inline code block labelled "Modded"*
        ```ps
        # Example
        --doorstop-enabled true --doorstop-target-assembly "C:\Users\user\AppData\Roaming\r2modmanPlus-local\RiskOfRain2\profiles\Default\BepInEx\core\BepInEx.Preloader.dll
        ```
4. Append the launch parameters to the "Target" field of the shortcut's properties
    ```ps
    # Example

    # before
    "C:\Program Files (x86)\Steam\steamapps\common\Risk of Rain 2\Risk of Rain 2.exe"

    # after
    "C:\Program Files (x86)\Steam\steamapps\common\Risk of Rain 2\Risk of Rain 2.exe" --doorstop-enabled true --doorstop-target-assembly "C:\Users\user\AppData\Roaming\r2modmanPlus-local\RiskOfRain2\profiles\Default\BepInEx\core\BepInEx.Preloader.dll
    ```

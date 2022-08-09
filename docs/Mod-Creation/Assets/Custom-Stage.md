# Custom Stage

-   [Unity Version 2019.4.26](https://download.unity3d.com/download_unity/e0392c6b2363/Windows64EditorInstaller/UnitySetup64-2019.4.26f1.exe)
-   Create a new project with the 3D template
-   Add these lines in your `MyUnityProject/Packages/manifest.json` file
    -   ```json
        "com.passivepicasso.thunderkit": "https://github.com/PassivePicasso/ThunderKit.git",
        "riskofthunder-ror2importextensions": "https://github.com/PassivePicasso/RoR2ImportExtensions.git",
        ```
-   In the `ThunderKit Settings`:
    -   `Import Configuration`:
        -   `Assembly Identity Algorithm`: `Stabilized`
        -   `Import Project Settings`: `Everything`
    -   `ThunderKit Settings`:
        -   `Browse` to the game executable
        -   Click `Import`
-   If you get a message box telling you to make a backup, click `No Thanks`

    ![API Update Required Message Box](https://i.imgur.com/zROzKsY.png)

-   Close the Unity Editor

-   Put the content of the [Assets.zip](https://cdn.discordapp.com/attachments/575431803523956746/1006280734648057876/Assets.zip) inside the `MyUnityProject/Assets/` folder

-   Open the Unity Editor

-   Open the `NuketownStage/Scenes/NuketownScene.unity` scene

-   Click on the `GroundMapNodeGroup` GameObject, enable it with the `Unity Inspector` in the scene, right click for moving the camera around, but also for getting the preview of nodes and placing them around.

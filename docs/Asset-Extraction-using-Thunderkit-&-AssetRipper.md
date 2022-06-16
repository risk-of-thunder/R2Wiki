# Warning

GameImporter no longer works for Risk of Rain 2 due to the migration to Addressables and a lack of support. Use [AssetRipper](https://github.com/AssetRipper/AssetRipper) instead. This page needs to be updated for AssetRipper.

# GameImporter
[GameImporter](https://github.com/PassivePicasso/GameImporter) is a fork of UTinyRipper developed by Passive Picasso (AKA: Twiner). It is used for importing a game's assets and scripts into a Unity project for inspection.

## Why GameImporter and not UTinyRipper directly
While GameImporter is techincally a fork of UTinyRipper, GameImporter extracts the game's assets in such a way that allows them to be easily transfered to other ThunderKit managed projects. This is important, as trying to transfer any of Risk of Rain 2's assets that were extracted with UTinyRipper will cause issues in your ThunderKit project, such as missing scripts. This is due to the fact that UTinyRipper extracts the classes along with the assets, and as such, the extracted assets reference these individual class files. Moving the assets out of the project will cause the scripts within said assets to become missing, as they make use of the class files.

GameImporter instead changes it so these Assets reference the Risk of Rain 2 Assembly-CSharp.dll instead of individual classes, which bypasses this problem.

## Extracting the Game's assets using GameImporter

You will need to have the basic knowledge explained in [Thunderkit: First Steps](https://github.com/risk-of-thunder/R2Wiki/wiki/Thunderkit:-First-Steps).

1. Begin by creating a new Unity project, and installing ThunderKit (see the first guide in [ThunderKit: First Steps](https://github.com/risk-of-thunder/R2Wiki/wiki/Thunderkit:-First-Steps) for a tutorial on this).
2. Open the ThunderkitSettings window (Tool/Thunderkit/Settings) and look at the PackageSourceSettings.
3. If a PackageSource that points to https://thunderkit.thunderstore.io exists, skip to step 6, otherwise, continue.
4. Click the "Add" button, and on the dropdown menu, select "ThunderstoreSource"
5. Select the new Thunderstoresource, name "Package Source:" to "Thunderkit Extensions" and "URL:" to "https://thunderkit.thunderstore.io"
6. Close the ThunderkitSettings, open the PackagesWindow (Tools/Thunderkit/Packages)
7. Expand the Thunderkit Extensions menu, select GameImporter uploaded by "Twiner". Hit Install
8. Return to Unity and wait for it to download and install GameImporter.
9. Once Unity becomes responsive and installs GameImporter, look in the upper part of the window and open `Tools`. If `Game Asset Importer` appears as one of the options, you've succcesfully installed GameImporter. Simply select that option and GameImporter will begin to extract RoR2's Assets. You can configure what you want to extract from Risk of Rain 2 from the ThunderKit settings window (`Tools > Thunderkit > Settings`).
10. Remember that asset extraction takes time. Go do something else while GameImporter is busy Extracting the game's assets.

## Working Shaders in unity editor.

Since Commit [Shader Export](https://github.com/PassivePicasso/GameImporter/commit/65b3ecfa0e6fb34fa4fcdbf67915672119a88985). GameImporter now has the ability to *properly extract the Hopoo shaders and have them work inside the editor* Without the usage of stubbing.

If youre looking to upgrade your game importer project, simply delete all the assets inside your Assets folder, and click "Game Asset Importer" and import the assets once again.

![](https://cdn.discordapp.com/attachments/873741640198672404/888814017563471963/0042d63748d76fdc67fdc056667e336a.png)

###### (Image of the Vagrant's passive electricity effect, uses cloud remapping.)

There are methods for having these shaders *Work outside of the game importer project*, albeit with a few caveats, a guide can be found [here](https://github.com/risk-of-thunder/R2Wiki/wiki/Working-Shaders-outside-of-a-Game-Importer-Project.)

If you need to select a proper shader for a material youre working on, you'll need a special editor class to select your shaders. the class can be downloaded [here](https://cdn.discordapp.com/attachments/562704639569428506/888431119052193872/ExtraShaderPicker.cs)

Simply place the downloaded class inside a folder named "Editor" on your base assets folder.
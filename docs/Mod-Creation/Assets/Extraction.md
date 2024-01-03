# Extraction

## Why

Extracting / Ripping the game assets, but also:

Getting the game YAML shaders from the game, which can then later be used on your modding Unity Project directly.

## How
### Extracting
 Download the [AssetRipper GUI](https://github.com/AssetRipper/AssetRipper/releases/)
- Open AssetRipper.exe. A console window will open, as well as a browser window with the GUI. If it doesn't, copy the link in the console and open it yourself.  
![image](https://github.com/risk-of-thunder/R2Wiki/assets/53384824/80ccb466-cf70-4bfa-9485-c15fbc161c36)
  - *If it doesn't look like this, you may have an older version. Skip to the Version 0.3.4.0 and Older section below.*

- Go to Settings at the top and configure as follows.  
Main things to note are Shader Export Format, and Script Export Format.
![image](https://github.com/risk-of-thunder/R2Wiki/assets/53384824/11000e79-e7eb-4d60-ba63-a1d58c0a8395)
- Go to Commands at the top, and enter the path to your Risk of Rain 2 folder in the field.  
![image](https://github.com/risk-of-thunder/R2Wiki/assets/53384824/236d2fa8-9832-4318-a53b-22dee8380b24)
  - Hit Load.
  - It should take you to the home page with the files now loaded.
- Go back to the Commands page, enter the path to an **EMPTY** folder to which you would like to extract the project, and hit Export.  
![image](https://github.com/risk-of-thunder/R2Wiki/assets/53384824/ca4e8daf-5d57-4295-b290-dbb32a793057)

### After Extracting
Chances are if you try to open this project in Unity (make sure you have the right version), it will crash on startup.  
- Go to your exported folder, navigate to ExportedProject\Assets\LightingDataAsset and delete this whole folder.
- Back to your exported folder, navigate to AuxiliaryFiles\GameAssemblies, find UnityEngine.UI.dll, and copy this to ExportedProject\Assets\Plugins.

## Version 0.3.4.0 and older:
- Open AssetRipper.exe
- Configuration as follow 

  ![image](https://cdn.discordapp.com/attachments/873741640198672404/1033447183871123558/unknown.png)

- File -> Open File

  ![image](https://user-images.githubusercontent.com/837334/176716836-28164064-8f18-4483-92cb-833e19f95631.png)

- Select game executable (Risk of Rain 2.exe)
- Export -> Export all files

  ![image](https://user-images.githubusercontent.com/837334/176716983-3d09deca-a0cb-4a47-8f30-46f83bfeda18.png)
- Select an empty folder where the game will be extracted in.

### After Extracting
Chances are if you try to open this project in Unity (make sure you have the right version), it will crash on startup  
- Go to your exported folder, navigate to ExportedProject\Assets\LightingDataAsset and delete this whole folder
- Back to your exported folder, navigate to AuxiliaryFiles\GameAssemblies, find UnityEngine.UI.dll, and copy this to ExportedProject\Assets\Plugins
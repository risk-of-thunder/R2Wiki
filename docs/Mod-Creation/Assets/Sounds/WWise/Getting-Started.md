# Getting Started

## Requirements:
* [Wwise v2019.2.12.7544](https://www.audiokinetic.com/download/)
* An IDE (like Visual Studio 2022)
* Audio files in .wav format (will not work in any other format, convert using audacity if needed)

Note: This tutorial will not showcase all the advanced features Wwise offers such as randomness, 3d audio, and other features. Only importing an audio and getting it to play via script.

### Installing Wwise into a Unity Project

Follow the order exactly, deviation will lead to the project not being setup correctly.

The Wwise Installer does not function well when trying to install to an existing project, so it is recommended you start from a blank unity project to complete these steps.  After completing all steps you can copy the content from the new Wwise project into your target project and should be able to open it successfully with wwise at that point.

  1. Get Offline Installer from AudioKinetic
https://www.audiokinetic.com/library/edge/?source=InstallGuide&id=working_with_offline_installers
  2. Create new Unity Project with Unity Hub for Unity 2019.4.26
  3. Close Unity
  4. Open the Wwise Launcher and navigate to the Unity Page
  5. Find the new project in the list, and click on the left drop down to select the option for Integrating with an Offline Installer
  6. Wait for the Wwise Launcher to finish the integration process
  7. Open the Project with Unity again
##### Remaining steps are only for ThunderKit users
  8. Create an Assembly Definition in the Assets/Wwise folder and name it Wwise
  9. Create an Assembly Definition in the Assets/Wwise/Editor folder and name it WwiseEditor
10. Install ThunderKit
11. Locate Risk of Rain 2 using the ThunderKit Settings window
12. Wait for import to finish
13. Delete Packages/Risk of Rain 2/Wwise.dll if it exists (.dll will not be visible in the name in Unity)
14. Delete Packages/Risk of Rain 2/plugins/AkSoundEngine.dll if it exists (.dll will not be visible in the name in Unity)
15. Delete Packages/Risk of Rain 2/plugins/AkWaapiClient.dll if it exists (.dll will not be visible in the name in Unity)

### Process:
[The Whole process in video format more indepth up to the coding part.](https://www.youtube.com/watch?v=K0r6fIu-Rxg)

PLEASE NOTE: Dont actually make the game parameter "Volume_Master" or your sounds will go way too quiet with master slider. delete if you already made it but keep Volume_SFX.
* Launch wwise and create / open project.
* To import a new audio, use Project > Import Audio Files > Select your audio file and hit import.
After importing it should appear in the hierarchy as seen below.
![p1](https://i.imgur.com/z4LzA4Q.png)
* Next we're going to create an event that will be used to call this sound from script. 
* At the top hit Layouts > SoundBank
* Now at the bottom left you should see an event viewer, right click inside and create a New Event > Play
* Name it anything and hit okay, now in the target field you will select the audio clip you imported earlier by dragging it into the field. 
* Now that we have our event setup for our audio clip, we can create our soundbank. In the upper right window (SoundBank Manager) you can hit new and create your soundbank.
* Now with your soundbank selected, you can drag your event into the soundbank editor to add it to your soundbank.
* Now just hit Generate Selected and it will generate your soundbank.bnk file!
![p2](https://i.imgur.com/izyvCeO.png)

## Playing your audioclips from script:
[Check how to import a soundbank here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Loading-Assets/#loading-soundbanks)
* Now to finally get a sound to play, just call
AkSoundEngine.PostEvent( <Event Id>, <GameObject Source>);
* To get your sounds event id, just locate the text file with the same name as your generated soundbank.bnk in your wwise projects output folder.

![p5](https://i.imgur.com/CGDqZDl.png)
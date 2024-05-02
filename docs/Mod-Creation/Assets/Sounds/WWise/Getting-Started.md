# Getting Started

## Requirements:
* [Wwise v2019.2.12.7544](https://www.audiokinetic.com/download/) 
	* NOTE: while SoTV version of RoR2 uses 2019.2.12.7544, Autokinetic removed the ability to download it (I've been told they do that from time to time) so instead you can download 2019.2.14.7616, they are compatible and the way you make sure if it is compatible or not is by checking what SchemaVersion and what SoundbankVersion you got in SoundbanksInfo.xml file after generating your soundbanks with Wwise. SoTV version of RoR2 uses SchemaVersion 12 and SoundbankVersion 135. You can find game's SoundbanksInfo.xml inside Risk of Rain 2_Data\StreamingAssets\Audio\GeneratedSoundBanks\Windows.
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
  8. Backup your project before starting so you don't have to reintegrate Wwise in case Thunderkit fails to do its thing.
  9. Create an Assembly Definition in the Assets/Wwise folder and name it Wwise
  10. Create an Assembly Definition in the Assets/Wwise/Editor folder and name it WwiseEditor
11. Install ThunderKit
12. Install RoR2ImportExtensions
13. Locate Risk of Rain 2 using the ThunderKit Settings window
14. Wait for import to finish.
15. Delete Packages/Risk of Rain 2/Wwise.dll if it exists (.dll will not be visible in the name in Unity)
16. Delete Packages/Risk of Rain 2/plugins/AkSoundEngine.dll if it exists (.dll will not be visible in the name in Unity)
17. Delete Packages/Risk of Rain 2/plugins/AkWaapiClient.dll if it exists (.dll will not be visible in the name in Unity)

However, there is a chance that your import will get stuck (as in stops progressing), it is caused by conflicts between Wwise and Thunderkit. To solve this do the following steps:

* Delete both assembly definitions created on steps 9 and 10 and restart import process. Your best bet is to restore the backup that you surely made and start from step 11, ignoring 9 and 10 for now. 
* After Thunderkit does its thing you will get error messages described in steps 15 to 17. Ignore them for now.
* Go to Assets\Wwise and create Assembly Definition called Wwise, under Assembly Definition Reference add Unity.Timeline, click Apply. 
* Go to Assets\Wwise\Editor, create Assembly Definition called WwiseEditor, under Assembly Definition References add Unity.Timeline and Wwise, under Platforms uncheck "Any Platform", click Deselect all and check "Editor" (**this is important, otherwise Thunderkit will attempt to compile editor scripts into assembly, resulting in errors**), click Apply. 
* After this you will still get errors from steps 15 to 17, do those steps to fix the errors. You might need to restart the project after deleting dlls. 

After doing all of this you should have a successful integration of Wwise and Thunderkit into your project. The easiest way to check whether integration was successful or not is to create MusicTrackDef (Create - RoR2 - MusicTrackDef) and if it looks like this 

![image](https://github.com/risk-of-thunder/R2Wiki/assets/53978306/c619cbd6-d0e9-483f-9b2c-c81f3223c5af)

Then you did everything correctly. However if it looks like this

![image](https://github.com/risk-of-thunder/R2Wiki/assets/53978306/4b292886-5d44-446b-bec9-fde478c60db8)

then you messed up somewhere and have to retrace your steps.


### Process:
[The Whole process in video format up to the coding part.](https://www.youtube.com/watch?v=QtfYsdJFty4)

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
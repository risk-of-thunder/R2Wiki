# Working Shaders outside of an AssetRipper project

TODO: The following needs to be updated from GameImporter to [AssetRipper](https://github.com/AssetRipper/AssetRipper), and additionally be updated for ThunderKit 5.

While having access to shaders inside the GameImporter, it can make working materials for a custom mod a bit hectic, this is because you would need to keep switching between GameImporter and your Unity project only to do few modifications to the material.

There are ways of making the Hopoo Shaders work outside of the GameImporter project, albeit with a few caveats.
![](![](https://i.gyazo.com/418533a9fefe25957736c1e46d372ae7.png)
###### an example of shaders working outside of GameImporter. the porject on the right is the Lost in Transit project.

The Caveats of having hopoo shaders outside of GameImporter are the following:
* Having to add the Hopoo shaders to your `.gitignore`
* Having to deal with the potential issues that come with multiple people working on your project (due to the repo not having the shaders)
* Optional: Upgrading your project to use Thunderkit so you can build your assetbundle. for more information on this, click [here]

Keep in mind that you DO NOT need to port your entire mod to thunderkit, we're only using thunderkit so we can build our assetbundle with a special pipeline.

## Getting Started
You'll need...:
* A GameImporter project.
* Your unity project.
* Opotional: Very basic thunderkit knowledge.

### Setting up the Shaders.
Due to the nature of the hopoo shaders, nobody in the risk of rain 2 community can give you the shaders you'll need pre-packaged, as such, you'll have to set up your project to accept the hopoo shaders. This guide will go step by step on how to do this.

* Step 1: Close both your Unity Project and the GameImporter project.
* Step 2: Have both your project's and GameImporter's root folders open.
![](https://i.gyazo.com/6a579b58f718d010a2b2cdb7d28eacb2.png)
* Step 3: Copy over GameImporter's Project settings folder to your project's root folder, replace all the files.
![](https://i.gyazo.com/bbca3e02447d997e6c1d8bfc559339e7.png)
* Step 4: In your GameImporter's root folder, go to Assets. Copy the Assets/Shaders folder and paste them in your project's Assets folder, ideally within a subfolder.
![](https://i.gyazo.com/7b86fdfd184ba107ea54ea4fc17ade37.png)
* Step 5: Go into the GameImpporter's Resources folder, copy the following shaders & their meta files to the folder where your placed the shaders found inside Assets/Shaders.

		decaliciousgameobjectid.asset
		efficientblur.asset
		fillcrop.asset
		translucentimage.asset
		underwaterfog.asset

Said folder should now have a total of 242 files.
![](https://i.gyazo.com/8f93a33c3ef6bb68ff36625a24104496.png)

* Step 6: Copy the entire contents of Resources/Shaders into your project's Assets folder, ideally within the same folder where you have the other shaders.
![](https://i.gyazo.com/dfe0bba21fcec89dd6eb7652549fed7c.png)

The final product should look something like this. where "StolenShaders" are all the shaders found inside Assets/Shaders & Assets/Resources. and ActualShaders are all the shaders found inside Assets/Resources/Shaders

![](https://cdn.discordapp.com/attachments/575431803523956746/889546505835393034/unknown.png)

* Step 7: Boot up your unity project, you should now have working shaders in your editor.
![](https://i.gyazo.com/9abfb0b6be621db7fca428fec685a40a.png)

### Dealing with Copyright issues.
In case your mod doesnt have its editor in a github repository, then you do not need to worry about this. but if you do, you must know that you ***Cannot under any circumstance*** push right now, this is due to the fact that you'd be technically redistributing the real Hopoo shaders. which is a violation of Hopoo Games' Copyright.

Assuming you followed the method here, and all the shaders in GameImporter are inside a folder, you can simply add a `.gitignore` there with the following contents.

	*.*
	!.gitignore
	!*.meta
This will effectively make your repository ignore all the shader files except the meta files.

## ***REMEMBER, YOU NEED TO GIT IGNORE THESE SHADERS, OTHERWISE YOU RUN THE RISK OF HOPOO GAMES / GEARBOX TAKING LEGAL ACTION AGAINST YOUR MOD. YOU'VE BEEN WARNED.***

### Asset Bundle Building
Now that you have the shaders in the editor, there's this slight issue that comes with developing your materials.
You cannot simply have the Materials use the real shaders, this is because when your assetbundle builds, the real hopoo shaders will be in the asetbundle, and this is technically redistributing them, which goes against Hopoo Games / Gearbox's EULA.

An option would be to have them all use the stubbed shaders, and switch to the real one only to make modifications, and afterwards switch them back to the stubbed ones.

Otherwise, you can use thunderkit and have a special pipeline setup that'll do the following for you:
* 1.- Switch all your material's shaders from real shaders to stubbed shaders.
* 2.- Build the assetbundle, which causes all the materials in it to reference stubbed shaders.
* 3.- Switch back all your material's shaders from stubbed shaders to real shaders.

The end result causes you to have all your materials use the real shaders in the editor, but the exported version in the assetbundle to use the stubbed shaders.

To begin, you'll need at least basic understanding of how Thunderkit builds your assetbundle, the simplest version contains just a manifest with an assetbundle definition datum

![](https://i.gyazo.com/75bc20d53efb79db04166058de05e724.png)

In case you have all your materials inside a single folder, you can name this folder "Materials" and add it to the list of assets.

![](https://i.gyazo.com/3010125d7405bc51f47586696cb6d8e2.png)

The next step is building a very basic pipeline with a custom job. You can download the class that handles building the assetbundle and swapping shaders [here](https://cdn.discordapp.com/attachments/873741640198672404/889559116622741514/StageAssetBundlesAndSwapShaders.cs)

You need to place this class inside a folder in yoiur assets called "Editor".
Once you click "execute". thunderkit will build your assetbundle with stubbed shaders instead of the real shaders.

![](https://i.gyazo.com/395f247fa7e66ce960c58530ce3e6adb.png)

The pipeline expects you to use Kevin's [stubbed shader setup](https://github.com/risk-of-thunder/R2Wiki/wiki/Creating-Mods-with-Thunderkit#using-stubbed-shaders) for this to work properly. And also expects you to follow the guide on using shaders outside of a GameImporter project. 
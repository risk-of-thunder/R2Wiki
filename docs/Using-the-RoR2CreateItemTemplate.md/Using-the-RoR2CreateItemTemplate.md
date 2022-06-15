Link to Project Template. Just download the `RoR2CreateItemTemplate.zip`: [RoR2CreateItemTemplate](https://github.com/derslayr10/RoR2CreateItemTemplate)

This Visual Studio Project Template was made to help speed up the creation of items using KomradeSpectre's Aetherium Item Creation Tutorial. This is not a replacement for this tutorial, but should be used alongside the tutorial for ease of use.

The GitHub page has a simple how-to-use, but here is a more in-depth explanation:

To use this tool, simply download the .zip file from the GitHub and place it in your `Visual Studio 2019/Templates/ProjectTemplates` folder. For me, this was found in my `Documents` on Windows 10.

The template contains Komrade's `ItemBase.cs` and `ItemHelpers.cs` for a basic item class and a render helper. Also included is a `Main.cs` that initializes each item and an `EXAMPLE_ITEM.cs` that is just a filled in example of the ItemBase to show how to make a new class inheriting the base. Simply create a new class that inherits the ItemBase, and fill in all the missing information.

NOTE: With the way the project template is made, several locations in several code files are automatically replaced based on the name of the Visual Studio Project. Most of these replacements have to do with the `namespace` keyword and the `using` statements at the top of your files, though are also used in the strings defining paths within the asset bundle. These names are only replaced on creation of the project, and if you rename your project manually, you may need to go in an change the name in these locations, or face many errors. To keep things simple, do not rename your project unless necessary and do so with caution.

The template also includes an asset bundle and UnityPackage within it. To use these, when you make a new Unity Project, first follow the steps from Komrade's tutorial to delete the `Scene` folder and the objects in the `Scene Hierarchy` on the left side of the screen, then import the package using `Assets -> Import Package -> Custom Package` and grabbing the `CREATE_ITEM_TEMPLATE_UNITYPACKAGE` from the Visual Studio project folder. This will create a lot of folders, many empty expect for a (nearly) empty text file. This text file is here because of weirdness with Unity not letting me export empty folders, so I put what I thought would be the least amount of data in it to keep the package small. These folders also contain a Sample Item prefab and material. Simply use the Unity project like Komrade says to and ignore the sample information.

NOTE: To help clarify things for beginners, some folders have different names than Komrade uses. You can rename these to reflect his tutorial, but otherwise you will need to be sure to include the folder names in the paths in the VS project, or it will not work correctly.

When you go to build the asset bundle, add all items to the `create_item_template_asset` bundle, and build the bundle into the correct folder in the Visual Studio project. This will automatically update the already existing asset bundle under this name, which will automatically bring in your changes without having to do anything after this step.

Other than this, follow Komrade's tutorial.
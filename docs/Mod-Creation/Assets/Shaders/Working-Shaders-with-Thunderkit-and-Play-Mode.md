## Prerequisites
* Working [Thunderkit](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/ThunderKit/Crash-Course-and-Getting-Started/) project
* [Stubbed Shaders](https://github.com/risk-of-thunder/RoR2StubbedShaders)

## Warning
This makes the project fairly unstable after exiting Play mode, it can and will crash, sooner or later. Crashes can be avoider by occasionality restarting the project. My advice is to do edits in bulk, where you adjust all materials during the same Play mode session, quit it and restart the project. My assumption it has to do with severe memory leaks but that's the price we pay for dealing with Unity. The project will behave normally if you don't enter Play mode and those crashes don't corrupt anything, just be sure to save the scene often.

## The Sause
The gist of it is that we use Addressables to load game's shaders and replace stubbed shaders with game's version in editor's play mode, which allows us to use up to date game shaders with zero chances of accidentally shipping them in our mod (which can and will break some stuff on top of it being a copyright infringement). Of course the method is not ideal and the tutorial will go through some downsides as they come up.

From the very start I'd like to emphasize - this method does not allow you to reference game's assets in your materials, if you write a code that loads, lets say, an image asset for your material and then put that image reference to your asset material bad things might happen, like completely bricking your project on restart. 

First of all, add this script to your project.
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement;
using UnityEngine.ResourceManagement.AsyncOperations;
using UnityEngine.ResourceManagement.ResourceLocations;

public class ReplaceShaders : MonoBehaviour
{
    #if UNITY_EDITOR

    const string PREFIX = "Stubbed";
    const int PREFIX_LENGTH = 7;

    // Start is called before the first frame update
    void Awake()
    {
        SwapShaders();
    }

    private void SwapShaders() 
    {
        Renderer[] components = Resources.FindObjectsOfTypeAll<Renderer>();

        foreach(var renderer in components)
        {
            if(renderer.materials != null){
                for(int i = 0; i < renderer.materials.Length; i++)
                {
                    var material = renderer.materials[i];

                    if(!material){
                        continue;
                    }
                    SwapShader(material);

                    renderer.materials[i] = material; 
                }
            } 
            if(!renderer.material){
                continue;
            }
            SwapShader(renderer.material);
        }

        void SwapShader(Material material)
        {
            string stubbedShaderName = material.shader.name;
            if (!stubbedShaderName.StartsWith(PREFIX)) 
            {
                return;
            }
            IList<IResourceLocation> resourceLocations = Addressables.LoadResourceLocationsAsync(stubbedShaderName.Substring(PREFIX_LENGTH) + ".shader", typeof(Shader)).WaitForCompletion();
            if (resourceLocations.Count > 0)
            {
                material.shader = Addressables.LoadAssetAsync<Shader>(resourceLocations[0]).WaitForCompletion();
            }
        }
    }
    #endif
}
```

The script requires your assembly definition to have `Unity.Addressables` and `Unity.ResourceManager` references, either by assembly or by assembly definition (but considering we are using Thunderkit you will have those dlls imported with RoR2).

<img width="550" height="363" alt="image" src="https://github.com/user-attachments/assets/99efbb2f-e7a4-4115-81c5-665f3858003c" />

Add an empty object to your scene, either drag your script to it or add the script via `Add Component` button and you are basically done. Now press the Play button at the top of your window. However your editor might crash. 

### Fixing Play Mode Crash
This is a thing introduced with Alloyed Collective, Gearbox added `Unity.Burst` to RoR2 and obviously we import it together with the rest of the libraries. However, shipped `Burst` dlls are not compatible with Editor and causes crashes. To fix it, first go to `Packages\Risk of Rain 2` of your project, find `Unity.Burst.dll` and `Unity.Burst.Unsafe.dll` and delete them. After that go to `Packages` folder of your project, open `manifest.json` and add the following line to your dependencies

`"com.unity.burst": "1.8.21",`

Save the file and go back to the project, it should reload and import the Burst library from the package manager. With that you should have Play mode now working.

### Play Mode Material Editing
We are now in Play mode. I would recommend instantly pressing Pause to not waste your PC's power since chances are you are not running any game's logic anyway. Do note that **nothing you do on the scene is saved in Play mode.** This is normal Unity behaviour and it plays to our advantage. Since nothing is saved, the fact that we load and replace shaders for each instance of material on the scene does not affect our assets and we have basically zero chance of our project bricking or losing references like it could happen if you were to actually try and reference game's assets via fields (this is a real thing but do not try this if you do not want your project to brick on restart). 

Here is an example of working Triplanar shader in editor with this method

<img width="1606" height="1106" alt="image" src="https://github.com/user-attachments/assets/63cca0dd-c3c7-4727-9ecb-17b2e09932cc" />

You can edit values and they will be reflected on your material in real time. However, as stated before, they will not be transferred to your existing asset material, both because no changes are saved in Play mode and because you are not actually editing the asset but a separate instance of an asset that was created when we swapped shaders. To save our changes we need to move them to our material asset and doing it by hand is fairly tedious. So instead we are just gonna copy the values.

First, right click on the Inspector header, go to `Add Tab - Inspector`, this will add a new Inspector window.

<img width="500" height="462" alt="image" src="https://github.com/user-attachments/assets/91194fba-50c0-4183-921d-16d4b1f0788d" />

With a second Inspector window created, move it so you have two Inspectors active at the same time.

<img width="987" height="647" alt="image" src="https://github.com/user-attachments/assets/f1c2ee64-7dd0-4ab9-8824-d0d890431eb0" />

Select your asset material in your Project window and press the Lock button on one of the Inspectors.

<img width="435" height="155" alt="image" src="https://github.com/user-attachments/assets/915b0068-b023-45a4-9cf8-1fffe119c6eb" />

This will lock this Inspector to our asset so it stays active no matter what we select. Strictly speaking you do not need to do this, however this helps a lot when copying values from Play material to asset material, double checking that you applied everything correctly. Now, on Play material click 3 dots, select `Copy Material Properties`

<img width="733" height="336" alt="image" src="https://github.com/user-attachments/assets/71c7a0ca-1142-4f1c-ad51-5689c93af64f" />

Now go to asset material, click the same button and select `Paste Material Properties`

<img width="615" height="303" alt="image" src="https://github.com/user-attachments/assets/6d277a2b-bb29-420e-9770-9020d51a2cf3" />

Congratulations, you have moved all of the values from your Play material to your asset material so your new values will be saved and reflected on your next Play mode start.

### Pink Material Issues

Now after existing Play mode you might encounter an issue where all your materials are pink.

<img width="1606" height="1106" alt="image" src="https://github.com/user-attachments/assets/4de94012-0615-4cde-8546-88b128e6805f" />

I am not 100% sure what causes this since it doesn't happen all the time, however the fix is simple. Find any script that you have (in this instance we are going to use our `ReplaceShaders` script) in the Project window, right click it and select Reimport. This reloads the project and fixes the material issues. My guess is that this is related to Thunderkit since Thunderkit has a funny habit of making all materials invisible sometimes on building the pipeline.

### Decalicious

Decalicious is a library RoR2 uses for making decals. We don't really have full access to it, on top of it being deprecated. There is a package for the appropriate version of Decalicious for use in editor that floats around but you will have to find it yourself if you want to use the full library. In this tutorial we are just gonna use stubbed shaders. 

To create a decal, add a `Decal` component to any object on your scene, this will all automatically add `Mesh Renderer` and `Mesh Filter`. Select Unity Cube as `Mesh Filter` and add your Decalicious material (usually `DecaliciousDeferredDecal`) to the `Mesh Renderer` and `Decal` components. Start Play mode, you might notice there is no decal being rendered. That's because while your `Mesh Renderer` material has been replaced, `Decal` still uses stubbed material (note lack of `Instance` for `Decal` material).

<img width="751" height="951" alt="image" src="https://github.com/user-attachments/assets/14538691-353c-46c1-a2d4-184cb74bbce3" />

So to fix this we right click on `Mesh Renderer` material, select `Copy` and then right click on `Decal` material and select `Paste`. This will also fill `Render Mode` with appropriate value depending on the material's shader (in this instance - Deferred). Now we have a working decal in Editor.

<img width="1866" height="829" alt="image" src="https://github.com/user-attachments/assets/daa775e9-9373-439d-8c0b-28600f15b5be" />

Remember, that any changes to scene objects in Play mode are not saved, I repeat this here because it is doubly important for decals, since unlike materials you will move it around to place the decal where you want it to be, doing this prior to having working shaders is problematic to say the least since all you have to work is a cube. To remedy this, you can click on 3 dots in your `Transform`, select `Copy - Component`, exit Play mode and then again click on 3 dots in your `Transform`, this time selecting `Paste - Component Values`.

One thing I will note about Decalicious - the game uses a special mesh called DecalCube for setting up decals, and if you have it in your project then it will be automatically added as a mesh in `Mesh Filter` when adding the `Decal` component. I am not sure how important this is, since I don't see any visual differences between Unity Cube and DecalCube, but something to note. You can get DecalCube by exporting the game into Unity Project. 

## Credits
* Groove_Salad for [ShaderSwapper](https://github.com/Priscillalala/ShaderSwapper) code that was used as basis for ReplaceShaders script
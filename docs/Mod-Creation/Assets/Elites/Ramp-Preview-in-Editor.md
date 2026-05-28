# Creating and previewing elite ramps in Editor

This guide will go through creating and previewing elite ramps in Editor, without the need of external tools, like graphics editors. Explanation of how elite ramps actually work can be found [here](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Elites/).

## Prerequisites

* Way to have shaders in the editor. You have two options:
    * Export Devotion version of the game via AssetRipper. You can download Devotion version via [RoR2VersionSelector](https://github.com/risk-of-thunder/RoR2VersionSelector) and export by following [this guide](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Extraction/).
    * [Setup Thunderkit project](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/ThunderKit/Crash-Course-and-Getting-Started/) and use [this guide](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Assets/Shaders/Working-Shaders-with-Thunderkit-and-Play-Mode/) to get working shaders with Play mode.

* A model to preview ramp on. This guide will use Sand Crab from EnemiesReturns.
* [GradientTexture](https://github.com/mitay-walle/com.mitay-walle.gradient-texture) package. Despite it being marked as abandoned and repo suggesting to use ProceduralTexture this package is simpler and only gives us what we need. And it works with RoR2 Unity version, both Devotion and current at the time of writing (Alloyed Collective version 1.4.1) version, so no reason to update it.

This guide will not go into explanation on how to create the Thunderkit project, how to use ReplaceShaders script with Play mode or how to install Unity package. Use the links above to get started.

## The Sause

First of all, we need the `EliteRampPreview` script.

```csharp
using UnityEngine;

namespace EditorHelpers
{
	public class EliteRempPreview : MonoBehaviour
	{
    	#if UNITY_EDITOR
    	public static int EliteRampPropertyID = Shader.PropertyToID("_EliteRamp");

    	public static int EliteIndex = Shader.PropertyToID("_EliteIndex");

    	public Texture2D eliteRamp;

    	public Renderer renderer;

    	private MaterialPropertyBlock propertyBlock;

    	public void Awake()
    	{
        	propertyBlock = new MaterialPropertyBlock();
    	}

    	private void FixedUpdate()
    	{
        	SetProperties();
    	}

    	private void SetProperties()
    	{
        	renderer.GetPropertyBlock(propertyBlock);
        	propertyBlock.SetTexture(EliteRampPropertyID, eliteRamp);
        	propertyBlock.SetFloat(EliteIndex, 1);
        	renderer.SetPropertyBlock(propertyBlock);
    	}
    	#endif
	}
}
```

Save it to `EliteRampPreview.cs` and put it inside your `Scripts` folder.

I recommend doing shader preview on an empty scene, mainly for the purposes of it loading faster. Drag your model with proper material onto the scene.

<img width="1413" height="784" alt="image" src="https://github.com/user-attachments/assets/257f5f5f-7279-4032-9fea-ffcc740bf80f" />

Now add an `Elite Ramp Preview` component to your model, on the same object where your `Renderer` is. Add Renderer to it by dragging your `Renderer` to the `Renderer` field of `Elite Ramp Preview`.

<img width="477" height="738" alt="image" src="https://github.com/user-attachments/assets/e9df0664-196e-44fb-9e5a-716bac8410f2" />

Now we need to create our ramp. Right click in your Project window, select `Create -> Texture -> Gradient`. You now have a scriptable object that is used to create and export gradients in Unity Editor.

<img width="827" height="910" alt="image" src="https://github.com/user-attachments/assets/9cec8176-77e8-4de2-ace7-21670a536539" />

Set the `Resolution` to 256x8. Height doesn't actually matter, it is solely for preview. Untick `SRGB`. Set `Vertical Lerp` to the line that follows 0. You can achieve that first by selecting the line at the bottom, then by right-clicking each node on the line, selecting `Edit key` and setting 0 as value.

<img width="242" height="318" alt="image" src="https://github.com/user-attachments/assets/9f61df50-5920-4b38-a802-f003c175cece" />

`Horizontal Top` doesn't matter for us, we entirely remove it from our gradient by setting `Vertical Lerp` to 0. Our gradient will fully match `Horizontal Bottom`.

<img width="475" height="238" alt="image" src="https://github.com/user-attachments/assets/a5d2ab8c-f517-42e7-bb53-643bbae28a9f" />

Now click on an arrow next to our gradient in `Project` window and select the image. Set `Wrap Mode` to `Clamp` for the image.

<img width="835" height="940" alt="image" src="https://github.com/user-attachments/assets/d2fbe1c2-9361-4b7d-8414-15de643c1578" />

Now drag the gradient onto the `Elite Ramp` field of `Elite Ramp Preview` component. If it doesn't work (depends on Unity version), then drag the image that shows when you click the arrow.

<img width="481" height="121" alt="image" src="https://github.com/user-attachments/assets/23d45d20-1d3e-4461-8d6a-67d4b4b3cde5" />

Now you can press Play, wait for it to load and you will have a working elite ramp preview in Editor. You can live edit the gradient in the asset and it will reflect changes in real time.

<img width="910" height="512" alt="2026-05-25 13-47-59- 00 02 190-00 15 840" src="https://github.com/user-attachments/assets/9db4821b-d188-43c3-a085-f2b89871c52b" />

When you are done you need to export it to PNG by clicking the "Encode to PNG" button on the asset since the game won't understand what `GradientTexture` `ScriptableObject` is. When you export to PNG remember to check `sRGB`, set `Wrap Mode` to `Clamp` and uncheck `Generate Mip Maps` on the newly generated asset. Preview the ramp to make sure it is the same as the one in `GradientTexture`.

<img width="860" height="520" alt="image" src="https://github.com/user-attachments/assets/50a2be16-5b5b-47e9-bddb-1e991e5a7adc" />

Now clone your model on the scene (either by dragging a new one or pressing `Ctrl-D`) and apply a newly exported ramp to the clone. Start Play mode and ensure ramps match, this process allows you to double check that you selected all of the import settings correctly.

<img width="996" height="506" alt="image" src="https://github.com/user-attachments/assets/ad8b630c-6290-4a84-910a-6371f0bbd695" />

And once you are done I recommend removing `EliteRampPreview` from the model. While it basically won't do anything in game since all of the code is gated by UNITY_EDITOR it is still a useless component during runtime.

## FAQ

*Q: My model is basically solid color when previewing.*

<img width="696" height="518" alt="image" src="https://github.com/user-attachments/assets/525dd0ac-b893-4fc0-8e70-13c05efd3bb6" />

A: You forgot to set `Wrap Mode` to `Clamp`.

*Q: My gradient when saving to PNG is different from my gradient in `GradientTexture`.*

<img width="1106" height="524" alt="image" src="https://github.com/user-attachments/assets/bf445da1-4664-4f99-97d2-68dd776ce3a2" />

A: You didn't untick `SRGB` on `GradientTexture` asset or forgot to tick`sRGB` on PNG.
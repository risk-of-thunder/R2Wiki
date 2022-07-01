# Updating Your Mods for SotV
This page is a repository of information detailing information that is useful for developers updating mods.
*(Source: ROR2 Modding Discord, #development, "Modders Need to know" thread)*
## Other
* Survivors need to have `SurvivorDef.cachedName` set before contentpack loading starts for eclipse tracking to work properly
## R2API
* R2API is receiving updates to function with the changes to RoR2's assembly structure and content loading system
* R2API lost the following APIs:
`BuffAPI`, `BuffAPI`, `EffectAPI`, `FontAPI`, `ProjectileAPI`, `ResourcesAPI` (and its Providers), and `SurvivorAPI`, and `ItemDropAPI`.
	* Most of these APIs have been moved to R2API.ContentAdditon, and ItemDropAPI has been (mostly) replaced by RoR2.Run.
* (Enable/Disable)Pickup (this might get a small addition to ItemAPI later on that would allow you to enable/disable for all runs, but that won't be in the initial 4.0.0 release) 
Most of these APIs were already marked as obsolete and had their functionality moved elsewhere
* SoundAPI  has been affected but only the method for adding networksoundevents, since its been replaced by content addition
## Loading Assets
* The games asset resources are now accessible using Addressables. 
  * The old method of Resources.Load will work once the RoR2 BepInEx package has been updated to re-route Resources.Load calls to the LegacyResourcesAPI provided by HG
 * The `Shader.Find()` is replaced by the `LegacyShaderAPI.Find()` call in order to easily find shaders in their new homes, using the old paths
 * If you want to migrate your code, you can use `LegacyResourcesAPI` directly before Bepinex is updated by calling
`RoR2.LegacyResourcesAPI.Load` instead, you will need to reference the `LegacyResourcesAPI.dll` to do so
   * Alternatively you can update your code to use addressables, like follows
`Addressables.LoadAssetAsync<GameObject>(key: "RoR2/Base/WarCryOnMultiKill/WarCryEffect.prefab").WaitForCompletion()`
## References
* Code from `Assembly-CSharp.dll` has been moved mostly to `RoR2.DLL`
* If you're using `NetworkBehavior`, you will now need to reference `com.unity.multiplayer-hlapi.Runtime.dll`
* RoR2 is now on Unity `2019.4.26`, if you use the Editor you will need to update to `2019.4.26`
* If you use `CharacterMotor`, you will now need to reference `KinematicCharacterController.dll`
* The version of `PostProcessing` package RoR2 uses is version `2.3.0`
* The class `GameNetworkManager` has been renamed to `NetworkManagerSystem`
* Unfinished content, such as Wicked Ring or Ancestral Incubator are now in the class `JunkContent` instead of `RoR2Content`
* Wwise got updated to version `2019.2.12.7544`

## Addressables
When writing new code to load assets from RoR2, we can now use Addressables, [The following example shows how you can assign a material from a provided address (labeled Key in the code) to a MeshRenderer.](https://www.raywenderlich.com/14494028-introduction-to-modding-unity-games-with-addressables)

<details>
  <summary>Click to expand!</summary>

```csharp
[RequireComponent(typeof(MeshRenderer))]
public class AddressableLoader : MonoBehaviour
{
    public static string[] AvailableKeys;
    public string Key;
    private string LastKey;
    // Start is called before the first frame update
    void Update()
    {
        if (LastKey == Key) return;
        LastKey = Key;
        var materialOp = Addressables.LoadAssetAsync<Material>(Key);
        materialOp.Completed += MaterialOp_Completed;
    }
    void MaterialOp_Completed(AsyncOperationHandle<Material> obj)
    {
        var material = obj.Result;
        var renderer = GetComponent<MeshRenderer>();
        if (renderer && material)
        {
            renderer.material = material;
        }
    }
}
```
</details>

## Changing the Camera
Changes to ideallocalcamerapos now have to go through a handle and request like
<details>
  <summary>Click to expand!</summary>

```csharp
private CameraTargetParams.CameraParamsOverrideHandle handle;

public override void FixedUpdate()
{
    base.FixedUpdate();

    CameraTargetParams ctp = base.cameraTargetParams;
    CharacterCameraParamsData characterCameraParamsData = ctp.currentCameraParamsData;
    float denom = (1 + Time.fixedTime - this.initialTime);
    float smoothFactor = 8 / Mathf.Pow(denom, 2);
    Vector3 smoothVector = new Vector3(-3 / 20, 1 / 16, -1);
    characterCameraParamsData.idealLocalCameraPos = CameraPosition + smoothFactor * smoothVector;

    CameraTargetParams.CameraParamsOverrideRequest request = new CameraTargetParams.CameraParamsOverrideRequest
    {
        cameraParamsData = characterCameraParamsData,
        priority = 0,
    };
    
    handle = ctp.AddParamsOverride(request);
    base.cameraTargetParams.RemoveParamsOverride(handle);
}
```
</details>

CharacterCameraParamsData
<details>
<summary>Click to Expand!</summary>
```csharp
public BlendableFloat minPitch;
public BlendableFloat maxPitch;
public BlendableFloat wallCushion;
public BlendableFloat pivotVerticalOffset;
public BlendableVector3 idealLocalCameraPos;
public BlendableFloat fov;
public BlendableBool isFirstPerson;
```
</details>

## Crosshair
`CharacterBody.defaultCrosshairPrefab` is now readonly;
simple fix is to change `body.defaultCrosshairPrefab = crosshairPrefab` to `body._defaultCrosshairPrefab = crosshairPrefab` , 'improper' because that property is readonly and you technically aren't supposed to be changing it so your mileage may vary on side effects.
proper fix using the new system is `RoR2.UI.CrosshairUtils.RequestOverrideForBody(body,crosshairPrefab,RoR2.UI.CrosshairUtils.OverridePriority.Sprint);`

 
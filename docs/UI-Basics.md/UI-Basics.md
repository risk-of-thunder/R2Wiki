# Preface
Risk of Rain 2's UI uses Unity's UI System. As such, getting familiar with the Unity UI system is key.

Some mods may require a visual representation of data on screen for the user. For example, the amount of health left in the form of a health bar, the amount of ammo left, or maybe even a custom data.

# TL;DR about how Unity's UI works
`Rect`s are put onto a `Canvas`, and the entire `Canvas` is rendered over ***everything*** in the game. The `Canvas` resizes to the screen and resizes the `Rect`s in it as well to fit the new screen size.

# How-to

Assuming you've setup your coding environment (if not, please follow [this guide](https://github.com/risk-of-thunder/R2Wiki/wiki/%5BIn-depth%5D-First-mod)), let's get started.

## From a glance...
1. Hook onto `RoR2.HUD` (or any particular UI elements') `Awake` function and store the `HUD` instance.
1. Create a GameObject. Add your new GameObject to the `HUD` container. Add a `RectTransform` component to it, and set `anchorMin`, `anchorMax`, `sizeDelta` and `anchoredPosition` accordingly.
1. Add an `Image` component (load your sprite into `GetComponent<Image>().sprite`).

## In-depth...
### Step 1
> Hook onto `RoR2.HUD` (or any particular UI elements') `Awake` function and store the instance.

As a general example, we will hook to HUD.
```cs
private HUD hud = null;

private void Awake()
{
    On.RoR2.UI.HUD.Awake += MyFunc;
}
```
Now that we have hooked onto the HUD component, I just need to store the instance.
```cs
private void MyFunc(On.RoR2.UI.HUD.orig_Awake orig, RoR2.UI.HUD self)
{
    orig(self); // Don't forget to call this, or the vanilla / other mods' codes will not execute!
    hud = self; 
    //hud.mainContainer.transform // This will return the main container. You should put your UI elements under it or its children!
    // Rest of the code is to go here
}
```

Don't forget to unhook `OnDestroy`!
```cs
private void OnDestroy()
{
    On.RoR2.UI.HUD.Awake -= MyFunc;
}
```

### Step 2
> Create a GameObject. Add your new GameObject to the UI element instance. Add a `RectTransform` component to it, and set `anchorMin`, `anchorMax`, `sizeDelta` and `anchoredPosition` accordingly.

**This step is really important**: it determines where the UI element is going to be and anchor it there. For now, understand this:
- The anchor positions are ***normalized from 0 to 1***, and the bottom-left is `(0, 0)`, top-right is `(1, 1)`.
- Setting `anchoredPosition` is essentially moving the GameObject to translate by a certain amount, relative to its anchor.
- `sizeDelta` is the difference in size of the object relative to the anchors.

```cs
GameObject myObject = new GameObject("GameObjectName");
myObject.transform.SetParent(hud.mainContainer.transform);
RectTransform rectTransform = myObject.AddComponent<RectTransform>();
rectTransform.anchorMin = Vector2.zero;
rectTransform.anchorMax = Vector2.one;
rectTransform.sizeDelta = Vector2.zero;
rectTransform.anchoredPosition = Vector2.zero;
// You can safely omit the last line if the element is already where you want it to be.
// This collection of lines will fill the canvas with this Rect.
```

#### Unity Editor equivalent:
Creating `GameObject` with the name `GameObjectName`

![](https://cdn.discordapp.com/attachments/443014192899948545/690173620596768770/unknown.png)

Adding it to the `HUD` (typically a `Canvas`)

![](https://cdn.discordapp.com/attachments/443014192899948545/690188956930932796/unknown.png)

Adding a `RectTransform`

![](https://cdn.discordapp.com/attachments/443014192899948545/690173740080037932/unknown.png)

Setting its parameters

![](https://cdn.discordapp.com/attachments/443014192899948545/690186566181716182/unknown.png)


### Step 3
> Add an `Image` component (load your sprite into `GetComponent<Image>().sprite`).

```cs
GameObjectReference.AddComponent<Image>();
GameObjectReference.GetComponent<Image>().sprite = Resources.Load<Sprite>("textures/itemicons/texBearIcon");
// Utilize the ResourcesAPI from R2API to load your image!
```

#### Unity Editor equivalent:

Adding a `Image` component

![](https://cdn.discordapp.com/attachments/443014192899948545/690187998414897157/unknown.png)

Setting the sprite

![](https://cdn.discordapp.com/attachments/443014192899948545/690188096968327184/unknown.png)

# Go further

Get the Unity editor (it's free!), create a `Canvas` and start to create some `Image`s, see what you can do with the Unity UI system. Feel free to experiment and try to recreate the steps you took in code.

# References

- [Unity UI documentation (Packages)](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/index.html)
- [Unity UI documentation (Manual)](https://docs.unity3d.com/2017.3/Documentation/Manual/UISystem.html)
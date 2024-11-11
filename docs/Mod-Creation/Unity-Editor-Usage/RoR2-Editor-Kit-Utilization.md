# RoR2 Editor Kit Utilization


[RoR2EditorKit](https://github.com/risk-of-thunder/RoR2EditorKit/tree/main) is a powerful package maintained by the community that contains a robust API and utilities for helping the development of Editor related scripts. Thanks to its 5.0.0 update, RoR2EditorKit's core assembly which contains its API and main functionality does not require RoR2 to be within the editor nor ThunderKit to be installed, as such, it can be used in virtually any scenario to help your development of editor scripts.

This guide will cover common utilizations and tips with working with the API for your editor scripts.

![R2EKIcon](https://github.com/user-attachments/assets/3b97807e-fb6c-4665-b32b-980dda90729b)

## Installation

1. Go to ``Window/Package Manager`` to open the Unity Package Manager

![image](https://github.com/user-attachments/assets/46ac15bd-6def-4221-a3dc-f6df50c74ade)

2. Go to the [RoR2EditorKit Repository](https://github.com/risk-of-thunder/RoR2EditorKit/tree/main). Taking a look at the website you can see a big green ``<> Code`` button. Clicking it and then clicking the copy url button will copy the link to the repository.

![image](https://github.com/user-attachments/assets/704878e1-f077-4559-bc93-92b8cbbe6229)

3. On the Package Manager window, hit the ``+`` button, and click the ``Add package from Git URL...`` option

![image](https://github.com/user-attachments/assets/7562cea9-2d0a-4bc1-84e7-2f4ba00d2159)

4. Paste the git URL copied beforehand. As a side note, it is recommeneded to install only the latest tagged release from the repository. To install a specific release tag, append the Tag's name preceded by a ``#`` character.

For example, to download the version 5.4.0 of RoR2EditorKit to your project, you must use the git url ``https://github.com/risk-of-thunder/RoR2EditorKit.git#5.4.0``

![image](https://github.com/user-attachments/assets/0c4bfd53-5105-4a07-89a4-72a31e28c23a)

## Assembly structure

The RoR2EditorKit package consists of 4 main assemblies.

| Assembly Name | Features |
|--|--|
| Core | The core RoR2EditorKit assembly, it contains all the infrasturcture, framework and APIs for developing editor scripts, this assembly has no dependencies outside of the Editor Coroutines dependency, which gets installed atuomatically when you first install RoR2EditorKit.|
| R2API Support | A smaller assembly that contains editor related utilities when specific R2API assemblies are installed, such as support for proper DirectorAPI stage serialization and the AddressReferencedAsset system |
| RoR2 | The secondary assembly which contains all editor related utilities for RoR2 specifically, It mainly features inspectors, property drawers and windows. |
| ThunderKit | A smaller assembly that contains editor related utilities when ThunderKit is installed. |

Due to the nature of these assemblies, they only get enabled and compiled when specific requirements are met (IE: the ThunderKit assembly only enables if ThunderKit is Installed)

## RoR2EditorKit's base Editors

RoR2EditorKit comes bundled with a total of 7 inspector subclasses. These are the inspectors sorted in a hierarchy

### Inspector<T<T>> (T : UnityEngine.Object)

The ``Inspector<T>`` is the base class for any and all of RoR2EditorKit's editors, the package uses the word "Inspector" to refer to any ``UnityEditor.Editor`` for distinction purposes.

Features:
* Access to a EditorSettingCollection for the inspector for both Project and Preference settings
* A way to both enable and disable said inspector, enabling and disabling inspectors is one of the key features of RoR2EditorKit. When an inspector is disabled it uses the default inspector.
* Generic callbacks when the inspector is enabled or disabled
* Easy access to a root VisualElement
* Access to the edited object, casted to <T>
* Easy access for creating and setting Editor Settings.

If youre going to inherit from the Inspector class, you'll need to implement a way for the end user to enable or disable your custom inspector. alongside implementing your own UI utilizing the ``CreateInspectorUI``

### ScriptableObjectInspector<T<T>> (T : ScriptableObject)

The ``ScriptableObjectInspector<T>`` inherits from ``Inspector<T>`` and its used for creating an Inspector for a Scriptable Object.

Features:
* Implements a toggle for enabling and disabling the inspector, this toggle is placed on the Header of the inspector.

You'll still need to implement your UI manually, or alternatively, inherit from either of the 2 following inspector types.

#### IMGUIScriptableObjectInspector<T<T>> (T : ScriptableObject)

The ``IMGUIScriptableObjectInspector<T>`` inherits from ``ScriptableObjectInspector<T>`` and its used for creating an Inspector for a ScriptableObject using IMGUI.

Features:
* Handles the creation of an IMGUIContainer which will be used to render your UI

You can implement your IMGUI UI inside the ``DrawIMGUI()`` method.

#### VisualElementScriptableObjectInspector<T<T>> (T: ScriptableObject)

The ``VisualElementScriptableObjectInspector<T>`` inherits from ``ScriptableObjectInspector<T>`` and its used for creating an Inspector for a ScriptableObject using Visual Elements.

Features:
* Automatically obtains an UXML instance based off the inspector's name, this UXML instance will be used as the actual UI, which then the inspector passes to an abstract method for initialization.
* A method that you can override to validate if a specified path corresponds to your inspector.

### ComponentInspector<T<T>> (T : Component)

The ``ComponentInspector<T>`` inherits from ``Inspector<T>`` and its used for creating an Inspector for a Component or MonoBehaviour.

Features:
* Implements a toggle for enabling and disabling the inspector, this toggle is placed right at the beginning of the component's inspector UI

You'll still need to implement your UI manually, or alternatively, inherit from either of the 2 following inspector types.

#### IMGUIComponentInspector<T<T>> (T : Component)

The ``IMGUIComponentInspector<T>`` inherits from ``ComponentInspector<T>`` and its used for creating an Inspector for a Component or MonoBehaviour using IMGUI.

Features:
* Handles the creation of an IMGUIContainer which will be used to render your UI

You can implement your IMGUI UI inside the ``DrawIMGUI()`` method.

#### VisualElementComponentInspector<T<T>> (T : Component)

The ``VisualElementComponentInspector<T>`` inherits from ``ComponentInspector<T>`` and its used for creating an Inspector for a Component or MonoBehaviour using Visual Elements.

Features:
* Automatically obtains an UXML instance based off the inspector's name, this UXML instance will be used as the actual UI, which then the inspector passes to an abstract method for initialization.
* A method that you can override to validate if a specified path corresponds to your inspector.

## RoR2EditorKit's base Property Drawers

RoR2EditorKit comes bundled with a total of 3 PropertyDrawer subclasses. These are the PropertyDrawer sorted in a hierarchy

### ExtendedPropertyDrawer<T<T>>

The ``ExtendedPropertyDrawer<T>`` is the base class for any and all of RoR2EditorKit's PropertyDrawers.

The TypeParam <T> can be used to specify what kind of value this PropertyDrawer is Drawing. For example, you can use <SkillDef.Variant> to specify that the Property drawer draws a Variant Skill Def. Or you can use <SomePropertyAttribute> to specify the PropertyDrawer is used for drawing a PropertyAttribute (IE: [Range])

Features:
* Access to a EditorSettingCollection for the PropertyDrawer for both Project and Preference settings
* Easy access to the Data youre drawing by using the ``propertyDrawerData`` property.
* Easy access for creating and setting Editor Settings.

#### IMGUIPropertyDrawer<T<T>>

The ``IMGUIPropertyDrawer<T>`` inherits from ``ExtendedPropertyDrawer<T>``and its used for drawing Property Drawers using IMGUI.

Features:
* Utility methods for drawing properties using the non automatic layout GUI system

Due to the nature of IMGUI in PropertyDrawers, you cannot use ``EditorGUILayout`` for drawing IMGUI, you'll need to use ``EditorGUI`` instead.

#### VisualElementPropertyDrawer<T<T>>

The ``VisualElementPropertyDrawer<T>`` inherits from ``ExtendedPropertyDrawer<T>`` and its used for creating a PropertyDrawer using Visual Elements

Features:
* Automatically obtains an UXML instance based off the PropertyDrawer's name, this UXML instance will be used as the actual UI, which then the PropertyDrawer passes to an abstract method for initialization.
* A method that you can override to validate if a specified path corresponds to your PropertyDrawer.

## RoR2EditorKit's base EditorWindows

### TODO

## The Editor Setting system

### TODO

## RoR2EditorKit Utilities

### TODO
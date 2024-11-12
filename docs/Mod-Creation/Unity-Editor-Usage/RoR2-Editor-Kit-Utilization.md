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

RoR2EditorKit comes bundled with a total of 5 EditorWindow subclasses. These are the EditorWindow  sorted in a hierarchy

### ExtendedEditorWindow

The ``ExtendedEditorWindow`` is the base class for any and all of RoR2EditorKit's Editor Windows.

Features:

* Access to a EditorSettingCollection for the Window for both Project and Preference settings
* Implementation of Serialized Object binding for ease of creation of UI
* Generic callbacks for Serialized Object manipulation
* Custom Open method for assigning the Serialized Object
* Easy access for creating and setting Editor Settings.

If youre going to inherit from the ExtendedEditorWindow class, you'll need to implement your own UI utilizing the ``CreateGUI`` method

### ObjectEditingEditorWindow<T<T>> (T : UnityEngine.Object)

The ``ObjectEditingEditorWindow<T>`` inherits from ``ExtendedEditorWindow<T>`` and it's used for creating an Editor Window for editing a single object.

This type is especially useful for creating a complex UI for editing the specified object and making said editing experience better for the end user

Features:
* Direct access to the editing object by using the ``targetType`` attribute.

#### IMGUIObjectEditingEditorWindow<T<T>> (T : UnityEngine.Object)

The ``IMGUIObjectEditingEditorWindow<T>`` inherits from ``ObjectEditingEditorWindow<T>`` and it's used for creating an Editor Window for editing a single object using IMGUI

Features:

* Allows the creation of the UI using only IMGUI

#### VisualElementObjectEditingEditorWindow<T<T>> (T : UnityEngine.Object)

The ``VisualElementObjectEditingEditorWindow<T>`` inherits from ``ObjectEditingEditorWindow<T>`` and it's used for creating an Editor Window for editing a single object using Visual Elements

Features:

* Automatically obtains an UXML instance based off the EditorWindow's name, this UXML instance will be used as the actual UI, which then the EditorWindow passes to an abstract method for initialization.
* A method that you can override to validate if a specified path corresponds to your EditorWindow.

### EditorWizardWindow

The ``EditorWizardWindow`` inherits from ``ExtendedEditorWindow`` and its used for creating complex wizard setups for aiding the end user in development of assets in a semi-asynchronous fashion utilizing Coroutines.

Unlike ``ObjectEditingEditorWindow``s, an EditorWizardWindow works exclusively with Visual Elements, albeit IMGUI can still be used by using ``IMGUIContainers``

Features:

* A Header, Center and Footer pre-created by RoR2EditorKit that displays the wizard's name, your UI content, and buttons for closing and running the wizard
* A Progress bar that can be used to accurately display the progress of the wizard
* Automatically obtains an UXML instance based off the EditorWizardWindow's name, this UXML instance will be used as the actual UI, which then the EditorWizardWindow passes to an abstract method for initialization.
* Robust implementation of asynchronous code utilizing Coroutines thanks to the ``EditorCoroutines`` package.

Alongside the EditorWizardWindow, RoR2EditorKit comes with the WizardCoroutineHelper, which is used to maintain complex, step-based wizard setups (Coroutine for writing text to a file, then a secondary step that does something with said file)

## The Editor Setting system

One of the Key Features of RoR2EditorKit is its robust Settings system, the Editor Setting System allows you to save metadata to specific files that contains settings related to misc Types within the editor. Thanks to a powerful string based serialization system, the Editor Settings system can serialize a multitude of types, such as Strings, integer, chars, colours, animation curves, etc.

![image](https://github.com/user-attachments/assets/4ce52a1c-171c-4465-94e8-89cd8254c2d4)

All of the previously mentioned systems (Inspectors, Editor Windows and PropertyDrawers) have access to the EditorSetting system by either using the properties ``xProjectSettings`` or ``xPreferenceSettings``

By default, RoR2 Editor Kit comes with 2 types of Editor Settings.

| Editor Setting Type | Description |
|--|--|
| Project Settings | Located within your specified project, these settings are bound exclusively within the project itself, and are not shared between project instances. |
| Preference Settings | These settings are bound to your machine, and are used across different instances of RoR2EditorKit, a key example is that inspectors being enabled or disabled are considered a "Preference" setting, this way, user A can have the inspector enabled while user B can have it disabled. |

The Editor Settings system is split in 4 key types.

### EditorSettingCollection

The ``EditorSettingCollection`` is a serializable class which as the name suggests, represents a collection of Editor Settings for a specified Type.
A SettingCollection will always have an owner variable which is obtained from the ``System.Type`` that requested the creation of it, albeit in some scenarios the Type could not exist in the project (IE: Its a preference setting from a type thats not present in the current project)

It contains methods for Obtaining or Creating new settings utilizing ``GetOrCreateSetting``, alongside simply assigning setting values and saving the settings.

Editor Setting Collections are stored in what are called ``IEditorSettingProvider``s.

### IEditorSettingProvider

The ``IEditorSettingProvider`` is an interface that can be implemented in an object to mark it as a provider of Editor Settings. It contains the following members to implement:

* A ``List<EditorSettingCollection>``, which are the actual settings stored by the provider.
* A ``string`` property, which is used to represent the provider with a unique identifier name.
* A ``void SaveSettings()`` method, which is used to save the settings for the provider to disk.

RoR2EditorKit comes bundled with two providers, the Project and Preference settings. These are ScriptableSingletons. for more information on scriptable singletons, refer to [this](https://docs.unity3d.com/2021.3/Documentation/ScriptReference/ScriptableSingleton_1.html) page.

IEditorSettingProviders can be obtained by using the ``EditorSettingManager``

### Editor Setting Manager

The ``EditorSettingManager``is a static class that manages providers and works as a front-end for obtaining ``EditorSettingCollection``s.

It contains methods for getting or creating new instances of EditorSettingCollections for specified ``System.Type`` and can be stored in specified Providers. Providers can be specified by either an Enum to represent the default Providers that come bundled with RoR2EditorKit, or via a string for custom providers.

The manager also exposes methods for Resetting and Removing entire editor setting collections, or even resetting entire providers. Careful utilization of these methods must be used as you can easily wipe preference settings by mistake.

### Implementing a custom EditorSettingProvider

This part will cover how to implement your own EditorSettingProvider to the manager. below is an example of one using a ScriptableSingleton.

    public sealed class CustomEditorSettings : ScriptableSingleton<CustomEditorSettings >, EditorSettingManager.IEditorSettingProvider
    {
        [SerializeField]
        private List<EditorSettingCollection> _projectSettings = new List<EditorSettingCollection>();

        List<EditorSettingCollection> EditorSettingManager.IEditorSettingProvider.editorSettings => _projectSettings;
        string EditorSettingManager.IEditorSettingProvider.providerName => nameof(CustomEditorSettings);
        public void SaveSettings() => Save(true);

        private void Awake()
        {
            EditorApplication.quitting += EditorApplication_quitting;
        }

        private void EditorApplication_quitting()
        {
            Save(true);
        }

        private void OnDestroy()
        {
            EditorApplication.quitting -= EditorApplication_quitting;
        }

        [InitializeOnLoadMethod]
        private static void Init()
        {
            EditorSettingManager.RegisterProvider(instance, () => instance);
        }
    }

Some things to note:

* The InitializeOnLoad decorated attribute is used for initializing and adding the instance of the ScriptableSingleton to the EditorSettingManager. which is how you can expose your provider to the system.
* For a ScriptableSingleton to work properly in the editor you need to decorate the class with a ``[FilePath]`` attribute, more information about this attribute can be found [here](https://docs.unity3d.com/2021.3/Documentation/ScriptReference/FilePathAttribute.html)

### Implementing an UI for a provider

You can utilize the ``EditorSettingsElement`` to expose a provider's editor settings, this can be achieved by using the following constructor

``new EditorSettingsElement((EditorSettingManager.IEditorSettingProvider)settings));``

Afterwards adding the element to any Visual Element will display the provider's settings in a UI

## RoR2EditorKit Useful Classes

The core library of RoR2EditorKit comes bundled with multiple classes that can aid in the development of editor scripts.

| Class Type | Description |
|--|--|
| AssetDatabaseUtil | Contains utility methods for interacting with the AssetDatabase, such as directly obtaining an asset's guid, directly loading an asset via its GUID, Finding Assets |
| AssetGUID<T> | A struct that is used for encapsulating an Asset via its GUID, useful for keeping constants of assets. these assets can then be loaded using the implicit casting operators. |
| ElementContextMenuHelper | A static class that works as an extension for Visual Elements, it allows you to easily add context menus to any element that are visible via an icon that's added alongside the element. |
| IMGUIUtil | A static class that contains methods for drawing controls using IMGUI in an automatic layout context, it contains methods like drawing a property with a check system, and drawing field based on their type. It also exposes a method for drawing a field for an EditorSetting inside a collection. |
| IOUtils | Contains utility methods for interacting with Paths, such as ensuring directories, obtaining currently selected directories and manipulating paths. |
| R2EKMath | A simple math class used for arithmetic operations, including the ability to round vectors to the nearest grid value rounding vectors and a remap function |
| ReflectionUtils | A static class which contains reflection utilities. |
| SerializableShaderWrapper | A class used for serializing Shader objects as shader name or guid. |
| SerializationMediator | A front-end facade for the Serialization system within RoR2EditorKit, it can be used to match the game's serialization methods for usage on runtime solutions. An optional safety bypass exists as a sub-class which should only be used for Editor related solutions. |
| VisualoElementEditorSettingHelper | A static class that works as an extension for Visual Elements, it allows you to directly connect a VisualElement to an Editor Setting. When the value of the Visual Element changes, the value within the editor setting changes as well. |
| VisualElementTemplateDictionary | A ScriptableSingleton that contains a dictionary of string to Visual Element Tempaltes, you can use this to find a UXML Template by name |
| VisualElementUtil | A static class containing a plethora of extension utilities used for Visual Elements |
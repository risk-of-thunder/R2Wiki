# Editor Scripts

An Editor Script is a C# script that runs inside the Unity Editor, these scripts can be done to basically *extend* the functionality of Unity's powerful editor. An Editor script can be useful to automate an annoying part of development or improve the user experience of complex Components and Scriptable Objects.

One such example of an Editor Script is the custom Inspector of ``EntityStateConfiguration`` that comes bundled with RoR2EditorKit. Thanks to this inspector, utilizing Entity State Configurations is easy and convenient, without having to manually add the fields to serialize or guess the proper string formatting for serialization.

(Left is the default look of the ``EntityStateConfiguration``, right is how it looks with RoR2EditorKit's inspector)

![image](https://github.com/user-attachments/assets/481ef593-7faa-450f-ba6c-f6ae7b8be7b2)

## Glossary

| Term | Definition |
|--|--|
| Editor Script | A regular C# script that contains editor related code. These are usually bundled with a separate Editor only assembly. |
| SerializedObject | a SerializedObject is a special type of class that represents a UnityEngine.Object in a serialized format. Editors and multiple editing related scripts utilizes them since it allows to represent virtually *any* UnityEngine.Object in a generic way. |
| SerializedProperty | A SerializedProperty is a property within a SerializedObject that contains the actual data of an Object, for example, a class with a public string field named "myString" has a SerializedProperty of name "myString" that contains the actual stored value. Serialized properties can have nested properties, such as in the case of a custom Serializable data structure. |
| Editor | An "Editor" is a custom class that can be inherited to create custom Inspector for objects. These objects are edited utilizing their SerializedObject representations. |
| PropertyDrawer | A custom class that Draws a GUI for a type of SerializedProperty. |
| Editor Window | An actual, dockable and movable window that can be used inside the editor, these windows can have custom scripts related to them. |
| UIElements | UIElements are a custom, special class inside unity thats used to represent user interfaces, these elements in modern versions of unity are mostly used to replace the old UI systems of unity. Its built in a very similar structure to Web Development, where UIElements can be queried utilizing the UQuery system. (CSS, JS, HTML) |
| UXML | An UXML file is a file that represents the hierarchy and layout of different UIElements. Its quite similar to HTML in web development. |
| USS | A USS file is a file that represents the visual style of UIElements inside their hierarchy and layout. Its quite similar to CSS in web development. |
| IMGUI | The traditional way of writing editor UI is via IMGUI, which means basically programming your inspectors and UI. Most editors and inspectors in unity still utilize IMGUI. |

## Creating an Editor Assembly

By default, any script that's within an "Editor" folder is considered an Editor Script, and will be added to a special assembly called "Assembly-CSharp-Editor". We recommend instead of utilizing the default editor assembly to create a custom Assembly Definition. More information about Assembly Definitions can be found [here](https://docs.unity3d.com/2021.3/Documentation/Manual/ScriptCompilationAssemblyDefinitionFiles.html)

To create a new Assembly Definition, right click in the project window and select ``Create/AssemblyDefinition``

![image](https://github.com/user-attachments/assets/2dcc82dd-e586-4c78-9ec7-64d2af8e9c1d)

To properly configure your Assembly Definition to be used within the editor, we need to select **__ONLY__** the "Editor" platform under the AssemblyDefinition's ``Platforms`` section. Failing to do so can and will cause issues when building things such as Scripts and AssetBundles.
An easy way to do this is by:
1. Untick ``Any Platform``
2. Click ``Deselect All``
3. Tick ``Editor``

![image](https://github.com/user-attachments/assets/625ea1eb-6de2-4e07-aedd-894548f89524)

## My first inspector

We will begin by creating a custom editor for a component. this inspector will have a button that does something when clicked. for simplicity sake we will utilize IMGUI to create our UI. We begin by creating a new script inside our editor assembly, instead of making it inherit from MonoBehaviour we will make it inherit from ``UnityEditor.Editor``

![image](https://github.com/user-attachments/assets/a456277b-4912-446b-ade0-aeed2352bd92)

The editor class has a main method we can override called ``OnInspectorGUI``, the base method of this method draws the default inspector, so for our purposes we will keep the ``base`` call. To add our new button, we can utilize the class ``GUILayout``'s method ``Button``. The method returns a ``bool`` that determines if the button was clicked or not, so we can just use an ``if`` statement to run code when the button is clicked.

The class EditorUtility contains a bunch of utility methods within the editor, we will use this to create a popup dialog that informs the user they clicked the button.

Finally, we need to tell unity what object this editor is for, we utilize the ``CustomEditor``attribute for specifying what object this editor is for.

![image](https://github.com/user-attachments/assets/e6422478-8c19-4b28-adae-b07a4a23e7c8)

Now if you inspect the specified type, you can see that its inspector window has a new button, and clicking it will display our custom dialog.

![image](https://github.com/user-attachments/assets/62ab5f2b-c0f1-4166-9963-c7e455286e05)

(Note, you can draw the default inspector at any point in time by utilizing ``DrawDefaultInspector()``)

## Useful Classes

| Class Type | Description |
|--|--|
| AssetDatabase | The AssetDatabase represents a database of all the assets within the editor, this can be used to load specific assets, manage them, save in memory objects to assets, etc |
| Editor | The base class for any and all inspector related classes, the Editor class can be used to write custom inspectors for objects. |
| EditorApplication | A static class that represents the UnityEditor.exe instance currently open. |
| EditorGUI | Class used for writing GUI code for the editor, it utilizes Serialzied Properties and Serialized Objects, the GUI written in here does not layout automatically, for automatic layout, use the class below |
| EditorGUILayout | A class used for writing GUI code for the editor, which automatically layouts to simplify writing complex windows and inspectors. |
| EditorGUIUtility | A general utility class that contains various utility methods for the unity editor's GUI system |
| EditorUtility | A general utility class that contains various utility methods for general editor usage. |
| EditorWindow | The base class for any and all editor windows. |
| FileUtil | Class used to manipulate directories and files, which can be used to copy, move, delete assets and directories. |
| InitializeOnLoadMethodAttribute | An Attribute that can be applied to static methods, methods with this attribute will be executed every time a domain reload occurs in the editor. Useful for setting up underlying systems |
| MonoScript | Representation of a .cs file within the unity editor |
| ObjectNames | Class used to create displayable names in the editor |
| PrefabUtility | Class that contains utility methods for handling and managing prefab assets |
| PropertyDrawer | A custom class that Draws a GUI for a type of SerializedProperty. |
| Selection | A class that can be used to get the selected object or assets in the editor. |
| SerializedObject | a SerializedObject is a special type of class that represents a UnityEngine.Object in a serialized format. Editors and multiple editing related scripts utilizes them since it allows to represent virtually *any* UnityEngine.Object in a generic way. |
| SerializedProperty | A SerializedProperty is a property within a SerializedObject that contains the actual data of an Object, for example, a class with a public string field named "myString" has a SerializedProperty of name "myString" that contains the actual stored value. Serialized properties can have nested properties, such as in the case of a custom Serializable data structure. |
| Undo | Access to the Undo/Redo system |

## Developing editor scripts with RoR2EditorKit

[RoR2EditorKit](https://github.com/risk-of-thunder/RoR2EditorKit/tree/main) is a powerful package maintained by the community that contains a robust API and utilities for helping the development of Editor related scripts. Thanks to its 5.0.0 update, RoR2EditorKit's core assembly which contains its API and main functionality does not require RoR2 to be within the editor nor ThunderKit to be installed, as such, it can be used in virtually any scenario to help your development of editor scripts. We will use this to re-create our previous inspector utilizing UIElements.

![R2EKIcon](https://github.com/user-attachments/assets/3b97807e-fb6c-4665-b32b-980dda90729b)

### Installation

1. Go to ``Window/Package Manager`` to open the Unity Package Manager

![image](https://github.com/user-attachments/assets/46ac15bd-6def-4221-a3dc-f6df50c74ade)

2. Go to the [RoR2EditorKit Repository](https://github.com/risk-of-thunder/RoR2EditorKit/tree/main). Taking a look at the website you can see a big green ``<> Code`` button. Clicking it and then clicking the copy url button will copy the link to the repository.

![image](https://github.com/user-attachments/assets/704878e1-f077-4559-bc93-92b8cbbe6229)

3. On the Package Manager window, hit the ``+`` button, and click the ``Add package from Git URL...`` option

![image](https://github.com/user-attachments/assets/7562cea9-2d0a-4bc1-84e7-2f4ba00d2159)

4. Paste the git URL copied beforehand. As a side note, it is recommeneded to install only the latest tagged release from the repository. To install a specific release tag, append the Tag's name preceded by a ``#`` character.

For example, to download the version 5.4.0 of RoR2EditorKit to your project, you must use the git url ``https://github.com/risk-of-thunder/RoR2EditorKit.git#5.4.0``

![image](https://github.com/user-attachments/assets/0c4bfd53-5105-4a07-89a4-72a31e28c23a)

## Creating an inspector using UIElements

To create an inspector using UIElements we recommend utilizing the editor tool ``UIBuilder``. Access to the UIBuilder can be found by clicking "Window/UI Toolkit/UI Builder". The manual entry for UIBuilder can be found [here](https://docs.unity3d.com/2021.3/Documentation/Manual/UIBuilder.html)

![image](https://github.com/user-attachments/assets/bac8a6e4-ee66-4874-af66-1bf3c9013b84)

Note that by default, UIBuilder only exposes Runtime based controls, to get access to all controls for editor usage, click the main UXML file in the hierarchy and tick "Editor Extension Authoring". You can also enable it by default by going to the UIToolkit Project Settings

![image](https://github.com/user-attachments/assets/7aad0cb4-9ca2-4573-ae9b-d6185d57328c)
![image](https://github.com/user-attachments/assets/95d8fff1-2cf6-4cd6-b522-19daf9e7cf7e)

We can create the UI of our inspector by simply utilizing the "PropertyField" element, this element will draw the correct control for a field with the given name.

![image](https://github.com/user-attachments/assets/8df125d0-a25f-4880-bb38-d636a6602809)

Something worth mentioning is that a lot of UIElements have a property called "Binding Path", the Binding Path is used in the editor to bind the values and states of a Serialized Property to a specific UIElement, to bind an element to a property, set "BindingPath" to the field's name.

![image](https://github.com/user-attachments/assets/918ce29a-b9f4-43a9-b139-fa3f2400df27)

For the button we can simply drag and drop the Button element into our UXML file.

![image](https://github.com/user-attachments/assets/33477ebc-0239-4098-a66d-4109703f53e3)

You may notice that all UIElements have a section called "Style", the "Style" of the UIElement is used to modify how an element looks and well, its style. These styles can be inlined, or written as actual style classes stored inside a USS file.

![image](https://github.com/user-attachments/assets/5c66332f-de7e-4aaf-8daf-b46bf5163a52)

You can now save the UXML file, which will be how our inspector is laid out and how it looks. The name of the UXML file should match the name of the inspector class youre going to create.

![image](https://github.com/user-attachments/assets/d91fef56-ade2-46dd-87f3-8eca2473853c)

Now, we can start giving our UI a proper behaviour and have it appear as the UI of our custom inspector, while you can make your inspector inherit from ``Editor``and override ``CreateInspectorGUI``, unity by default does not provide any tooling for obtaining an inspector's UXML file and instantiating it. For this case, we will levrage the custom inspector setup in RoR2EditorKit. to differentiate the ``UnityEditor.Editor`` class from the custom subclasses in RoR2EditorKit, RoR2EditorKit calls them ``Inspector``. For our specific usage which is for creating an inspector for a ScriptableObject, we will inherit from ``VisualElementScriptableObjectInspector``

![image](https://github.com/user-attachments/assets/7271fb18-631d-4a76-98c1-b852e3f84487)

Inside the class we can see we're required to implement the ``InitializeVisualElement`` method, RoR2EditorKit calls this method after creating an instance of your UXML file and assigning it to your inspected object, and as such, ``templateInstanceRoot`` represents the actual root of your UXML document. We can utilize the extension method ``.Q`` to quickly query and obtain a Visual Element that matches the given filters. In the following code, the Query statement tells unity to "Find a UIElement of type 'Button' that has the name 'MyButton' and return it".

The ``Button`` element has a ``clicked`` event that we can use to run code when the button is clicked.

Lastly, we need to implement an override to the ``ValidatePath`` method, since there can be situations where multiple UXML files have the same name, RoR2EditorKit uses this method to check if the path to an UXML file is the correct path to its inspector. The default behaviour is that it only checks if the path contains the package's name. For our use case, we can just do the same but replace the package name with a directory that's parent of our UXML file.

![image](https://github.com/user-attachments/assets/68ca40fc-3c14-44af-8232-06cf1abf1baf)

The resulting UI gives an inspector that separates the layout and look of the UI and it's functionality, And still works as the IMGUI inspector.

![image](https://github.com/user-attachments/assets/da7caad0-ecbd-446c-9762-482614315d51)

For an indepth explanation of the utilities provided by RoR2EditorKit, check [this](https://risk-of-thunder.github.io/R2Wiki/Mod-Creation/Unity-Editor-Usage/RoR2-Editor-Kit-Utilization/) wiki page.

## Utilizing editor code within Runtime code

You may stumble into the necesity of running Editor related code in a regular, runtime level MonoBehaviour or ScriptableObject. This scenario can be solved by utilizing conditional compilation using pre-processor directives.

Take for example, this OnValidate method that needs to save the asset after its been modified by some user.

    private void OnValidate()
    {
        if(someCondition)
        {
            _hidenInInspectorValue = 0.4f;
            //Find a way to save the above change somehow
        }
    }

To ensure proper saving of our modified asset, we can utilize ``EditorUtility.SetDirty``. To properly utilize this method without causing issues at build time we can encapsulate the statement inside an ``#if UNITY_EDITOR`` pre-processor, and fully qualify the type name ``EditorUtility``

    private void OnValidate()
    {
        if(someCondition)
        {
            _hidenInInspectorValue = 0.4f;
    #if UNITY_EDITOR
            UnityEditor.EditorUtility.SetDirty(this);
    #endif
        }
    }

Of course, it is worth mentioning that any code encapsulated by the ``#if UNITY_EDITOR`` statement will not be present in builds of your content.

## IMGUI UI in Visual Elements

Sometimes you may encounter the need to utilize IMGUI inside an UI created with Visual Elements. This can be due to some limitation of UIElements that hasnt been developed yet, or for ease of utilization of certain features such as dropdown menus.

For situations like this, you can utilize the ``IMGUIContainer`` element, which is a special VisualElement that can be used to draw UI using IMGUI.

    public void SetupElement(VisualElement rootElement)
    {
        rootElement.Add(new IMGUIContainer(MyIMGUIMethod);
    }

    private void MyIMGUIMethod()
    {
        EditorGUILayout.LabelField("This label was written using IMGUI!");
    }

Due to limitations, you cannot use Visual Elements in an IMGUI UI
# Thunderkit - Using the DOTS framework in Modding

The Data Oriented Technology Stack (otherwise known as the DOTS framework) is a development philososphy and technology tools designed for creating fast performant code by utilizing Data oriented programming. which allows for highly efficient and performant code.

The DOTS framework is built on three key pillars, of which 2 are accessible and one is unknown if it can be used in modding.

| Framework Part | Description | Availability |
|--|--|--|
| ESC - Entity Component System | The ECS is a framework that works on a purely data driven design, where it has Entities that populate scenes, but lack data and behaviour, components which hold the data in an Entity, and Systems, which transform the data of components in a deterministic way | Unknown |
| Burst Compiler | Burst is a compiler that can be used alongside the Job system to create code that improves performance by translating your IL/.Net bytercode into native CPU code using the LLVM compiler. | Available, known method via Unity and ThunderKit |
| C# Jobs | Allows you to create multithreaded code for performance intensive tasks, such as multiple transform modifications, complex mathematic operations and more. Has built in guard rails for development of safe multithreaded code | Available |

This guide will not cover how to use the Burst Compiler or how to Write C# jobs, it'll only cover how to get access to the framework on a ThunderKit context and how to compile your mod

## Burst and Jobs in RoR2

Utilizing Jobs is an already by default implemented feature of the current unity version, however, We've made the mod [BurstsOfRain](https://thunderstore.io/package/RiskofThunder/BurstsOfRain/) for the purposes of extending the development potential by including extra libraries for running burst compiled Jobs and access to improved Native Collections such as Native Lists, Native Hash Maps and more.

![image](https://github.com/user-attachments/assets/5adb67af-82f7-46a6-90bf-d5fa2f7b8832)

Bursts of Rain is a thunderstore package that contains a lightweight assembly that can be used for loading burst-code assemblies, however, it also comes with re-distribution of specific unity DLL's that are loaded which can be used in jobs.

| Redistributed Assembly | Description | Version |
|--|--|--|
| System.Runtime | Required for the collections package. | Not Applicable |
| [Unity.Burst](https://docs.unity3d.com/2021.3/Documentation/Manual/com.unity.burst.html) | The runtime front-end of the Burst compilation system, this runs the bursted code when applicable. | 1.8.18 |
| [Unity.Collections](https://docs.unity3d.com/2021.3/Documentation/Manual/com.unity.collections.html) | A package that contains multiple, thread safe, native collections that can be used within jobs, such as Lists, Queues, Hashmaps and more. | 1.5.1 |
| [Unity.Mathematics](https://docs.unity3d.com/2021.3/Documentation/Manual/com.unity.mathematics.html) | A light-weight math library with a shader-like syntax which can be safely used within jobs. | 1.2.6 |

As a note, it is _**NOT**_ recommended to install Bursts of Rain directly with the ThunderKit package manager for the following reasons:

* Unity will not recognize the redistributed unity assemblies, this is an intentional feature.
* You'll have to manually install the selected versions of Burst, Collections and Mathematics.
* The collections package has a dependency on Mono.Cecil, which makes modding difficult.

For these reasons, this guide will cover installing Bursts of Rain using the [RoR2 Thunder Burster](https://github.com/risk-of-thunder/RoR2ThunderBurster) package.

## Installing RoR2 Thunder Burster

It is recommended to do a full game re-import once the package is installed. to do this, select all folders within your project's Packages folder (``ProjectRoot/Packages``) except for ``manifest.json`` and ``packages-lock.json``. and Delete the folders.

![image](https://github.com/user-attachments/assets/2e09eed6-4032-420d-b9a0-bfaa4c0f23ec)

1. Go to ``Window/Package Manager`` to open the Unity Package Manager

![image](https://github.com/user-attachments/assets/46ac15bd-6def-4221-a3dc-f6df50c74ade)

2. Go to the [RoR2ThunderBurster Repository](https://github.com/risk-of-thunder/RoR2ThunderBurster). Taking a look at the website you can see a big green ``<> Code`` button. Clicking it and then clicking the copy url button will copy the link to the repository.

![image](https://github.com/user-attachments/assets/71d65b39-4efe-498c-99a4-b3602e2f5f39)

3. On the Package Manager window, hit the ``+`` button, and click the ``Add package from Git URL...`` option

![image](https://github.com/user-attachments/assets/7562cea9-2d0a-4bc1-84e7-2f4ba00d2159)

4. Paste the git URL copied beforehand. As a side note, it is recommeneded to install only the latest tagged release from the repository. To install a specific release tag, append the Tag's name preceded by a ``#`` character.

For example, to download the version 0.0.2 of RoR2 Thunder Burster to your project, you must use the git url ``https://github.com/risk-of-thunder/RoR2ThunderBurster#0.2.2``

![image](https://github.com/user-attachments/assets/0c6b43e8-2b4f-4346-89bc-5fcb866ba88c)

5. Once installed, you may get a popup regarding missing dependencies, clicking on "Install All" will install all the listed packages. it may take a while for the editor to finish installing your dependencies.

![image](https://github.com/user-attachments/assets/13074990-6be5-45fd-a05c-dd46d58b28a9)

6. You may stumble upon this popup once the packages are installed, click "Ok" to proceed. If this popup does not appear or your project ends in an invalid state, refer to the next section.

![image](https://github.com/user-attachments/assets/ad657793-7f92-4d0b-9942-17b219bfc473)

7. Open the ThunderKit settings window, and go to the import config, you may notice that a new import step for installing Bursts of Rain has appeared, reimport your game and youre ready to go.

![image](https://github.com/user-attachments/assets/c5e3db64-cdd2-4487-875d-83af2156f731)

### Project Troubleshooting

If you installed RoR2ThunderBurster after you've imported the game your project might end up in an unstable/unusable state, this is because the Collections package comes with an explicit dependency to Mono.Cecil, which gets installed and conflicts with our BepInEx mono.cecil assemblies. To fix this, you can do the following steps.

1. Go to ``ProjectRoot/Library/PackageCache`` find the folder ``com.unity.collections@1.5.1``

![image](https://github.com/user-attachments/assets/d7d60e7c-5dd0-4371-b463-c828beaf8c00)

2. Copy this folder and paste it on ``ProjectRoot/Packages`` and remove the substring ``@1.5.1``, this Embeds the package into your project and can now be modified.

![image](https://github.com/user-attachments/assets/e3f2168f-783f-455c-81ba-a855e823eda9)

3. Download this [Zip](https://github.com/user-attachments/files/17710219/com.unity.collections.zip) file, which contains modified files. Open the zip file and drag all its contents inside the ``com.unity.collections`` folder. Replace all files.

![image](https://github.com/user-attachments/assets/848289e3-d98f-4061-9ec3-d743374ecf47)

4. Your project should be back to an usable state, if not, ask for help in the RoR2 modding community's ThunderKit channel.

## Using RoR2 Thunder Burster

RoR2 Thunder Burster consists of 2 new Compossable Objects added.

| Compossable Object | Compossable Type | Description |
|--|--|--|
| BurstAssemblyDefinitionsDatum | Manifest Datum | A subclass of the AssemblyDefinitions Manifest Datum, it's used by the following pipeline job to burst the specified assemblies using the Burst compiler. |
| BurstStagedAssemblies | Pipeline Job | A custom pipeline job, that takes the main manifest's BurstAssemblyDefinitionsDatum and bursts the defined assemblies. |

Begin by modifying your manifest and replacing it's "Assembly Definitions" datum for the "Burst Assembly Definitions Datum". You can lick on the cogwheel next to the manifest datum to remove it.

![image](https://github.com/user-attachments/assets/a141c91e-2d26-43e4-a96e-d734628f762a)

To burst your assemblies, add the Burst Staged Assemblies pipeline job to your build pipeline, this pipeline job should run **_After_** Stage Assemblies. It also contains a field that you must assign the previously executed Stage Assemblies job. this is used to obtain necesary metadata for the burst compiler to work.

![image](https://github.com/user-attachments/assets/2f3a6d20-5cf0-4b89-8372-a846c3b76c6f)

Executing your pipeline will build your assembly, and then burst said assembly thru the burst compiler. The bursted assemblies will be staged on the same folder as your Assembly.

![image](https://github.com/user-attachments/assets/7b6a4787-81bf-4c73-b46d-e779435b6a42)

## Enabling your assembly ingame

To enable your assembly ingame, add a Precompiled Assembly Reference to Bursts of Rain.

![image](https://github.com/user-attachments/assets/22d432a2-45e9-46be-ae95-6f2635605a27)

Somewhere during your mod's initialization routine, you can utilize the main class of Bursts of Rain for loading your bursted assembly. Here it's being done in my mod's Awake call.

![image](https://github.com/user-attachments/assets/c27a8cfe-242f-465e-903e-7017b93ee19c)

Your bursted assembly is now loaded at runtime, and burst-compiled jobs will be used instead of their native C# jobs.
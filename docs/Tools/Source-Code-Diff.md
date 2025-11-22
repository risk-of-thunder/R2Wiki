# Source Code Diff
Creating a diff of the game's source code is a useful way to track what changes are introduced with each game update.

This is done with a Version Control System, such as [Git](https://git-scm.com/tools).

## Setup (only done once)

### Initialize a repository
Create the following folder structure

```
RoR2_diff
└ Project
```

and make a repository of the `RoR2_diff` folder. If you're using the command line, execute the command `git init` from inside that folder.

This will create the `.git` folder.

### Download ILSpy
Download [ILSpy](https://github.com/icsharpcode/ILSpy/releases) and make sure it's not older than version 9.1.

Go to View -> Options, scroll down to the "Other" section, and check the option to "Sort custom attributes". This will prevent attributes from appearing in a random order between project exports, which can create diff noise.

<img width="586" height="493" alt="ILSpy options" src="https://github.com/user-attachments/assets/327b2bd8-6922-4971-9269-f746b422422a" />

### Obtain an older version of the source code
In order to make a diff, we need a previous version of the source code as a reference point.

Use [RoR2VersionSelector](https://github.com/risk-of-thunder/RoR2VersionSelector) and follow the instructions therein to download the penultimate game update.

## Extract the source code
Navigate to the Steam Risk of Rain 2 folder, or downloaded depot for the version selector, and load the `Risk of Rain 2_Data/Managed/RoR2.dll` assembly to ILSpy.

From the Assembly panel on the left, right click on RoR2 and "Save Code..." to the `Project` folder.

<img width="353" height="335" alt="ILSpy export" src="https://github.com/user-attachments/assets/2710e740-7b0a-4929-b5b2-aeecda2414d0" />

Back to Git, select all changed files and make a commit. If you are using the command line, this is done with

```
git add .
git commit -m "RoR2 version X.Y.Z"
```

## Repeat
Delete the contents of the `Project` folder, load the newer version of the RoR2.dll to ILSpy and repeat the previous step.

From the commit history in your Git client GUI you can now view the changes between the last two commits, or `git show` if you REALLY want to do this from the command line.

You can also do this from github, but if you push the repository online make sure to mark it as private.
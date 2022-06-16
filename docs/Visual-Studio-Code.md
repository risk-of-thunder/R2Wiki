# Using Visual Studio Code

Caution: You may be more comfortable using VSCode, but most modders use Visual Studio Community for writing mods, and Visual Studio supports more .NET configurations than VSCode currently does. It's still possible to use VSCode, but requires some setup.

[Visual Studio Code](https://code.visualstudio.com/) (or VSCode) is a general-purpose open source IDE by Microsoft. If you are more comfortabe in VSCode and prefer to use it rather than learning a new IDE (Visual Studio, which most modders and developers use for C#/.NET projects), this page is a description on some of the differences between the two IDEs.

Reference:
- https://code.visualstudio.com/docs/languages/csharp
- https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code

# New Project

To match the convention that most mods use, we will create a top-level "solution" (using a `.sln` file) and then write our code in a source directory as a .NET `classlib`.

Create a new folder called `SamplePlugin` (can be done in VSCode or File Explorer).

Open this folder in VSCode, and open a Terminal. We will use the `dotnet` CLI to create the necessary solution and and cs/csproj files.

```
# Terminal

D:\Mods\SamplePlugin > dotnet new sln

D:\Mods\SamplePlugin > dotnet new classlib -o SamplePlugin

D:\Mods\SamplePlugin > dotnet sln add .\SamplePlugin\SamplePlugin.csproj
```

After running these commands, your directory should look like this:

```
SamplePlugin
├── SamplePlugin
│   ├── Class1.cs
│   ├── SamplePlugin.csproj
│   └── obj
└── SamplePlugin.sln
```

The top-level solution relates to build information for the "solution" (this project, or a group of projects) such as Debug/Release compiling or the architecture target. The `.csproj` file specifies information for a project such as its .NET version and its dependencies.

# Adding References

Visual Studio can add references through a file chooser dialog, but VSCode doesn't have a good extension that does this (as of this writing).

The csproj file can be manually edited to add the required references to BepInEx and Risk of Rain 2 DLLs. If you copy the [necessary DLLs](https://github.com/risk-of-thunder/R2Wiki/wiki/First-Mod#getting-the-boilerplate) into `libs/`, then you can put the following lines into the csproj file:

```xml
<Project = Sdk=Microsoft.NET.Sdk">

  ...

  <ItemGroup>
    <Reference Include="Assembly-CSharp">
      <HintPath>libs\Assembly-CSharp.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="BepInEx">
      <HintPath>libs\BepInEx.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Facepunch.Steamworks">
      <HintPath>libs\Facepunch.Steamworks.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="HGCSharpUtils">
      <HintPath>libs\HGCSharpUtils.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="HGUnityUtils">
      <HintPath>libs\HGUnityUtils.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="KdTreeLib">
      <HintPath>libs\KdTreeLib.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="MMHOOK_Assembly-CSharp">
      <HintPath>libs\MMHOOK_Assembly-CSharp.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Mono.Security">
      <HintPath>libs\Mono.Security.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="pb_Stl">
      <HintPath>libs\pb_Stl.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Poly2Tri">
      <HintPath>libs\Poly2Tri.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="ProBuilderCore">
      <HintPath>libs\ProBuilderCore.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="ProBuilderMeshOps">
      <HintPath>libs\ProBuilderMeshOps.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="R2API">
      <HintPath>libs\R2API.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Rewired_Core">
      <HintPath>libs\Rewired_Core.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Rewired_CSharp">
      <HintPath>libs\Rewired_CSharp.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Rewired_Windows_Lib">
      <HintPath>libs\Rewired_Windows_Lib.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="SimpleJSON">
      <HintPath>libs\SimpleJSON.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Unity.Postprocessing.Runtime">
      <HintPath>libs\Unity.Postprocessing.Runtime.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Unity.RenderPipelines.Core.Runtime">
      <HintPath>libs\Unity.RenderPipelines.Core.Runtime.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Unity.RenderPipelines.Core.ShaderLibrary">
      <HintPath>libs\Unity.RenderPipelines.Core.ShaderLibrary.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Unity.TextMeshPro">
      <HintPath>libs\Unity.TextMeshPro.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine">
      <HintPath>libs\UnityEngine.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.AccessibilityModule">
      <HintPath>libs\UnityEngine.AccessibilityModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.AIModule">
      <HintPath>libs\UnityEngine.AIModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.AnimationModule">
      <HintPath>libs\UnityEngine.AnimationModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ARModule">
      <HintPath>libs\UnityEngine.ARModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.AssetBundleModule">
      <HintPath>libs\UnityEngine.AssetBundleModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.AudioModule">
      <HintPath>libs\UnityEngine.AudioModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.BaselibModule">
      <HintPath>libs\UnityEngine.BaselibModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ClothModule">
      <HintPath>libs\UnityEngine.ClothModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ClusterInputModule">
      <HintPath>libs\UnityEngine.ClusterInputModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ClusterRendererModule">
      <HintPath>libs\UnityEngine.ClusterRendererModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.CoreModule">
      <HintPath>libs\UnityEngine.CoreModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.CrashReportingModule">
      <HintPath>libs\UnityEngine.CrashReportingModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.DirectorModule">
      <HintPath>libs\UnityEngine.DirectorModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.FileSystemHttpModule">
      <HintPath>libs\UnityEngine.FileSystemHttpModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.GameCenterModule">
      <HintPath>libs\UnityEngine.GameCenterModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.GridModule">
      <HintPath>libs\UnityEngine.GridModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.HotReloadModule">
      <HintPath>libs\UnityEngine.HotReloadModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ImageConversionModule">
      <HintPath>libs\UnityEngine.ImageConversionModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.IMGUIModule">
      <HintPath>libs\UnityEngine.IMGUIModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.InputModule">
      <HintPath>libs\UnityEngine.InputModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.JSONSerializeModule">
      <HintPath>libs\UnityEngine.JSONSerializeModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.LocalizationModule">
      <HintPath>libs\UnityEngine.LocalizationModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.Networking">
      <HintPath>libs\UnityEngine.Networking.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ParticleSystemModule">
      <HintPath>libs\UnityEngine.ParticleSystemModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.PerformanceReportingModule">
      <HintPath>libs\UnityEngine.PerformanceReportingModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.Physics2DModule">
      <HintPath>libs\UnityEngine.Physics2DModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.PhysicsModule">
      <HintPath>libs\UnityEngine.PhysicsModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ProfilerModule">
      <HintPath>libs\UnityEngine.ProfilerModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.ScreenCaptureModule">
      <HintPath>libs\UnityEngine.ScreenCaptureModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.SharedInternalsModule">
      <HintPath>libs\UnityEngine.SharedInternalsModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.SpatialTracking">
      <HintPath>libs\UnityEngine.SpatialTracking.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.SpriteMaskModule">
      <HintPath>libs\UnityEngine.SpriteMaskModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.SpriteShapeModule">
      <HintPath>libs\UnityEngine.SpriteShapeModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.StreamingModule">
      <HintPath>libs\UnityEngine.StreamingModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.StyleSheetsModule">
      <HintPath>libs\UnityEngine.StyleSheetsModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.SubstanceModule">
      <HintPath>libs\UnityEngine.SubstanceModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.TerrainModule">
      <HintPath>libs\UnityEngine.TerrainModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.TerrainPhysicsModule">
      <HintPath>libs\UnityEngine.TerrainPhysicsModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.TextCoreModule">
      <HintPath>libs\UnityEngine.TextCoreModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.TextRenderingModule">
      <HintPath>libs\UnityEngine.TextRenderingModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.TilemapModule">
      <HintPath>libs\UnityEngine.TilemapModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.Timeline">
      <HintPath>libs\UnityEngine.Timeline.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.TimelineModule">
      <HintPath>libs\UnityEngine.TimelineModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.TLSModule">
      <HintPath>libs\UnityEngine.TLSModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UI">
      <HintPath>libs\UnityEngine.UI.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UIElementsModule">
      <HintPath>libs\UnityEngine.UIElementsModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UIModule">
      <HintPath>libs\UnityEngine.UIModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UmbraModule">
      <HintPath>libs\UnityEngine.UmbraModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UNETModule">
      <HintPath>libs\UnityEngine.UNETModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityAnalyticsModule">
      <HintPath>libs\UnityEngine.UnityAnalyticsModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityConnectModule">
      <HintPath>libs\UnityEngine.UnityConnectModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityTestProtocolModule">
      <HintPath>libs\UnityEngine.UnityTestProtocolModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityWebRequestAssetBundleModule">
      <HintPath>libs\UnityEngine.UnityWebRequestAssetBundleModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityWebRequestAudioModule">
      <HintPath>libs\UnityEngine.UnityWebRequestAudioModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityWebRequestModule">
      <HintPath>libs\UnityEngine.UnityWebRequestModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityWebRequestTextureModule">
      <HintPath>libs\UnityEngine.UnityWebRequestTextureModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.UnityWebRequestWWWModule">
      <HintPath>libs\UnityEngine.UnityWebRequestWWWModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.VehiclesModule">
      <HintPath>libs\UnityEngine.VehiclesModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.VFXModule">
      <HintPath>libs\UnityEngine.VFXModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.VideoModule">
      <HintPath>libs\UnityEngine.VideoModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.VRModule">
      <HintPath>libs\UnityEngine.VRModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.WindModule">
      <HintPath>libs\UnityEngine.WindModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="UnityEngine.XRModule">
      <HintPath>libs\UnityEngine.XRModule.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Wwise">
      <HintPath>libs\Wwise.dll</HintPath>
      <Private>false</Private>
    </Reference>
    <Reference Include="Zio">
      <HintPath>libs\Zio.dll</HintPath>
      <Private>false</Private>
    </Reference>
  </ItemGroup>

</Project>
```

NOTE - not all of these lines are required, and they may not be all of the DLLs that exist on your machine. Some of the DLLs may not be needed by your mod depending on the functionality you are writing.

NOTE - if you copy these DLLs into `libs/`, do NOT add Risk of Rain DLLs to version control if you are hosting your code on a public repository. We dont want to push code (even compiled code) owned by Hopoo anywhere.

# Building

## Building with VSCode

VSCode can auto-generate a "build" task for a .NET project that runs `dotnet build` or `dotnet msbuild`.

Go to `Terminal > Run Build Task`:
- click dialog "No build task found. Configure Build Task ..."
- "Create tasks.json" from template (VSCode will auto-generate a `tasks.json`)
- Select `.NET Core` since this project builds via `dotnet`.

You can see VSCode has filled out a tasks.json file that defines a build command that calls `dotnet build` with some additional arguments. This build task can now be run with `ctrl + shift + b` every time you want to build the project.

## Building Directly with `dotnet`

The project can be built from the command line by using the `dotnet` cli:

```
D:\Mods\SamplePlugin > dotnet restore

D:\Mods\SamplePlugin > dotnet msbuild
```

The build artifact will be be created under `SamplePlugin/SamplePlugin/bin/Debug/netstandard2.0/SamplePlugin.dll`.

When compiling for release, typically you will pass the Release parameter to msbuild:

```
D:\Mods\SamplePlugin > dotnet msbuild /p:Configuration=Release
```

The target will now be under `bin/Release`.

# Build Events

See: [Build Events](https://github.com/risk-of-thunder/R2Wiki/wiki/Build-Events) page that describes workflows while using Visual Studio.

Visual Studio Code also supports tasks that can be added in `.vscode/tasks.json`

## Copy result DLL to BepInEx directory

Create a directory in your `BepInEx/plugins` directory to store your plugin, e.g. `SamplePlugin/`.

Then add the following to your the tasks list in tasks.json for this project, adjusting paths as necessary:

```json
        {
            "label": "deploy",
            "command": "Copy-Item",
            "type": "shell",
            "args": [
                "SamplePlugin/bin/Debug/netstandard2.0/SamplePlugin.dll",
                "D:/Program Files (x86)/Steam/steamapps/common/Risk of Rain 2/BepInEx/plugins/SamplePlugin/"
            ],
            "group": "build"
        }
```

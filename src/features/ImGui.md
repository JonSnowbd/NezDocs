---
layout: default
title: ImGui
parent: Features
nav_order: 2
---

## Enabling ImGUI

For a basic ImGui inspector in Nez there are 2 things you need to do, first of which is to 
reference `Nez/Nez.ImGui/Nez.ImGui.csproj` in your Game's `.csproj`:

```xml
<ItemGroup>
    <!-- If the include isn't your path to nez, make sure to change it below! -->
    <ProjectReference Include="..\Nez\Nez.ImGui\Nez.ImGui.csproj" /> 
</ItemGroup>
```

And the in your `Core` implementation add the `Nez.ImGuiTools.ImGuiManager` to the list of Global
Managers.

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input;
using Nez;
using Nez.ImGuiTools;

public class GameCore : Core
{
    override protected void Initialize()
    {
        base.Initialize();

        var manager = new ImGuiManager();
        // Optionally you can make it toolbar only!
        // This doesnt remove anything that you cant re-enable in game while you debug.
        manager.ShowSeperateGameWindow = false;
        manager.ShowCoreWindow = false;
        manager.ShowSceneGraphWindow = false;
        RegisterGlobalManager(manager);
        manager.SetEnabled(false); // Turn it off right after if you don't need to immediately use it.

        Scene = new Scene();
    }
}
```

## Using ImGUI Directly

You may have noticed that you cannot use ImGui directly, this is because the
[ImGui Framework](https://github.com/mellinoe/ImGui.NET) that nez uses is not re-exported. To
use the ImGui Library directly its recommended to add a reference to it to your Game's `.csproj`:

```xml
<ItemGroup> <!-- Remember this doesnt have to be by itself, you can add this to any ItemGroup -->
    <PackageReference Include="ImGui.NET" Version="1.71.0" />
</ItemGroup>
```

**Note: It is very important that you specifically install `1.71.0` as it is what Nez uses under
the hood. Anything else could have type mismatches.**

And you can now directly use the ImGui by registering draw commands. As an example in an arbitary
`Scene`:

```csharp
using ImGuiNET;
using Nez;
using Nez.ImGuiTools;

namespace GameSource.Scenes
{
    public class ImGuiExample : Scene
    {
        ImGuiWindowFlags flags;

        public ImGuiExample()
        {
            flags = ImGuiWindowFlags.MenuBar | ImGuiWindowFlags.AlwaysVerticalScrollbar;
        }

        public override void OnStart()
        {
            base.OnStart();

            var manager = Core.GetGlobalManager<ImGuiManager>();
            manager.RegisterDrawCommand(DrawImGuiPanel);
            manager.SetEnabled(true);
        }

        void DrawImGuiPanel() // No return type, no parameters.
        {
            ImGui.Begin("Panel Name", flags);
            if (ImGui.Button("Log"))
            {
                Debug.Log("Button was pressed");
            }
            ImGui.End();
        }
    }
}
```

## Learning more about ImGUI

If you're curious on what ImGUI can do, or how to do it, Nez by default includes a demo window
that shows off everything that ImGui can do, you can cross reference this panel with everything
inside [this file](https://github.com/ocornut/imgui/blob/master/imgui_demo.cpp). The method names
are the exact same and pointers are replaced with `ref`.

![Demo Example]({{site.baseurl}}/assets/example/imguiDemo.png)

## Resources

- [ImGui_Demo.cpp](https://github.com/ocornut/imgui/blob/master/imgui_demo.cpp)
- [Nez Repo Docs](https://github.com/prime31/Nez/blob/master/FAQs/DearImGui.md)
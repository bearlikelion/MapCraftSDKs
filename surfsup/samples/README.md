# SurfsUp sample maps

`surf_mapcraft.mcmap` is a short surf course made with MapCraft and the SurfsUp SDK.

- **Course** layer: the start and finish platforms and three ramps from the Surf Ramp generator, two straight and one curving 90 degrees. Select a ramp group and change its parameters in the inspector to reshape it.
- **Gameplay** layer: the player start, the start zone on the first platform, two split zones between the ramps and the finish zone on the last platform.
- **Hazards** layer: a lava floor with a kill zone above it, which sends players back to the start.

Zones use `tools/trigger`, so the editor draws them see-through in their FGD color and the Godot extension turns them into invisible areas.

## sample_map

`sample_map.mcmap` is a longer linear course that shows one Surf Ramp shape per ramp, so you can click through them and compare their settings.

- **Course** layer: the start platform, five ramps named after what they show (Straight, Left curve, S-Curve both sides, Dip, Broken thin) and the finish platform, with `func_detail` pillars under both platforms.
- **Gameplay** layer: the player start, the light, the start zone with its run direction, four split zones in the gaps between ramps (`split_index` 0 to 3) and the finish zone.
- **Hazards** layer: a lava floor with a lava `kill_box` over it.

Each ramp starts about 192 units below the one before, so a player always lands on the next surf face.
Curves keep each piece under 5 degrees of turn, and the broken ramp is three long pieces rather than many short ones.
Splits are thin walls across each gap: SurfsUp blocks the finish until a run has crossed all of them, which stops shortcuts.

Open either map with File > Open, then set the SurfsUp project folder in File > SDK Settings so the materials load.
To play one, put the MapCraft Godot addon in the SurfsUp project, set the project folder and Godot editor in SDK Settings, and use Run > Play in SurfsUp project.

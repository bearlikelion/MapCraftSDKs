# EverQuest SDK

Bring EverQuest zones into MapCraft to study, rework or remake them.
The `everquest` plugin (`plugins/everquest`) reads the classic zone archives (`.s3d`); EverQuest's files stay in your own install and are never copied into MapCraft.

## Setup

1. Install the SDK from File > SDK Manager…, which brings its plugin.
   In a MapCraft source checkout, `cargo xtask plugins` builds `everquest.wasm` into `sdks/everquest/plugins` instead.
2. Start a new map with the SDK and set its **EverQuest folder** to the folder holding the zone files, such as `gfaydark.s3d`.
   The plugin reads each zone's `_obj.s3d` from there for its object models.
3. Import a zone with File > Import > EverQuest zone (S3D), or drop its `.s3d` on the window.
   Pick the zone's own archive (`gfaydark.s3d`), not its `_obj`, `_chr` or `_sndbnk` files.

## What works

- **Zone geometry** becomes brushes, one per flat patch: neighboring triangles that share a plane, a material and a texture mapping merge into one convex brush half an EverQuest unit thick, extruded behind the surface.
  - Textures line up as in the game, including trim that stretches one row of a texture along a strip.
  - Drawn geometry goes in the **Zone** group.
  - Geometry players walk through, such as foliage and water surfaces, goes in **Passable** as a `func_illusionary`.
  - Invisible walls and other collision the game never draws go in **Invisible walls**, hidden, with a purple `everquest/boundary` material.
- **Textures**: the zone's bitmaps and DDS pictures (DXT1, DXT3 and DXT5) are kept with the map, in a Materials panel folder named after the zone.
  Masked materials show their first palette color as clear, so leaves, fences and signs cut out as they do in the game.
- **Objects** placed in the zone become `prop_static` entities in the **Objects** group, showing their models from the zone's `_obj.s3d` with heading, tilt and scale.
- **Lights** become `light` entities in the **Lights** group with their color and radius, and fade with EverQuest's own falloff curve (`_falloff` set to `everquest`), so a baked zone lights much as the game's own vertex lighting does.
  The game's zones carry no bounce light; set worldspawn's **Light bounces** to 0 for the closest match.
- **Coordinates** stay EverQuest's own, Z up, so a location in MapCraft matches `/loc` in the game (which lists Y before X) and spawn and door tables from a server database.
  An EverQuest unit is roughly a foot, so the SDK sets 3.28 units per meter.

## What doesn't work yet

- Newer zones and models in `.eqg` archives (Gates of Discord onward).
- Objects with a skeleton, such as North Qeynos's `templelife`, show as boxes; only static objects show their models.
- Texture animation shows the first frame, and translucent materials such as water draw opaque.
- Zone regions (water, lava, zone lines), the sky, sounds, and exporting back to EverQuest.
- A zone with more than 100,000 brushes after merging keeps the first 100,000, leaving out invisible walls first, then passable geometry, and says so in the Output panel; the largest Planes of Power zones reach about 240,000.
- Doors, spawn points and zone points live in the server's database, not the zone files, so they are not imported.

## Attribution

- EverQuest, its zones, models and textures belong to Daybreak Game Company.
  This SDK includes none of them; the plugin reads them from your own copy when it runs.
- The S3D, WLD and texture formats were implemented from [EQ Sage](https://github.com/knervous/eqsage) (MIT) and from observing real files.
- `fgd/godot.fgd` is MapCraft's own set of the entities the MapCraft Godot addon builds.

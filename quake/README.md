# Quake SDK

Edit maps for id Software's Quake (1996) in MapCraft.
The SDK holds the entity definitions and compile profiles; the `quake` plugin (`plugins/quake`) reads and writes `.map` files and reads WAD textures.
Quake itself, its PAK files and its palette stay in your own install and are never copied into MapCraft.

## Setup

1. Install the SDK from File > SDK Manager…, which brings its plugin.
   In a MapCraft source checkout, `cargo xtask plugins` builds `quake.wasm` into `sdks/quake/plugins` instead.
2. Open the SDK in MapCraft and set its folders:
   - **Quake folder**: the folder holding `id1`.
     The plugin reads Quake's palette from `id1/pak0.pak` (`gfx/palette.lmp`), or from a loose `id1/gfx/palette.lmp`.
   - **WAD folder**: where your texture WADs live.
     WADs anywhere under the Quake folder are found too.
   - **Texture folders** (below the paths): more folders of WADs, or single WAD files from anywhere, such as a jam's WAD; click **File** to pick one.
   - **ericw-tools folder**: the folder holding `qbsp`, `vis` and `light`.
   - **Quake engine**: the engine to play in, such as ironwail or Quakespasm.
3. Put some WADs in the WAD folder.
   The [Prototype WAD](http://khreathor.xyz/site/prototype/) by Khreathor is a good free start, and the SDK's default material, `64_grey_1`, comes from it.

## What works

- **Importing `.map` files** (File > Import > Quake map):
  - Standard (Quake) and Valve220 brush formats, as TrenchBroom, J.A.C.K., Hammer and older editors write them.
  - Point and brush entities with all their keys in file order; extra surface values from Quake 2 style files are skipped.
  - The world's keys (`message`, `worldtype`, `light`, `_sunlight` and the rest) go on the map's world where it does not have them yet; `wad` and `mapversion` are left out, since the export writes its own.
  - TrenchBroom layers and groups become groups named after them, nested ones as `Layer / Group / Subgroup`.
  - Texture alignment: Valve220 axes carry over as they are, and Standard faces get Quake's projection from their normal, offsets, rotation and scales, matching what ericw-tools' qbsp compiles.
  - The textures the map uses, from the WADs its `wad` key names (matched by file name, since the key usually holds paths on the mapper's own disk), then every other WAD the SDK can read.
    MapCraft keeps their pictures with the map.
- **Exporting `.map` files** (File > Export > Quake map (Valve220), and the first step of both compile profiles):
  - Valve220, with `"mapversion" "220"` added to the world's keys.
  - Points and texture axes are written unchanged in their shortest exact form, so brushes and alignment survive the trip: MapCraft's points run clockwise seen from outside as Quake's do, and its texture axes are Valve220's.
  - Layers and groups become TrenchBroom `func_group` entities (`_tb_type`, `_tb_name`, `_tb_id`, `_tb_layer`, `_tb_group`), so they come back as they were in TrenchBroom and in MapCraft, and qbsp merges them into the world.
  - Brush entities carry their brushes; brushes in layers and groups are world brushes.
  - A world with no `wad` key gets one naming, by full path, the WADs that hold the map's textures, so qbsp finds those in the Quake folder as well as the WAD folder.
  - The Output panel warns about texture names over 15 characters, which do not fit a WAD or BSP, and about brushes with fewer than four faces, which are left out.
  - A key, value or material a `.map` cannot hold (a quote, a line break, a space in a texture name) stops the export with a message instead of writing a broken file.
- **Compiling**: "Compile" exports the map to a `build` folder beside it, then runs qbsp, vis and light and copies the BSP and its `.lit` colored light into `id1/maps`; "Build and play" then starts the engine on it.
  Light writes colored light twice: inside the BSP as a BSPX `RGBLIGHTING` lump, which FTE, QuakeSpasm-Spiked and ezQuake read with no other file, and as the `.lit` file that Ironwail, vkQuake and QuakeSpasm need.
  The `build` folder keeps the export from overwriting a `.map` of the same name next to your map.
- **Baked lighting in the Lit view**: while a view is Lit, "Bake lighting" runs the same qbsp and light (without vis) in the editor's bake folder shortly after each edit, without saving the map, and the view draws the compiled faces with their lightmaps as Quakespasm-family engines such as Ironwail do: twice the lightmap, multiplied in sRGB and clamped, with skies and unlit liquids as the game shows them.
  The worldspawn's light compiler keys (sun brightness, direction, color and softness, sky fill, ambient occlusion, bounce, minimum light) are in the inspector, so tuning them shows the game's shadows a second later.
  Triggers do not show in a bake, as in the game; switch to Textured to see them.
- **WAD textures**:
  - WAD2 (Quake) textures decoded with Quake's palette, and WAD3 (Half-Life) textures with the palette each one carries.
  - Texture names are lowercase, as Quake ignores case; `*`, `+` and `{` prefixes are kept.
  - Skies keep both layers; in the views they show as Quake's scrolling sky does, projected from the view direction with the near clouds over the far ones (not moving). `{` textures show palette index 255 as clear.
  - The material browser lists every texture in the readable WADs, shrunk when the WADs are big; the textures a map uses are read full size.
  - Each WAD is its own folder in the Materials panel, named after the WAD file, so **Folders** shows or hides a whole WAD and searching a WAD's name finds its textures.
    A texture in more than one WAD shows once, in the folder of the WAD its picture comes from.

- **Models**: monsters, weapons, items, keys, torches and explosive boxes show their Quake models in the views, read from the Quake folder's PAK files (or loose `progs` and `maps` files).
  - `.mdl` models show their first frame and first skin; `.bsp` brush models, such as the health and ammo boxes, show with their own textures.
  - The FGD names one model per class, so variants picked by spawnflags (rotten health, big ammo, the yellow and red armor skins) show the default model.
- **Textures** draw pixel sharp, as Quake draws them, and stay crisp in the distance through mipmaps.

## What doesn't work yet

- **TrenchBroom layer settings** such as "omit from export", hiding, locking and sort order are not kept; layers become plain groups.
- Quake 3 brush primitives and patches, Quake 2 surface flags, textures inside BSP files, and WADs a map's `wad` key names outside the SDK's folders and the Texture folders list (add those under Texture folders).
- Displaced faces are written flat, with a warning in the Output panel, and entity connections are not exported, since Quake has neither.

## Attribution

- Quake, its PAK files and its palette belong to id Software.
  This SDK includes none of them; the plugin reads the palette from your own copy when it runs.
- `logo.jpg` is Quake's Steam store header; the Quake logo is a trademark of id Software, used only to name the game this SDK is for.
- `fgd/quake.fgd` is written for MapCraft.
  Entity names, keys and flag bits follow Quake's game code, as any definition file for the game must; the descriptions are original.
- ericw-tools (GPL) is a separate program you install yourself; the compile profiles only run it.
- TrenchBroom's layer and group keys are read as a file format; no TrenchBroom code or data is included.
- The default material name refers to Khreathor's Prototype WAD, which its author allows for use with Quake with credit.
  The WAD is not included.

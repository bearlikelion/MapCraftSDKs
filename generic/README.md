# Generic Map SDK

Open and edit Quake and TrenchBroom style `.map` files in MapCraft when the game has no SDK of its own.
The `quake` plugin (`plugins/quake`) reads and writes the `.map` files; the SDK knows no game, so it has no entity definitions and no compile profiles.

## Setup

1. Install the SDK from File > SDK Manager…, which brings its plugin.
   In a MapCraft source checkout, `cargo xtask plugins` builds `quake.wasm` into `sdks/generic/plugins` instead.
2. Start a new map with the SDK and set its **textures folder** to the folder the map's texture names are relative to, such as a Godot project's `textures`.
   PNG, JPEG, TGA, BMP and KTX2 pictures and WAD files in it show up as materials; a map still opens without it, untextured.
3. Bring the map in with File > Import > Quake map, or drop the `.map` on the window.

## What works

- Standard (Quake) and Valve220 brush formats, as TrenchBroom, J.A.C.K. and func_godot projects use them.
- Point and brush entities keep their class and every key, whatever the game.
- TrenchBroom layers and groups become groups named after them.
- File > Export > Quake map (Valve220) writes the map back as a `.map`.

## What does not

- There are no entity definitions, so the entity browser is empty and keys are edited as plain text.
  For a TrenchBroom game config or a Godot project using func_godot, `mapcraft-cli sdk import-trenchbroom <folder>` makes an SDK with the game's own entities instead.
- Textures in formats MapCraft cannot read, such as DDS or supercompressed KTX2, are left blank.
- Every picture in the folder is a material, so normal and metal maps kept beside a texture are listed too.

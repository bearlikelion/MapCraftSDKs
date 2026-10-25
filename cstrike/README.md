# Counter-Strike: Source SDK

Make maps for Valve's Counter-Strike: Source in MapCraft.
The SDK holds the manifest and compile profile; the `source` plugin (`plugins/source`) lists the game's materials and reads and writes Hammer `.vmf` files, and `source-bsp` imports compiled maps.
The game's materials, its entity definitions (`bin/cstrike.fgd`) and its compilers stay in your own install and are never copied into MapCraft.

## Setup

1. Build the plugins with `cargo xtask plugins`, which copies `source.wasm` and `source-bsp.wasm` into `sdks/cstrike/plugins`.
2. Set **Counter-Strike: Source game folder** in File > SDK Settings to the game's `cstrike` folder.
   The Windows version of the game is needed for `bin/cstrike.fgd` and the compilers; on Linux, the compilers run through Wine.

The [Counter-Strike: Source guide](https://github.com/bearlikelion/MapCraft/blob/main/docs/guide/cstrike.md) covers what is imported, exported and skipped.

## Attribution

- Counter-Strike: Source, its packages, materials, entity definitions and compilers belong to Valve.
  This SDK includes none of them; the plugins read them from your own install when they run.
- The VMF reader and writer are written for MapCraft from the format's public description and from observing files.

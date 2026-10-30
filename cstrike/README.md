# Counter-Strike: Source SDK

Make maps for Valve's Counter-Strike: Source in MapCraft.
The SDK holds the manifest and compile profiles; the `source` plugin (`plugins/source`) lists the game's materials and reads and writes Hammer `.vmf` files, and `source-bsp` imports compiled maps.
The game's materials, its entity definitions (`bin/cstrike.fgd`) and its compilers stay in your own install and are never copied into MapCraft.

## Setup

1. Install the SDK from File > SDK Manager…, which brings its plugins.
   In a MapCraft source checkout, `cargo xtask plugins` builds `source.wasm` and `source-bsp.wasm` into `sdks/cstrike/plugins` instead.
2. Set **Counter-Strike: Source game folder** in File > SDK Settings to the game's `cstrike` folder.
3. Only the Windows version of the game ships `bin/cstrike.fgd` (with `base.fgd` and `halflife2.fgd` beside it).
   On the Linux version, point **cstrike.fgd** at a copy of that folder's `cstrike.fgd`.
4. On Linux, the Fast, Compile, Final and Build and play profiles run vbsp, vvis and vrad built for Linux from the Source SDK, which go in the game's `bin/linux64` folder.
   The game's Linux version does not include them; Compile with Wine runs the Windows version's compilers instead.

The [Counter-Strike: Source guide](https://github.com/bearlikelion/MapCraft/blob/main/docs/guide/cstrike.md) covers what is imported, exported and skipped.

## Attribution

- Counter-Strike: Source, its packages, materials, entity definitions and compilers belong to Valve.
  This SDK includes none of them; the plugins read them from your own install when they run.
- `logo.jpg` is Counter-Strike: Source's Steam store header; the Counter-Strike: Source logo is a trademark of Valve, used only to name the game this SDK is for.
- The VMF reader and writer are written for MapCraft from the format's public description and from observing files.

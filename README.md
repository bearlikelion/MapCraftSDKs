# MapCraft SDKs

This repository is the official registry of SDKs for [MapCraft](https://github.com/bearlikelion/MapCraft), the open-source level editor.
An SDK teaches MapCraft one game: its entities, where its materials live, which plugins to load and how to compile and launch a map.
MapCraft ships without SDKs: a fresh install shows the ones listed in this repository's `index.json` as cards to pick from, and installs them on demand.
MapCraft's SDK Manager (File > SDK Manager…) reads the same `index.json`, so anything listed in it can be installed or updated with one click.

## What is here

- `index.json`: the registry MapCraft reads, at `https://raw.githubusercontent.com/bearlikelion/MapCraftSDKs/main/index.json`.
- `templates/basic/`: a minimal SDK to start from, the same one MapCraft's Create tab writes.
- `<id>/`: SDKs whose source lives in this repository, such as `surfsup/`, `quake/` and `cstrike/`; an SDK can also live in its own repository and only add an entry to `index.json`.
- `.github/workflows/check.yml`: the checks every pull request runs.
- `.github/workflows/release.yml`: publishes the SDKs here to GitHub Releases and keeps `index.json` current.

This repository is also MapCraft's `sdks/` folder, as a git submodule, which is why SDK folders sit at the top level.
MapCraft builds the first-party plugins into `surfsup/plugins/`, `quake/plugins/` and `cstrike/plugins/` with `cargo xtask plugins`; those builds are not committed, and the release workflow builds them the same way into each zip.

## How the SDKs here are released

The release workflow runs when a push to `main` changes an SDK folder, and by hand from the Actions tab.
It builds `mapcraft-cli` and the first-party plugins from MapCraft's `main`, then handles each top-level SDK folder in turn.
An SDK whose release `<id>-<version>` (such as `surfsup-5`) already exists is skipped.
Any other SDK is checked, packaged, published as the release `<id>-<version>` with its zip, and its entry in `index.json` is replaced.
The workflow then commits `index.json` back to `main`.

So to release a new version, bump `version` in its `sdk.toml` and merge; never edit the entries of SDKs in this repository by hand.
Changes merged without a version bump are not published, because that version's release already exists.
If a run fails after creating a release, delete that release and its tag, then run the workflow again.

### Logos

An SDK folder may hold a `logo.png` or `logo.jpg`, shown on the SDK's card in MapCraft; a wide picture such as a 460x215 Steam header fits best.
The release workflow adds it to the entry as `"logo": "<id>/logo.jpg"`, a path relative to `index.json`.
The Quake and Counter-Strike: Source logos are trademarks of id Software and Valve, used only to name the games those SDKs are for.

## Publishing an SDK

SDKs whose source lives in this repository only need steps 1, 2 and 5; the release workflow does steps 3 and 4.

### 1. Lay out the folder

```
my-game/
  sdk.toml          the manifest: id, name, version, entities, materials, paths, compile profiles
  fgd/              entity definitions
  plugins/<id>.wasm WebAssembly plugins the manifest lists, if any
  samples/*.mcmap   at least one small map that shows the SDK working
  logo.png          optional picture for the SDK's card (or logo.jpg)
  README.md         what the SDK is for and how to set it up
```

Start from `templates/basic/`, or make a fresh copy with `mapcraft-cli sdk new my-game --id my-game --name "My Game"`, or with File > SDK Manager… > Create.
The id is saved in every map made with the SDK, so choose it once: lowercase letters, digits, `_` and `-`.
Fill in `description`, `author`, `license` and `homepage` in `sdk.toml`; they are copied into the registry entry.
Every key is described in [`docs/sdk-format.md`](https://github.com/bearlikelion/MapCraft/blob/main/docs/sdk-format.md) in the MapCraft repository.

### 2. Check it

```sh
mapcraft-cli sdk check my-game --var game=/path/to/the/game
```

The check reports each item as pass, warn or fail: the manifest, every FGD, every plugin (loaded in the plugin host), path settings, texture folders, compile programs and the sample maps.
Unset paths only warn, since they belong to each user's machine, but nothing may fail.
The SDK Manager's Check button runs the same check, and its Test button opens your first sample map.

### 3. Package it

```sh
mapcraft-cli sdk package my-game --out dist --url https://github.com/you/my-game/releases/download/v1.0.0
```

This writes `dist/my-game-1.0.0.zip` and prints its registry entry with the zip's `sha256` and `size`.
The same files always produce the same zip, so the checksum only changes when the SDK does.
Upload the zip somewhere it will stay, such as a GitHub release; without `--url`, `download` is just the file name, relative to the registry.

### 4. Add the entry to `index.json`

Paste the printed entry into the `sdks` list:

```json
{
  "id": "my-game",
  "name": "My Game",
  "version": "1.0.0",
  "description": "Edit maps for My Game.",
  "author": "You",
  "license": "MIT",
  "homepage": "https://github.com/you/my-game",
  "download": "https://github.com/you/my-game/releases/download/v1.0.0/my-game-1.0.0.zip",
  "sha256": "64 hex digits printed by sdk package",
  "size": 4096,
  "min_mapcraft": "0.2.0",
  "plugins": [],
  "logo": "https://raw.githubusercontent.com/you/my-game/main/logo.png"
}
```

`logo` is optional: a PNG or JPEG, as a URL or a path relative to `index.json`.

To publish an update, bump `version` in `sdk.toml`, package again, upload the new zip and replace the entry.
MapCraft offers Update when the registry's version is newer than the installed one, and an updated SDK asks again before its compile profiles run a program.

### 5. Open a pull request

If its source should live here, put the SDK folder in `<id>/` at the top level and leave `index.json` alone; once merged, the release workflow publishes it.
Otherwise add your entry to `index.json` in the pull request.

## Review checks

The workflow runs on every pull request:

- `templates/basic/` and every SDK folder pass `mapcraft-cli sdk check` with no failures.
- `index.json` parses: known format and version, valid and unique ids, 64-digit checksums, sizes up to 512 MiB.
- Every entry installs into an empty data folder: it downloads, matches its `sha256`, unpacks safely, holds an `sdk.toml` with the same id, and then passes the check.

A maintainer then reviews what a machine cannot:

- The license is open (MIT, Apache-2.0, BSD, CC0, CC BY, CC BY-SA, MPL-2.0 or similar) and covers everything in the zip but a game FGD shipped under the exception below.
- No game's own assets, textures, models or sounds are included unless their license allows it; SDKs point at the user's game install instead.
- Entity definitions are written for the SDK, not copied from Valve, id Software or TrenchBroom files.
  The exception is a game's own FGD that some versions of the game do not ship, copied unchanged and credited in the SDK's README, such as Counter-Strike: Source's `cstrike.fgd` and `base.fgd`, which its Linux version lacks.
- Plugins come with public source that builds them.
- Compile profiles only run the game's own tools or well-known compilers, never downloads or scripts fetched at run time.
- The homepage works and says how to report problems.

## Running your own registry

Any server, or a folder on disk, can host a registry in the same format.
Add its `index.json` URL or path in MapCraft under Preferences > SDK registries.
Relative `download` paths resolve next to the `index.json`, so a folder holding `index.json` and its zips is a complete private registry.
The format is described in full in [`docs/sdk-registry.md`](https://github.com/bearlikelion/MapCraft/blob/main/docs/sdk-registry.md) in the MapCraft repository.

# Landscaper - FF7 World Editor

This is a world map editor for Final Fantasy VII PC. It allows you to edit the world map geometry, dialogues, encounters, scripts and more.

[![Screenshot](https://raw.githubusercontent.com/maciej-trebacz/ff7-landscaper/main/landscaper-1.0-screenshot.png)](https://raw.githubusercontent.com/maciej-trebacz/ff7-landscaper/main/landscaper-1.0-screenshot.png)

## Platform support

All platforms can edit Final Fantasy VII PC game files.

- **Windows:** Can also connect to the running game to sync messages and show live model positions.
- **macOS and Linux:** File editing only; live-game integration is unavailable, including through Wine.

## Having issues? Want other features?

You can file issues directly using the [Issues](https://github.com/maciej-trebacz/ff7-landscaper/issues) tab if you have a GitHub account. Otherwise, you can also join my Discord server: [https://discord.gg/YyecaMa7Wf](https://discord.gg/YyecaMa7Wf).

Or come and chat with me [live on Twitch](https://twitch.tv/m4v3k) where I often code this app (and other FF7 related projects) live!

## Current features

* Messages 
  * edit region names and dialogue messages that show up on while on the world map
  * add new messages to be referenced by scripts
  * jump to script that references a message
* Map
  * view and edit the world map geometry for all three game maps
  * four different viewing modes: textured, terrain, region, scripts
  * export/import maps from/to .obj files that can be used in 3D modeling software like Blender
  * view all textures used in the world map
* Encounters
  * edit random encounter tables for each world region
  * edit Yuffie and Chocobo specific data
  * edit terrain region sets for each world region
* Scripts
  * custom text editor for scripts decompiled into a high-level scripting language
  * ability to edit three types of scripts: system, model and mesh
  * properties sidebar to adjust selected function parameters with inline documentation
  * search across all scripts
  * add new model & mesh scripts

## CI builds and releases

The [Build and release workflow](.github/workflows/build-release.yml) tests and
builds Windows x64 NSIS, macOS universal (Apple Silicon and Intel) DMG, and
Linux x64 AppImages on pull requests, pushes to `main`, and manual runs.
Download them from the run's `windows-x64-installer`, `macos-universal-installer`,
and `linux-x64-installer` artifacts. Pushing an `X.Y.Z` or `vX.Y.Z` tag publishes
a GitHub release with all three packages and generated release notes after
every build succeeds.

The existing Rust dependency is a local path (`../../ff7-lib` relative to
`src-tauri`), so CI checks out
[julianxhokaxhiu/ff7-lib.rs](https://github.com/julianxhokaxhiu/ff7-lib.rs)
alongside this repository as `ff7-lib`. **Commit and push the accompanying
portability changes in that fork before running this workflow**; the currently
published library does not build on macOS or Linux. CI follows the fork's
`main` branch until the change is published; replace `ref: main` with its commit
SHA afterwards to make builds reproducible. No dependency variables or extra
tokens are required.

The workflow uses the automatic `GITHUB_TOKEN` with `contents: write` only in
the release job; no release PAT is needed.

Rust builds cache Cargo downloads and compiled dependencies from the Tauri
target directory with `Swatinem/rust-cache`. The first run populates the cache;
later runs reuse it when compatible with the Rust toolchain and dependencies.

### Publishing a version

1. Update `version` in [package.json](package.json),
   [src-tauri/tauri.conf.json](src-tauri/tauri.conf.json), and
   [src-tauri/Cargo.toml](src-tauri/Cargo.toml). Refresh and commit the npm and
   Cargo lockfiles with the version changes.
2. Commit and push those changes.
3. Create and push a matching tag, for example:

   ```sh
   git tag v1.1.2
   git push origin v1.1.2
   ```

CI rejects tags whose version does not match all three manifests. Running the
workflow manually on a branch builds artifacts without publishing a release.
The existing interactive [release.ps1](release.ps1) is a separate, local signed
release process and is not used by CI.

### Signing and automatic updates

CI intentionally disables Tauri updater artifact generation for its builds,
so no signing secrets are required. Installers are unsigned, may trigger
Windows SmartScreen warnings, and must be installed manually. macOS packages
are ad-hoc signed but not notarized and will require Gatekeeper approval. The Linux
AppImage targets x64 systems and is built on Ubuntu 22.04 for compatibility;
make it executable before running it (`chmod +x <filename>.AppImage`). CI does not
upload the checked-in [updater.json](updater.json), since it describes an older
signed upstream release.

New builds check this fork's releases for `updater.json`. Until signed updater
artifacts and a matching updater manifest are published there, automatic
updates are unavailable and update checks may report a missing manifest.
Enabling them later requires a Tauri signing key, its matching configured
public key, and generation of a manifest containing the new signatures and
download URLs.

## Acknowledgements

* **codemann8** - worldmap data research
* **ergonomy_joe** - code reverse engineering
* **picklejar** - world script research
* **FF7 Wiki authors** - invaluable resource for learning about FF7 internals
* **... and the whole Qhimm and FF7 community.**

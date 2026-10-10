# ykkz000's DLL Plugin API

[简体中文](README.zh_cn.md)

Provides the shared C-ABI services consumed by ykkz000 DLL plugins: the **EffectType API** (`ykkz000_plugin_api_effecttype`) and the **Persistence API** (`ykkz000_plugin_api_persistence`). Both are themselves plugins: they export `GetPlugin`, `DestroyPlugin`, and `GetPluginManifest`, and the loader initializes them before the consumers that declare them as manifest dependencies.

## Provided modules

- **EffectType API** (`ykkz000_plugin_api_effecttype.dll`): a versioned, append-only POD function table (`GetEffectTypeApi`) for engine access, validated memory access, player resolution, logging, and custom EffectType registration. It also publishes the shared `ykkz000/plugin/{effect.h,effecttype_api.h,engine_access.h}` headers.
- **Persistence API** (`ykkz000_plugin_api_persistence.dll`): a versioned, append-only POD function table (`GetPersistenceApi`) for variable-description-driven persistence of custom int-vector AutoVariables on engine City objects plus City/Unit lifecycle notifications.

No third-party code is statically linked into either plugin (`natives/vcpkg.json` has no dependencies).

## Requirements

- *Sid Meier's Civilization VI* with the **Gathering Storm (Expansion 2)** ruleset.
- Enable this mod **together with** `ykkz000's DLL Plugin Loader` (the loader discovers and initializes these API plugins) and any consumer mods.

## Build Environment

- Windows x64.
- PowerShell 5.1 or later (`.tools/build.ps1` starts with `#Requires -Version 5.1`).
- CMake 3.21 or later (`cmake_minimum_required(VERSION 3.21)` in `natives/CMakeLists.txt`).
- MSVC with the v143 toolset, C++20, x64. The provided `natives/CMakePresets.json` presets (`x64-v143`, `x64-ninja`) can be used for a manual CMake configure.
- vcpkg (manifest mode). Even with no dependencies the build passes the vcpkg toolchain; clone and bootstrap vcpkg and set `VCPKG_ROOT`:
  ```powershell
  git clone https://github.com/microsoft/vcpkg C:\vcpkg
  & C:\vcpkg\bootstrap-vcpkg.bat
  [Environment]::SetEnvironmentVariable('VCPKG_ROOT', 'C:\vcpkg', 'User')
  ```

The API mod has no art assets, so a full build does not require the Civilization VI SDK/Assets or the asset cooker; pass `--skip=art`.

## Building from Source

Build with `.tools/build.ps1`. Quote the project file because its path contains spaces and an apostrophe:

```powershell
# Build both API DLLs and generate the .modinfo
& ".\.tools\build.ps1" "ykkz000's DLL Plugin API.civ6proj" --skip=art

# Native code unchanged, only regenerate the mod package
& ".\.tools\build.ps1" "ykkz000's DLL Plugin API.civ6proj" --skip="dll,art"
```

- Default output: `<Documents>\My Games\Sid Meier's Civilization VI\Mods\ykkz000's DLL Plugin API`; override with `-o <path>` / `--output <path>`.
- Deployed artifacts: `Binaries/Win64/ykkz000_civ6_plugin/ykkz000_plugin_api_effecttype.dll` and `.../ykkz000_plugin_api_persistence.dll`.

## License

Licensed under the MIT License (see `LICENSE`). The license applies to the source code only (see `NOTICE`).

# ykkz000的DLL 插件 API

[English](README.md)

为 ykkz000 的 DLL 插件提供共享的 C ABI 服务：**EffectType API**（`ykkz000_plugin_api_effecttype`）与**持久化 API**（`ykkz000_plugin_api_persistence`）。二者本身也是插件：导出 `GetPlugin`、`DestroyPlugin` 与 `GetPluginManifest`，loader 会在声明依赖它们的 consumer 之前初始化它们。

## 提供的模块

- **EffectType API**（`ykkz000_plugin_api_effecttype.dll`）：版本化的、只增不改的 POD 函数表（`GetEffectTypeApi`），提供引擎访问、校验式内存读写、玩家解析、日志与自定义 EffectType 注册；同时发布共享头 `ykkz000/plugin/{effect.h,effecttype_api.h,engine_access.h}`。
- **持久化 API**（`ykkz000_plugin_api_persistence.dll`）：版本化的、只增不改的 POD 函数表（`GetPersistenceApi`），提供基于变量描述的引擎 City 自定义 int 向量 AutoVariable 持久化，以及 City/Unit 生命周期通知。

两个插件均未静态链接第三方代码（`natives/vcpkg.json` 无依赖）。

## 依赖

- 需《席德·梅尔的文明 VI》的**风云变幻（Expansion 2）**规则集。
- 请将本模组与 `ykkz000的DLL 插件加载器`（由 loader 发现并初始化这些 API 插件）以及各 consumer 模组**同时启用**。

## 构建环境配置

- Windows x64。
- PowerShell 5.1 或更高版本（`.tools/build.ps1` 以 `#Requires -Version 5.1` 开头）。
- CMake 3.21 或更高版本（`natives/CMakeLists.txt` 中的 `cmake_minimum_required(VERSION 3.21)`）。
- MSVC 的 v143 工具集、C++20、x64。可使用 `natives/CMakePresets.json` 提供的 `x64-v143`、`x64-ninja` preset 手动进行 CMake 配置。
- vcpkg（清单模式）。即使无依赖，构建也会传入 vcpkg toolchain；克隆并引导 vcpkg，然后设置 `VCPKG_ROOT`：
  ```powershell
  git clone https://github.com/microsoft/vcpkg C:\vcpkg
  & C:\vcpkg\bootstrap-vcpkg.bat
  [Environment]::SetEnvironmentVariable('VCPKG_ROOT', 'C:\vcpkg', 'User')
  ```

本模组无美术资源，因此完整构建不需要《文明 VI》SDK/Assets 与资源 cooker；传入 `--skip=art`。

## 从源码构建

使用 `.tools/build.ps1` 构建。项目文件路径包含空格与撇号，因此需要加引号：

```powershell
# 构建两个 API DLL 并生成 .modinfo
& ".\.tools\build.ps1" "ykkz000's DLL Plugin API.civ6proj" --skip=art

# 未改 native 代码，仅重新生成模组包
& ".\.tools\build.ps1" "ykkz000's DLL Plugin API.civ6proj" --skip="dll,art"
```

- 默认输出：`<Documents>\My Games\Sid Meier's Civilization VI\Mods\ykkz000's DLL Plugin API`；可用 `-o <path>` / `--output <path>` 覆盖。
- 部署产物：`Binaries/Win64/ykkz000_civ6_plugin/ykkz000_plugin_api_effecttype.dll` 与 `.../ykkz000_plugin_api_persistence.dll`。

## 许可证

本项目在 MIT 许可证下授权（见 `LICENSE`）。该许可证仅适用于源代码（见 `NOTICE`）。

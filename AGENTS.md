# Agent Instructions for ykkz000's DLL Plugin API

## 1. 项目概述

本项目是《Sid Meier's Civilization VI》（以下简写为“文明6”）的一个模组，提供共享的插件 API 插件：`ykkz000_plugin_api_effecttype` 与 `ykkz000_plugin_api_persistence`。二者导出 `GetPlugin`/`DestroyPlugin`/`GetPluginManifest`，由 Loader 作为插件初始化，以便在 consumer 之前捕获 `Host`。

- **技术栈**: C++, MSVC, CMake, vcpkg
- **架构**: 插件 DLL，游戏模组（无法独立运行，需要在游戏内加载）

## 2. 项目目录

项目目录为根项目目录下的`natives`目录

## 3. 测试

项目无法在脱离游戏本体的环境下运行，故无自动测试

## 4. 项目风格与规范

- 所有风格和规范**均不对第三方代码和文件生效**
- 所有注释使用美式英文

### C++ 代码风格与规范

- 对于由头文件定义的对外暴露的函数和类型需要使用Doxygen注释
- 对于类中`public`和`protected`成员变量和成员方法需要使用Doxygen注释
- 代码应当解耦，不应当出现属于特定模块的逻辑的代码出现在其它模块
- 所有日志输出使用美式英文
- 编译的二进制文件始终携带Copyright信息和许可协议信息（许可协议同项目许可协议，仅提供许可协议名即可）

#### 宏规范

- **优先**使用C++预定义宏、编译器预定义宏和业界常用宏
- 对于由头文件定义的作为API的宏统一使用`YKKZ000_`前缀，而在编译时传入的宏则不使用`YKKZ000_`前缀

#### 公共头文件规范

- 所有项目定义的类型和结构均在`ykkz000`命名空间
- 单一头文件**只能**涉及一个命名空间内的函数、类型等定义
- 所有公共头文件的路径结构**必须**与命名空间结构相同

#### Shared ABI headers

- `natives/include/ykkz000/bridge/**`、`export.h` 与 `natives/include/ykkz000/civ6/**` 是共享 ABI 头文件，其权威源为 `ykkz000's DLL Plugin Loader` 仓库；本仓库内置的是副本，变更时需同步。
- `natives/plugin-*/include/ykkz000/plugin/**`（API 头）是本仓库**权威源**；其它仓库（consumer）内置的是副本，变更时需同步。

##### API 插件规范

- 共享插件 API（如`ykkz000_plugin_api_effecttype`、`ykkz000_plugin_api_persistence`）本身是插件：它们导出`GetPlugin`/`DestroyPlugin`/`GetPluginManifest`，由Loader 作为插件初始化以捕获`Host`
- API 插件**禁止**包含与游戏玩法相关的具体逻辑；它们只提供通用的、与玩法无关的服务（如引擎访问、持久化、生命周期通知）
- API 通过C-ABI的版本化入口导出（如`GetEffectTypeApi(uint32)`、`GetPersistenceApi(uint32)`），返回**只增不改**的POD函数表；**禁止**跨DLL边界传递STL类型、异常或由分配器拥有的对象
- consumer **必须**在清单中声明对 API 插件的依赖，使其先于自身初始化，并直接`import` API入口函数
- API 插件**禁止**链接第三方库（不静态链接 MinHook/log4cxx 等）；如需第三方能力，应通过 Host 提供的服务实现

### 插件契约

- 每个插件**必须**导出`GetPluginManifest`，声明唯一逻辑名`name`、依赖插件名数组`dependencies`与所需最低Host API版本`requiredHostApi`
- `PluginManifest`仅可追加字段，已发布字段的含义**禁止**更改
- 初始化遵循**两阶段**：先加载全部候选DLL，再按拓扑序调用`GetPlugin`

## 5. Git 规则

- **提交信息**: 遵循 Conventional Commits
- **提交控制**：仅暂存流程中的修改的文件，提交后上传到远程仓库

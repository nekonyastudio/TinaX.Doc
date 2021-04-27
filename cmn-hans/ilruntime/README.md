# TinaX.ILRuntime

`TinaX.ILRuntime`为TinaX Framework提供了使用C#语言编写可热更新功能的能力。

- Powerd by [ILRuntime](https://github.com/Ourpalm/ILRuntime)

Package name : `io.nekonya.tinax.ilruntime`

- 基础命名空间: `TinaX.XILRuntime`
- 项目仓库：[https://github.com/yomunsam/TinaX.ILRuntime](https://github.com/yomunsam/TinaX.ILRuntime)
- 服务提供者:`XILRuntimeProvider` （命名空间:`TinaX.XILRuntime`）

?> 为了避免一些命名冲突，我们在自己的代码中`ILRuntime`前面大多加了`X`字母，为`XILRuntime`.

可用于依赖注入的接口或类

|   Type    |  命名空间  |   说明    |   备注    |
|-----------|-----------|-----------|-----------|
|`IXILRuntime`|`TinaX.XILRuntime`| 提供ILRuntime配置以及与热更工程进行交互功能的接口 | - |

## 依赖需求

使用`TinaX.ILRuntime`包，请确保项目中有相关功能实现了TinaX 内置资产服务接口`IAssetService`(位于包`TinaX.Core`,包`TinaX.Services`).

- 直接使用[TinaX.VFS](/cmn-hans/vfs/README.md)包即可.
- 如果您使用诸如`Addressables`等第三方资产加载功能，请访问文档了解[内置资产服务接口](/cmn-hans/core/manual/IAssetService.md)



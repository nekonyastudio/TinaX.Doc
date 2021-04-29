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

<br>


## 安装特殊说明

您可访问此处文档查看关于TinaX的安装方式：[安装TinaX](/cmn-hans/tinax/install/install_tinax.md)

其中要注意的是，如果您使用`Unity Package Manaer (UPM)`方式安装项目时，需要在`npmjs`的registry的`scopes`配置中添加ILRuntime相关的scope, 完整写法如下：

``` json
"scopedRegistries": [
    {
        "name": "TinaX",
        "url": "https://registry.npmjs.org",
        "scopes": [
            "io.nekonya",
            "com.ourpalm"
        ]
    },
    {
        "name": "package.openupm.com",
        "url": "https://package.openupm.com",
        "scopes": [
            "com.cysharp.unitask",
            "com.neuecc.unirx"
        ]
    }
],
```

?> 使用npm淘宝镜像同理.

?> 大陆地区使用的Unity Editor版本较为特殊，其第一方registry中内置了ILRuntime相关内容. 如果您的项目能确保所有参与开发人员都处于大陆地区，然后使用时没什么问题的话，可以不用管上述内容.
# TinaX.VFS <!-- {docsify-ignore-all} -->

`TinaX.VFS`是TinaX Framework默认的资产管理服务包. VFS : Virtual Files System, 虚拟文件系统。
- 根据Unity Asset Path加载资产，
- 无感知的AssetBundle管理
- AssetBundle打包
- 资产热更新

package name: `io.nekonya.tinax.vfs`

- 基础命名空间：`TinaX.VFSKit`
- 项目源码仓库：[https://github.com/yomunsam/TinaX.VFS](https://github.com/yomunsam/TinaX.VFS)
- 服务提供者：`VFSProvider` （命名空间：`TinaX.VFSKit`）

<br>

可用于依赖注入的接口或类：

|   Type    |  命名空间  |   说明    |   备注    |
|-----------|-----------|-----------|-----------|
|`IVFS`|`TinaX.VFSKit`| VFS中加载、卸载资产的主要服务接口 | - |
| `IAssetService` |`TinaX.Services`| `TinaX.Core`内置服务接口 | 位于`TinaX.Core`包|

服务接口Facade: 

``` csharp
TinaX.VFSKit.VFS //可通过该类直接访问IVFS类而无需依赖注入。
```

## 安装

### 使用[OpenUPM](https://openupm.com/)安装

``` bash
# Install openupm-cli if not installed.
npm install -g openupm-cli
# OR yarn global add openupm-cli

#run install in your project root folder
openupm add io.nekonya.tinax.vfs
```

<br>

### 通过npm安装 (UPM)

修改您的工程中的`Packages/manifest.json` 文件，并在文件的"dependencies"节点前添加如下内容：

``` json
"scopedRegistries": [
    {
        "name": "TinaX",
        "url": "https://registry.npmjs.org",
        "scopes": [
            "io.nekonya"
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

如果在进行上述操作后，您仍然未能在"Unity Package Manager"窗口中找到TinaX的相关Packages，您也可以尝试刷新、重启编辑器，或手动添加如下配置到"dependencies":

``` json
"io.nekonya.tinax.vfs" : "6.6.3"
```

<br>

### 通过Git方式安装(UPM)

你可使用如下地址在Unity Package Manager窗口中安装本包： 

```
git://github.com/yomunsam/TinaX.VFS.git
```

如果您想手动指定安装某个本本, 您可以使用 release tag, 例如 `#6.6.3`. 或访问Release页面了解细节: [https://github.com/yomunsam/TinaX.VFS/releases](https://github.com/yomunsam/TinaX.VFS/releases)

如不指定版本，Unity将会安装当前git仓库中的最新版本，可能会造成兼容性错误。


<br>

>关于安装TinaX的详细说明和问题，请参考文档页面：[在项目中安装TinaX](cmn-hans/install)


### 依赖

本项目（包）直接依赖以下包

- [com.neuecc.unirx](https://github.com/neuecc/UniRx#upm-package) :`https://github.com/neuecc/UniRx.git?path=Assets/Plugins/UniRx/Scripts`
- [com.cysharp.unitask](https://github.com/Cysharp/UniTask#install-via-git-url) :`https://github.com/Cysharp/UniTask.git?path=src/UniTask/Assets/Plugins/UniTask`
- [io.nekonya.tinax.core](https://github.com/yomunsam/tinax.core) :`git://github.com/yomunsam/TinaX.Core.git`

> 如果您通过Git方式安装Package，那么您需要手动确保所有依赖项已被安装。如果使用NPM/OpenUPM安装本Package，则所有依赖都将自动被安装。 

<br><br>


## 功能

- [通过VFS管理资产](/cmn-hans/vfs/manual/IVFS.md)


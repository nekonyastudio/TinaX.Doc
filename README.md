# TinaX

Documentation is being written.

> Sorry, English is not the author ’s native language, writing English documentation is more difficult for the author. 
>
> This means that English documents will be completed slightly later than **[Chinese documents](/cmn-hans/)**. 
>
> Of course, if you can, please help us to participate in the preparation of English documentation.

Unity-based Game Client Framework

[![LICENSE](../_media/996icu_license.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE)
<a href="https://996.icu"><img src="https://img.shields.io/badge/link-996.icu-red.svg" alt="996.icu"></a>
[![LICENSE](../_media/AGPL3_license.svg)](https://github.com/yomunsam/TinaX/blob/master/LICENSE)

TinaX Framework is a simple, complete, out-of-the-box development framework based on the Unity engine. TinaX's goal is to be a framework that adapts to different project and team sizes.

## Environmental Requirements

**Unity**
- Version used to develop TinaX: `Unity 2019.3.7f1`
- Recommended version for production: `Unity 2019.4.x LTS`
- Minimum compatible version of theory: `Unity 2019.3.x`

**C#**
- Unity Api Compatibility Level: `.NET 4.X`/`.NET Standard 2.0` 
- C# `7.2`

## Functional Overview

**Packages**

TinaX Framework uses packages to organize and manage modules of various functions. In order to facilitate the direct installation using tools such as UPM, different packages are stored in their own separate repo. See details below:

**Services**

A single-function module distributed in each package in TinaX is called a `Service`.

### TinaX.Core

`TinaX.Core` is the basic package of TinaX. It is responsible for startup and managing all services, providing common basic functions, providing event system, dependency injection, etc.

- Repository: [https://github.com/yomunsam/TinaX.Core](https://github.com/yomunsam/TinaX.Core)
- Package Name: `io.nekonya.tinax.core`

<br>

### TinaX.VFS

Virtual File System (VFS) is TinaX's assets management service. It simulates the directory structure of "`Assets/xxx`" in the Unity project at runtime, and implements assets loading, dependency management, version management and update, memory gc, etc.

- Repository: [https://github.com/yomunsam/TinaX.VFS](https://github.com/yomunsam/TinaX.VFS)
- Package Name: `io.nekonya.tinax.vfs`

<br>

### TinaX.UIKit

`UIKit` provides uGUI-based UI management services for TinaX, such as open, close, hide the UI, avoiding full-screen UIs from each other, and UI startup parameters.

And it provides related content such as component extensions and extensible UI animations.

- Repository: [https://github.com/yomunsam/TinaX.UIKit](https://github.com/yomunsam/TinaX.UIKit)
- Package Name: `io.nekonya.tinax.uikit`

<br>

### TinaX.I18N

I18N (Internationalized) support services based on the form of `key / value` make your application more friendly to users in different languages and regions around the world.

- Repository: [https://github.com/yomunsam/TinaX.I18N](https://github.com/yomunsam/TinaX.I18N)
- Package Name: `io.nekonya.tinax.i18n`

<br>

### TinaX.Lua

Provides Lua language runtime for TinaX. This service is based on `Tencent/xLua`.

- Repository: [https://github.com/yomunsam/TinaX.Lua](https://github.com/yomunsam/TinaX.Lua)
- This content needs to be directly imported into the project `Assets` directory, not provided as a package.

<br>

### TinaX.ILRuntime

Provides hot-updatable C# runtime for TinaX. The service is based on `ILRuntime`.

- Repository: [https://github.com/yomunsam/TinaX.ILRuntime](https://github.com/yomunsam/TinaX.ILRuntime)
- Package Name: `io.nekonya.tinax.ilruntime`

<br>

------

## Excellent Unity project recommendation

- **[QFramework](https://github.com/liangxiegame/QFramework)** : Your first K.I.S.S Unity 3D Framework.
- **[xasset](https://github.com/xasset/xasset)** : A lightweight assets management framework.
- **[CatLib](https://github.com/CatLib/Core)** : CatLib lightweight dependency injection container
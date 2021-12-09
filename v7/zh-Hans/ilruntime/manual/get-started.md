# 开始使用TinaX.ILRuntime

## 开始之前

在开始之前，我们需要对ILRuntime本身有所认知，并对ILRuntime的常见概念，包括跨域继承、委托、CLR重定向等概念有所了解。

您可在ILRuntime官方文档中了解相关知识：[https://ourpalm.github.io/ILRuntime](https://ourpalm.github.io/ILRuntime)

## 安装Package

TinaX.ILRuntime的包名为：`io.nekonya.tinax.ilruntime`, 您可在npmjs、openupm等平台找到它，或使用其他方式安装。

?> 预览版文档暂不详细介绍基础操作，或请查看当前正式版文档对应内容。

## 添加模块

安装好包之后，我们需要调用`.AddILRuntime()`方法将其添加到框架的[XCore](/zh-Hans/core/manual/xcore)中。

``` csharp
var core = XCore.CreateDefault()
    .AddILRuntime(); //添加TinaX.ILRuntime模块功能到框架中
await core.RunAsync();
```

?> 通常，TinaX框架所有模块往XCore添加功能时的"AddXXX()"方法都位于`TinaX.Services`命名空间中。

## 热更工程

使用ILRuntime时，我们通常会创建一个（或多个）.NET 类库作为热更工程，并编译出dll文件供ILRuntime运行。

我们可以使用Visual Studio 、Jetbrains Rider等IDE工具创建类库，或使用命令行工具`dotnet new classlib -o projectName` 创建类库

### 热更工程的.NET版本

ILRuntime的官方示例中，热更工程为 `.NET Framework` 类库。而实测发现，使用`.NET Strandard`以及新版.NET（`.NET 6`）依然是运行的。

并且不一定是C#类库，使用.NET平台的其他语言如F#也是可以的。

**但并不保证完全兼容，请在了解ILRuntime原理的前提下自行探索尝试**
# TinaX ILRuntime

## ILRuntime

[ILRuntime](https://github.com/Ourpalm/ILRuntime ':ignore')项目为基于C#的平台（例如Unity）提供了一个`纯C#实现`，`快速`、`方便`且`可靠`的IL运行时，使得能够在不支持JIT的硬件环境（如iOS）能够实现代码的热更新.

?> 您可访问[LRuntime官方文档](https://ourpalm.github.io/ILRuntime/ ':ignore')了解该项目

## TinaX.ILRuntime

TinaX 框架提供了`TinaX.ILRuntime`模块，以提供一个开箱即用的ILRuntime环境。

`TinaX.ILRuntime`以Unity Package形式提供并依赖ILRuntime官方Package（包名:`com.ourpalm.ilruntime`）.

`TinaX.ILRuntime`模块包括以下内容：
1. 一个调校好的立即可用的ILRuntime环境
2. 为`TinaX.Core`包的功能提供了适配，包括依赖关系注入等。
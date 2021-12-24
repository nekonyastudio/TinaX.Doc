# TinaX 框架模块 <!-- {docsify-ignore-all} -->

TinaX Framework 使用 Packages （包）的形式来组织管理各个功能单元，通常一个包我们称之为模块（Module）。通过安装模块包，即可向项目中添加各种功能。

关于TinaX Framework的模块汇总如下。


### 核心模块 :id=core-module

#### [TinaX.Core](/zh-Hans/core/README)

`TinaX.Core`模块是框架的基础模块，是框架一切功能的宿主和入口。同时提供诸如依赖关系注入（DI）、事件广播系统等基础功能。

<br>

### 程序运行时 :id=runtime

Unity引擎通常使用C#语言进行业务开发。但实际项目开发中，自带的语言开发环境往往无法满足诸如热更新等特殊需求，于是在中大型项目的实际生产实践中，我们往往需要在项目中实现更多的程序语言运行时环境。

TinaX 框架第一方适配了如下运行时环境：

#### [TinaX.ILRuntime](/zh-Hans/ilruntime/README)

<br>

### UI套件

#### [TinaX.UIKit](/zh-Hans/uikit/README)

`TinaX.UIKit`是框架的UI模块。

<br>

------

| 模块 | 提供形式 | 包名 | 说明 |
|------|---------|------|-----|
|TinaX.Core | `package` | `io.nekonya.tinax.core` | 核心模块|
|TinaX.ILRuntime | `package` | `io.nekonya.tinax.ilruntime` | ILRuntime支持模块 |
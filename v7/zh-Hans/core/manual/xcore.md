# XCore

XCore是TinaX Framework的基础概念。它作为整个框架的宿主程序，提供了包括如下功能：
- 依赖关系注入（DI）
- 配置（Configuration）
- 跟随框架的应用生命周期

<br>

## 设置XCore

首先，让我们来编写代码设置并启动一个`XCore`.

``` csharp
using TinaX;
using UnityEngine;

namespace Nekonya
{
    public class GameStartup : MonoBehaviour
    {
        async void Start()
        {
            var core = XCore.CreateDefault();
            await core.RunAsync();
        }
    }
}
```

这是一个常见的继承自Unity的`MonoBehaviour`的类，在其`Start()`方法中我们设置并启动了`XCore`. 其中启动XCore的代码`core.RunAsync()`返回值是一个可等待类型，因此我们使用`async/await`写法（参考：[使用 Async 和 Await 的异步编程](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/async/ ':ignore') ）

将上述编写的代码组件挂在到某个GameObject上之后，点击运行将会启动框架。但单纯启动了XCore之后，整个框架并没有表现出某些功能，而在实际使用中，我们通常需要把一些模块添加到XCore中。

<br>

## 添加模块

在启动XCore之前，我们可以设置添加一些模块，使得框架能够拥有实际功能：

``` csharp
using TinaX;
using TinaX.Services;
using UnityEngine;

namespace Nekonya
{
    public class GameStartup : MonoBehaviour
    {
        async void Start()
        {
            var core = XCore.CreateDefault()
                .AddPuerts()
                .AddILRuntime()
                .AddVFS();
            await core.RunAsync();
        }
    }
}
```

如上代码，`.AddILRuntime()`为XCore添加了[ILRuntime](https://github.com/Ourpalm/ILRuntime ':ignore')第三方功能的接入, 使得框架可以使用C#语言进行热更新。

`.AddPuerts()`为XCore添加了[Puerts](https://github.com/Tencent/puerts)第三方功能的接入，似的框架可以用`JavaScript`/`TypeScript`语言开发业务。 

`.AddVFS()`为XCore添加了[TinaX.VFS](/zh-Hans/vfs/README) 模块，使得框架可以进行资产的动态加载和热更新。

?> 请注意，上述的`AddILRuntime()`、`AddPuerts()` 和 `AddVFS()`代码均需要额外安装框架模块包才可使用，详请查阅相关文档。本文旨在介绍XCore相关功能。

除此之外，我们也可以开发自己的模块加入XCore，以扩展框架的功能。


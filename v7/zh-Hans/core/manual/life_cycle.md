# TinaX 生命周期

比如说，我们给XCore添加了`UI模块`、`资产管理模块`和`ILRuntime接入模块`。

如果我们需要使用UI模块的功能的话，就得等待UI模块启动完成；

而又比如我们需要调整资产管理模块的启动设置的话，就需要在资产管理模块启动之前对其进行一些操作；

上述的`UI模块`在加载UI时、`ILRuntime接入模块`在加载程序集时，也需要依赖`资产管理模块`提供的资产加载能力，也因此`资产管理模块`必须在前两者启动之前就启动完成。

如果手动管理这些程序的初始化和启动过程的话，必然是混乱而麻烦的，因此TinaX设计有生命周期功能，它会在XCore启动时，管理启动所有的模块等内容。

<br>

## 启动代码中的简单生命周期

对于不复杂的业务场景，我们可以简单的了解一下设置和启动XCore代码时的简单生命周期状态（在下文代码中以注释形式标注）：

``` csharp
using TinaX;
using TinaX.Services;
using UnityEngine;

namespace Nekonya
{
    public class GameStart : MonoBehaviour
    {
        async void Start()
        {
            var core = XCore.CreateDefault(); //XCore本体创建

            //添加模块： 此时模块都是未启动的状态
            core.AddILRuntime();
            core.AddVFS();

            //注册服务（依赖注入）
            core.Services.Singleton<IHelloService, HelloService>();
            var hello = core.Service.Get<IHelloService>(); //服务注册完成后立即可用

            //启动XCore
            await core.RunAsync(); //在此阶段会完成之前添加的所有模块的初始化和启动工作

            //这里是XCore启动完成后，可以使用之前添加的模块中的功能
        }
    }
}
```


<br>

而对于较为复杂的业务场景，我们可能需要一些更加严谨和精细方式去掌握框架的生命周期。TinaX提供了两种方式：[IXBootstrap 接口](/zh-Hans/core/manual/ixbootstrap) 和 [XCore Behaviour](/zh-Hans/core/manual/xcore_behaviour).

其中，`IXBootstrap` 接口设计于`TinaX 6.6.x`版本，`XCore Behaviour`为`TinaX 7.x`版本新加入功能。
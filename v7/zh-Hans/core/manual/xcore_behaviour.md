# XCore Behaviour

XCore Behaviour是TinaX 7.x 版本中引入的XCore生命周期方式，它提供如下活动状态：
- Awake : 在`模块`初始化之后、启动之前阶段被触发.
- Start : 在`模块`启动之后，框架启动完成后被触发.
- OnQuit : 在框架退出前被触发.

<br>

## 编写Start代码

我们尝试编写代码，以在模块启动之后执行下一步的业务逻辑:

``` csharp
using TinaX.Core.Behaviours;
using UnityEngine;

public class GameStart : IStart
{
    public int StartOrder { get; set; } = 0;

    public void Start()
    {
        Debug.Log("框架启动完了");
    }
}
```
上文代码中，我们编写了类`GameStart`继承自`TinaX.Core.Behaviours.IStart`接口，并实现了`StartOrder {get; set;}`属性和`Start()` 方法.

接着，向XCore注册我们所写的类：

``` csharp
//已省略部分代码
var core = XCore.CreateDefault();
core.Behaviour.Register(new GameStart()); //注册GameStart类
await core.RunAsync();
```

至此，当框架启动完成之后，`GameStart`类的`Start()`方法将被调用。其他`Awake`、`OnQuit`等内容的写法类似。

<br>

## 异步方法

XCore Behaviour的接口提供了异步版本，以便我们可以编写异步方法，并让框架在对应阶段等待我们的代码执行完成。

``` csharp
using System;
using System.Threading;
using Cysharp.Threading.Tasks;
using TinaX.Core.Behaviours;
using UnityEngine;

public class GameStartAsync : IStartAsync
{
    public int StartOrder { get; set; } = 0;

    public async UniTask StartAsync(CancellationToken cancellationToken = default)
    {
        await UniTask.Delay(TimeSpan.FromSeconds(3)); //模拟耗时操作
    }
}
```

异步方法采用`UniTask`（命名空间：`Cysharp.Threading.Tasks`）作为返回值，其来自第三方开源项目[Cysharp/UniTask](https://github.com/Cysharp/UniTask), 为Unity生态提供了0GC开销的async/await异步实现，使用方法与 .NET 的`ValueTask`类似（Unity生态的C#中没有`ValueTask`，亦可以**不严谨的**理解为与`Task`类似）

<br>

## 次序

假设我们有一个复杂的业务被分成A、B两部分，A、B两部分都注册了XCore Behaviour的同一种生命周期状态比如`Start`，其中B部分必须应该等待A部分的逻辑处理完成之后再处理，这时候我们就可以给两者注册的`Start`指定次序。

接口中的`int StartOrder { get; set; }`属性用于指定该次序，其数值默认情况下约定为`0`，数值越小的注册内容会靠前被执行，数值越大的内容会靠后执行。当数值相同时，其执行顺序不可控。

当同次序中存在多个异步注册项时，它们将在几乎同时被触发，并等待同次序中耗时最长的异步方法执行结束后，继续执行下一次序的内容。
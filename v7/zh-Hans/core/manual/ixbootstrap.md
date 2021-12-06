# IXBootstrap

IXBootstrap是TinaX 6.6.x 版本中引入的XCore生命周期方式, `IXBootstrap`是一个接口（interface）,位于命名空间`TinaX`：

``` csharp
namespace TinaX
{
    public interface IXBootstrap
    {
        void OnInit(IXCore core); //在模块初始化结束之后、启动之前的阶段被触发
        
        void OnStart(IXCore core); //在模块启动之后、框架启动完成后被触发

        void OnQuit(); //框架退出前被触发

        void OnAppRestart(); //框架尝试重启时触发
    }
}
```

我们可以编写继承自`IXBootstrap`接口的类，以接收生命周期事件：

``` csharp
using TinaX;
using UnityEngine;

public class GameBootstrap : IXBootstrap
{
    public void OnAppRestart() { }

    public void OnInit(IXCore core) { }

    public void OnStart(IXCore core) { }

    public void OnQuit() { }
}
```

编写完成后，不需要明确的向XCore注册该类。XCore将在启动阶段主动寻找它。


!> 请注意，继承自`IXBootstrap`的类必须位于主工程（`Assembly-CSharp`）才会生效。

<br>

## 禁用IXBootstrap

在XCore启动之前，设置其`ReflectIXBootstrap`属性为`false`可禁用该功能。

``` csharp
var core = XCore.CreateDefault();
core.ReflectIXBootstrap = false;
await core.RunAsync();
```

使用`XCore.CreateDefault()`方法创建的`XCore`实例，其`ReflectIXBootstrap`属性默认为`true`.
而直接使用`new XCore()`方式创建实例，其`ReflectIXBootstrap`属性默认为`false`.

<br>

## 对比XCore Behaviour

对比TinaX 7.x版本引入的`XCore Behaviour`，有如下差异：
- `IXBootstrap`方式没有显示的向XCore注册，而是在XCore启动阶段由框架反射查找所有接口的实现。在开发阶段省事，但容易在IL2CPP编译时被裁剪导致程序异常。
- `IXBootstrap`不支持异步等待。
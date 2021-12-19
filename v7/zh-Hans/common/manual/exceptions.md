# TinaX中的异常（Exception）

Unity传统的编程方式，尤其使用Lua语言编程时通常是没有使用异常（Exceptions）的习惯的, 而TinaX框架主要依靠抛出异常来处理错误。

?> 关于C#的异常处理请参阅文档[异常和异常处理](https://docs.microsoft.com/dotnet/csharp/fundamentals/exceptions/ ':ignore :target=_blank').

我们可以在调用TinaX框架内方法时，捕获并处理异常，例如：

``` csharp
using System;
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

            try
            {
                await core.RunAsync();
            }
            catch(Exception ex)
            {
                //Do something....
            }
        }
    }
}
```

<br>

## 就让它崩溃

尽管很多编程语言都设计了异常处理机制，但仍然有一种看似与之冲突的编程思想被提出："Let it Crash"（任其崩溃）。

TinaX的设计上一定程度的吸收了该思想，于是框架可能会抛出两种异常：一种是继承自`TinaX.Exceptions.XException`类的异常，另一种是其他异常。

继承自`XException`类型的异常，通常被认为是“无伤大雅的”、“可在掌控范围内”的异常，我们推荐可在运行时捕获并回退处理这一类异常。

而对于其他类型的异常，不推荐捕获异常，请让相应的程序流程崩溃以便在开发阶段解决掉它们。
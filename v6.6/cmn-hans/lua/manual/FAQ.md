# TinaX.Lua FAQ

<br>

### 如何调试Lua代码

#### Lua Perfect

以[Lua Perfect](https://luaperfect.net/)为例，启用调试时我们需要在代码中运行:
``` lua
require('LuaDebuggee').StartDebug('127.0.0.1', 9827)
```

这句话很好理解，require一个文件，然后调用它。但是问题就来了，这个文件在哪呢，它给放在了Unity工程的根目录下, 而TinaX大概率是加载不到这个文件的。

这时候我们就需要针对这个文件自定义它的加载行为。我们在继承自`TinaX.IXBootstrap`接口的类中编写如下代码：

``` csharp
using TinaX;
using TinaX.Lua;

namespace Nekonya.Example
{
    public class AppBootstrap : IXBootstrap
    {
        public void OnInit(IXCore core)
        {
            core.Services.Get<ILua>().ConfigureCustomLoadHandler(options =>{
                options.Add("LuaDebuggee", () => null); 
            });
        }

        public void OnStart(IXCore core){ }
        public void OnQuit() {}
        public void OnAppRestart() {}
    }
}
```

`IXBootstrap.OnInit`方法会在所有服务都完成初始化并注册了依赖注入容器之后、正式启动之前被调用。（简单的说就是我们需要在TinaX.Lua服务正式启动之前配置相关内容）。

#### LuaIde

LuaIde是一个Visual Studio Code的插件。LuaIde调试时也需要require自己的文件。

这里我们也可以换一个方式，把luaIde的调试文件导入到可以被`TinaX.Lua`直接require到的位置，然后就可以正常使用了。

#### 其他

其他调试工具的接入方法类似.

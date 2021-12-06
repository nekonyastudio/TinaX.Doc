# DisposableGroup

> 该功能最早出现于 TinaX.Core 版本 [6.6.2-preview](https://github.com/yomunsam/TinaX.Core/releases/tag/6.6.2-preview)

# 概述

DisposableGroup 用于统一管理多个实现了`System.IDisposable`接口的对象，并可统一执行`Dispose()`方法.

这是个非常简单的小工具。

<br>

# 使用

当我们需要管理多个实现了`System.IDisposable`接口的对象时，通常我们需要挨个调用它们的`Dispose()`方法，而DisposableGroup所提供的功能就是，把所有的对象都收集起来，然后在需要的时候统一调用一次Dispose()方法。

## 注册对象

方法原型：

``` csharp
DisposableGroup Register(IDisposable obj, bool InvokeDisposeOnceIfExist = false);
```

|参数 | 类型 |说明|
|-----|----|----|
|obj | `System.IDisposable` | 被注册的对象|
| InvokeDisposeOnceIfExist | `bool` | 如果要注册的对象已经存在，则调用一次Dispose |

?> `InvokeDisposeOnceIfExist` 参数 的作用详见后续文档。

返回值 `DisposableGroup` 为当前所被调用的DisposableGroup对象的自身。

## Dispose()

当`DisposableGroup`的`Dispose()`方法被调用时，所有之前注册进去的实现了`System.IDisposable`接口的对象的`Dispose()`方法都会被调用。

由于`DisposableGroup`本身的`Dispose()`也是来自`System.IDisposable`接口的，所以可以对DisposableGroup使用各种C#语法糖。

<br>

# 约定

约定TinaX中实现了`System.IDisposable`的类或接口，如有需要的话，尽可能提供针对`DisposableGroup`的扩展方法。

例如我们可以使用针对[XEvent事件系统](/cmn-hans/core/manual/event.md)的扩展方法`.RegisterEvent()`来更便捷的注册事件监听并同时将[IEventTicket](/cmn-hans/core/manual/event.md#IEventTicket)注册给DisposableGroup.

<br>

# 最佳实践用法

我们经常遇到在Unity中编写继承自MonoBehaviour的类的情况。并且我们知道，我们在继承自MonoBehaviour的类中注册的事件监听、加载的资源等内容，需要在`OnDestroy()`方法中取消注册或释放资源。

当不使用DisposableGroup时，如下案例：

``` csharp
using TinaX;
using TinaX.Systems;
using TinaX.VFSKit;
using UnityEngine;
using UnityEngine.UI;

namespace Nekonya.Demo
{
    class Meow : MonoBehaviour
    {
        //以下Image和Text在编辑器中拖拽赋值
        public Text myText;
        public Image myImg1;

        //以下为TinaX服务接口，由TinaX XCore进行依赖注入
        [Inject]
        public IVFS VFS { get; set; } //TinaX.VFS（资产管理系统）服务接口

        private IAsset m_Asset_Img1;    //资产接口，代表一个加载出的资产，来自TinaX.VFS

        private IEventTicket m_Event_OnPlayerEntry; //事件监听注册的凭证
        private IEventTicket m_Event_OnPlayerExit;

        private void Awake()
        {
            XCore.GetMainInstance().InjectObject(this); //对自己进行依赖注入
            //由于继承自MonoBehaviour的类是由Unity实例化出来的，所以需要手动让XCore对自己进行依赖注入
        }

        private async void Start()
        {
            //加载和使用资源
            m_Asset_Img1 = await VFS.LoadAssetAsync<Sprite>("Assets/xxx/xxx.png");
            myImg1.sprite = m_Asset_Img1.Get<Sprite>();

            //注册事件
            m_Event_OnPlayerEntry = XEvent.Register("OnPlayerEntry", playerName =>
            {
                myText.text = (string)playerName;
            });

            m_Event_OnPlayerExit = XEvent.Register("OnPlayerExit", _ =>
            {
                myText.text = "meow";
            });
        }

        private void OnDestroy()
        {
            m_Asset_Img1?.Release(); //释放资源
            m_Event_OnPlayerEntry?.Unregister(); //注销注册事件
            m_Event_OnPlayerExit?.Unregister(); //注销注册事件
        }
    }
}

```

该段代码在`Start()`中注册了事件并加载了资产，为了在组件被销毁的时候释放这些注册和加载，我们则需要在类中声明出私有变量来存储资产加载的结果，和事件注册的凭证，最后在OnDestory中使用它们，完成释放。

而在实际的开发过程中，类似的东西可能更多，这样写起来就有了很多重复繁琐的地方。

通过`DisposableGroup`，我们可以尽可能的简化它们，简化后如下：

``` csharp
using TinaX;
using TinaX.Utils;
using TinaX.VFSKit;
using UnityEngine;
using UnityEngine.UI;

namespace Nekonya.Demo
{
    class Meow : MonoBehaviour
    {
        //以下Image和Text在编辑器中拖拽赋值
        public Text myText;
        public Image myImg1;

        //以下为TinaX服务接口，由TinaX XCore进行依赖注入
        [Inject]
        public IVFS VFS { get; set; } //TinaX.VFS（资产管理系统）服务接口

        private DisposableGroup m_DisposableGroup = new DisposableGroup();

        private void Awake()
        {
            XCore.GetMainInstance().InjectObject(this); //对自己进行依赖注入
            //由于继承自MonoBehaviour的类是由Unity实例化出来的，所以需要手动让XCore对自己进行依赖注入
        }

        private async void Start()
        {
            //加载和使用资源
            var imgAsset = await VFS.LoadAssetAsync<Sprite>("Assets/xxx/xxx.png");
            m_DisposableGroup.Register(imgAsset); //将实现了System.IDisposable的对象注册给m_DisposableGroup
            myImg1.sprite = imgAsset.Get<Sprite>();

            //注册事件
            m_DisposableGroup.RegisterEvent("OnPlayerEntry", playerName =>  //XEvent事件系统 针对 DisposableGroup 提供了扩展方法
            {
                myText.text = (string)playerName;
            });

            m_DisposableGroup.RegisterEvent("OnPlayerExit", _ =>
            {
                myText.text = "meow";
            });
        }

        private void OnDestroy()
        {
            m_DisposableGroup.Dispose();    //一句代码释放所有
        }
    }
}
```
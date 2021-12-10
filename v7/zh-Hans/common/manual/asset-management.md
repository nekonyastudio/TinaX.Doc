# TinaX的资产管理

TinaX框架涵盖了很多各式各样的功能模块，而很多模块有个绕不开的功能，就是加载资产。比如UI模块可能要加载UI视图prefab，Lua模块要加载Lua代码，音频模块需要加载AudioClip等等。

当然，我们不可能在每个模块中都写一个资产加载和管理的功能，毕竟Unity AssetBundle一套体系还是挺复杂的，于是理所当然的，就应该有一个专门的模块来实现资产加载和管理功能，然后其他所有需要加载资产的模块都去找这个专门负责资产的模块来加载资产。

也自然，TinaX中也确实有这样一个专门负责资产管理和加载的模块，叫[TinaX.VFS](/zh-Hans/vfs/README). 可这样一来，就意味着，需要加载资产的功能模块，就必须依赖`TinaX.VFS`这个模块，这就不太符合TinaX设计上“任何一个模块都是可选的，可替代的”这个思想了。

<br>

## 资产服务接口 :id=IAssetService

于是我们在[TinaX.Core](zh-Hans/core/README)模块中设计了一个接口`IAssetService`（命名空间`TinaX.Services`）, 而我们任意一个模块，只需要拿到这个接口，就可以进行资产加载操作了，例如:

``` csharp
using TinaX;
using TinaX.Services;
using UnityEngine;

var assets = XCore.MainInstance.Services.Get<IAssetService>();  //获取资产服务接口
var myText = assets.Load<TextAsset>("asset load path");         //加载资产
Debug.Log(myText.text);                                         //使用资产
assets.Release(myText);                                         //释放使用完毕的资产
```

`TinaX.Core`模块中仅仅包含这个接口的定义，而没有具体的实现。

<br>

## 对框架使用者而言

对于使用框架的开发者而言，我们并不需要关心`IAssetService`内部到底是怎么实现的，我们需要知道：
1. 有`IAssetService`这么个接口的存在
2. 在我们的项目中，**需要有一个功能模块实现了这个接口**

而TinaX框架自己的资产管理模块[TinaX.VFS](/zh-Hans/vfs/README)就实现了这个接口，也就是说，直接使用它就不用操心了。

而知道有这么个接口的存在，显然我们也就知道了，可以通过实现这个接口，从而使用任意的资产管理框架（如`Addressables`）替代框架自己的`TinaX.VFS`.

?> `IAssetService`的设计目的是解决功能模块之间的解耦合问题，因此它约定的接口功能是非常有限的。在通常的业务开发中，不建议直接使用`IAssetService`接口。

<br>

## 对于深入开发者而言

如果我们不想在项目中使用TinaX自己的资产管理模块，而是想使用`Addressables`，或是其他第三方资产框架，或者是自己家有现成资产框架，其实也简单的。

第一步就是编写一个类继承自接口`IAssetService`, 然后将其注册到[服务容器](/zh-Hans/core/manual/dependency_injection)就完事了。

举例一个基本的实现如下：

``` csharp
using System;
using System.Threading;
using Cysharp.Threading.Tasks;
using TinaX.Exceptions;
using TinaX.Services;
using UnityEngine;
using UniRx;
#if UNITY_EDITOR
using UnityEditor;
#endif

namespace Nekonya
{
    public class SimpleAssetLoader : IAssetService
    {
        public string ImplementerName => "My Simple Asset Loader";

        public T Load<T>(string assetPath) where T : UnityEngine.Object
        {
#if UNITY_EDITOR
            return AssetDatabase.LoadAssetAtPath<T>(assetPath);
#else
            return default;
#endif
        }

        public UnityEngine.Object Load(string assetPath, Type type)
        {
#if UNITY_EDITOR
            return AssetDatabase.LoadAssetAtPath(assetPath,type);
#else
            return default;
#endif
        }

        public async UniTask<T> LoadAsync<T>(string assetPath, CancellationToken cancellationToken = default) where T : UnityEngine.Object
        {
#if UNITY_EDITOR
            await UniTask.NextFrame(cancellationToken);
            await UniTask.SwitchToMainThread(cancellationToken);
            var asset = AssetDatabase.LoadAssetAtPath<T>(assetPath);
            if (asset == null)
                throw new ApplicationException($"加载资产失败:{assetPath}");
            return asset;
#else
            await UniTask.NextFrame(cancellationToken);
            return default;
#endif
        }

        public async UniTask<UnityEngine.Object> LoadAsync(string assetPath, Type type, CancellationToken cancellationToken = default)
        {
#if UNITY_EDITOR
            await UniTask.NextFrame(cancellationToken);
            await UniTask.SwitchToMainThread(cancellationToken);
            return AssetDatabase.LoadAssetAtPath(assetPath, type);
#else
            await UniTask.NextFrame(cancellationToken);
            return default;
#endif
        }

        public void LoadAsync(string assetPath, Type type, Action<UnityEngine.Object, XException> callback)
        {
#if UNITY_EDITOR
            Observable.NextFrame()
                .SubscribeOnMainThread()
                .Subscribe(_ =>
                {
                    callback?.Invoke(AssetDatabase.LoadAssetAtPath(assetPath, type), null);
                });
#else
            Observable.NextFrame()
                .SubscribeOnMainThread()
                .Subscribe(_ =>
                {
                    callback?.Invoke(null, new XException("功能尚未实现"));
                });
#endif
        }

        public void Release(UnityEngine.Object asset)
        {
            Resources.UnloadAsset(asset);
        }
    }
}
```

（显然，它非常简陋，以至于只能在编辑器下正常运行。毕竟它只是演示怎么实现接口，而不是介绍怎么编写一个资产管理框架）

然后将其注册到服务容器：

``` csharp
using TinaX;
using TinaX.Services;

var assets = XCore.MainInstance.Services.Singleton<IAssetService, SimpleAssetLoader>();
```

就完成了。
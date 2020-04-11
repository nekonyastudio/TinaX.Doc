# 内置资源服务接口

TinaX中大量的服务包都需要加载资源，如Lua服务需要加载lua文件，UI服务需要加载UI prefab文件，ILRuntime服务需要加载DLL文件……

然而，不可能所有的服务包都各自去实现一遍资源管理和加载的功能的，于是，所有的服务都约定使用TinaX.Core包中的内置服务接口 - 资源接口 来加载和卸载各自所需要的资源。



内置资源接口类`IAssetService`位于包`TinaX.Core`， 命名空间`TinaX.Services`，接口内容如下：

``` csharp
T Load<T>(string assetPath) where T : UnityEngine.Object; //同步加载，泛型

UnityEngine.Object Load(string assetPath, Type type); //同步加载，非泛型

Task<T> LoadAsync<T>(string assetPath) where T : UnityEngine.Object; //异步加载，Task方式

void LoadAsync(string assetPath, Type type, Action<UnityEngine.Object, XException> callback); //异步加载，回调方式

void Release(UnityEngine.Object asset); //释放已加载的资源
```

<br>

## 使用接口

在我们启动框架后，我们可以在`IXCore`接口中获取内置接口:

``` csharp
XCore.GetMainInstance() //获取到IXCore
    .TryGetBuiltinService<IAssetService>(out var assets); //获取内置服务接口
```

**没有非泛型接口？**

内置服务接口推荐仅用于服务包需要解耦合的场景，而非用于业务开发。业务中如需加载资源，请使用资源管理服务直接提供的接口，如`TinaX.VFS`包的`IVFS`接口。


<br>

## 内置资源服务接口 与 VFS

VFS是TinaX的资源管理服务，用于在运行时动态管理、加载、卸载资源。那它与内置资源服务接口之间到底是怎样的关系呢？

#### 内置资源服务接口是一个单纯的接口

在TinaX.Core中约定了一些内置服务接口（包括文本所述的资源服务接口），这些接口只有单纯的约定，并没有具体的实现。

#### VFS实现了内置资源服务接口

VFS在服务启动时会向XCore声明自己实现了`IAssetService`（内置资源服务接口），这样一来，我们就可以在其他地方获取内置资源服务接口来加载和卸载资源。这时候本质上还是VFS在处理资源的操作。

#### 为什么需要约定内置接口？

为了解耦合。TinaX中几乎所有的服务包都需要加载资源：Lua服务需要加载lua脚本，UI服务需要加载UI prefab，I18N服务需要加载语言包字典。

如果没有约定内置接口，就意味着，几乎所有的包都必须直接声明依赖VFS包。这有违TinaX服务包的设计原则：TinaX将每个功能单独拆分成包，目的是尽可能让每个包可以被替代、可以单独使用。

#### 也就是所，不仅仅是VFS

实际上，如果你不喜欢VFS的话，你几乎可以使用任何一个资源管理框架来加入TinaX，为其他服务包和业务提供资源加载和卸载服务。比如Unity的`Addressables`、比如`XAsset`框架等。

我们只需要在它们的基础上封装一层服务提供者，封装实现内置资源服务接口并注册给XCore. 

<br>


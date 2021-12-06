# XCore

XCore 是 TinaX Framework的核心类，它负责启动和管理整个框架的所有服务功能。

## 启动框架

我们可以通过新建一个XCore实例来启动整个框架：

``` csharp
using TinaX; //XCore来自该命名空间
var core = XCore.New();

await core.RunAsync();

//或使用回调方式：
core.RunAsync(err=> { 
    if(err != null)
        Debug.LogError(err.Message);
    else
        Debug.Log("启动完成");
});
```

<br>

## 服务提供者

XCore提供了一个全局的依赖注入容器，但其本身并不包含任何对实际开发有用的功能。具体的功能由TinaX各个功能包的`服务提供者`提供.

我们可以在**框架启动之前**，添加服务提供者：

``` csharp
using TinaX;        //XCore来自该命名空间
using TinaX.UIKit;  //UIKitProvider 来自该命名空间
using TinaX.VFSKit; //VFSProvider 来自该命名空间

var core = XCore.New()
    .RegisterServiceProvider(new VFSProvider()) //注册VFS 服务提供者， 该提供者来自包"TinaX.VFS"
    .RegisterServiceProvider(new UIKitProvider()); //注册UI Kit 服务提供者，该提供者来自包"TinaX.UIKit"

await core.RunAsync();
```

<br>

## 服务的启动次序

服务的启动次序，与服务提供者被注册的先后顺序并无关系。服务的启动次序由各个服务的开发者在开发该服务时决定，不需要使用该服务的业务开发者过多关心其启动次序。

## XCore单例

当创建了XCore的对象之后，我们可以通过以下方式获取到它的全局单例：

``` csharp
XCore.GetMainInstance();
//或
XCore.MainInstance;
```

<br>

## 使用服务

### 服务接口

每个服务包含一个或多个服务接口，由`服务提供者`内部向XCore进行注册。比如 UI Kit服务会向XCore注册`TinaX.UIKit.IUIKit`服务接口。

业务开发者可以通过各个服务所提供的服务接口来使用相关的功能。

### 获取服务接口

XCore提供了一系列获取服务接口的方法，我们可以从XCore获取到服务接口：

``` csharp
using TinaX;
using TinaX.VFSKit;

var assets = XCore.GetMainInstance().GetService<IVFS>(); //获取服务接口
var myImg = assets.LoadAsset<Sprite>("Assets/Img/hello.png"); //使用服务接口提供的具体功能
```

<br>

## Facade

如果每次使用服务都得从XCore获取的话，可能会有点麻烦，`Facade`是一种简化用法。

有的服务可能会提供
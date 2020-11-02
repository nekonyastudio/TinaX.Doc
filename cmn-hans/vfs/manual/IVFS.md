# 通过VFS管理资产


VFS基于Unity的`AssetBundle`机制，与现有的Unity `Addressables` 是同类型产品。

接下来，我们通过`IVFS`服务接口来进行Unity资产操作，我们可以通过[依赖注入](/cmn-hans/core/manual/DependencyInjection.md)的方式获得该接口，或使用如下代码手动获取：

``` csharp
using TinaX;
using TinaX.VFSKit;

IVFS vfs = XCore.MainInstance.Services.Get<IVFS>();
//或
IVFS vfs2 = VFS.That; //使用服务接口Facade
```

## 寻址加载

寻址加载指通过Unity引擎中`Assets/xxx/xxx.xxx`这类地址加载资产，这是VFS的基本功能之一。 例如：

``` csharp
Sprite myImg = vfs.Load<Sprite>("Assets/Images/img1.png");
```

在上述代码中，我们使用同步加载的方式加载了图片"img1.png". 同步加载会堵塞我们的主线程，在加载较大的文件时，会明显感觉程序卡住了，所以更多时候，我们还可以使用异步加载：

``` csharp
Sprite myImg = await vfs.LoadAsync<Sprite>("Assets/Images/img1.png");
```

异步加载文件的时候，加载操作会放在后台线程执行。除了多线程这一点之外，异步加载还可以直接从网络中加载这个资产，这点我们在后文展开介绍。

上述代码使用了C# 5.0以上版本的`async/await`的异步模型来执行异步任务，除此之外，我们还可以使用对于Unity程序员更熟悉的回调（callback）方式来异步加载资产：

``` csharp
vfs.LoadAsync<Sprite>("Assets/Images/img1.png", (mtImg, err) =>
{
    //如果加载过程中出现异常(Exception)，则err不为null.
});

//或不使用泛型的写法
vfs.LoadAsync("Assets/Images/img1.png", typeof(Sprite), (mtImg, err) => { });
```

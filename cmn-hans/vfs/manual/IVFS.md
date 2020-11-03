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

### 虚拟寻址

有一个概念我们需要介绍，在上文中，我们所使用的路径`Assets/Images/img1.png`，它可能并不是真实的。

我们知道，在编辑器阶段，Unity的所有资源都是确实放在`Assets`目录下的，我们在编辑器下的`UnityEditor.AssetDatabase`也可以使用`Assets/xxx`的路径来加载资源。

但是在打包发布之后，情况就不一样了。我们知道，Unity并不会把`Assets`目录下的资源全部打包，而是只把与参与打包的场景相关联的资源进行打包，这个做法倒是没什么问题，确实应该这么做，但同时也意味着，通常情况下，我们无法在打包后随意的通过代码动态加载自己需要的资产了。

而TinaX的VFS工具，在打包时将**白名单**范围内的资产统一管理了起来，并且在打包发布后，依然可以用`Assets/xxx`的路径来找到我们想要的资产。在这一过程中，`Assets/Images/img1.png`并不像编辑器下真正的表示`Assets`目录下的某个文件（因为压根就不存在`Assets`目录了），而是一个虚拟的寻址路径，由VFS内部在被某种形式管理的一堆资产中找到对应的资产。而这个虚拟的寻址路径，我们通常也称之为`VFS路径`（`VFS Path`）.

对于业务开发者而言，我们只需要通过VFS路径即可加载到我们想要的资产，而不需要关心VFS内部具体是怎么管理这些资产的。实际上在VFS内部，这些资产的管理方式可以是不唯一的，比如我们可以根据需求把某一部分的图片集体打成图集（Atlas）,把某一部分文件集体打成一个整包，把某些文件最细粒度的分别打包等。这些资产的存在位置也是不唯一的，比如它们可以在发布Unity游戏/应用时一起打包发布，也可以在后续通过补丁包来更新之前发布的资产，甚至为了减少游戏包体，这些资产还可以一开始就放在云端，等玩家需要用到的时候再下载……

<br>


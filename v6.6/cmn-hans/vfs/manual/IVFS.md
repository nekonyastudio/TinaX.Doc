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

## 释放已加载资产

在我们加载了资产之后，如果使用完毕，应该适时的释放资产，以免内存中加载的资产越来越多，造成内存泄漏，或是被系统杀进程。

在VFS中，我们可以使用如下方式释放资产：

``` csharp
//加载资产
Sprite myImg = vfs.Load<Sprite>("Assets/Images/img1.png");

//使用资产
//xxxxxxxxxxx

//释放资产
vfs.Release(myImg); //传入加载得到的UnityEngine.Object资产
```

如果我们重复多次加载同一个资源的话，内存中只会保留一份，不会重复加载。在实际开发中，我们需要确保**每一次加载最终都对应着一次释放**，以免造成内存泄漏。



<br>

## 使用IAsset接口

除了上述方式直接加载资产之外，我们还可以加载得到`IAsset`接口，并通过`IAsset`接口操作资产. 把所有加载方法的`Load(xxx)`改成`LoadAsset(xxx)`即可：

``` csharp
IAsset asset_myImg = vfs.LoadAsset<Sprite>("Assets/Images/img1.png");

//通过IAsset接口获取资产
Sprite myImg = asset_myImg.Get<TextAsset>();
//或 Sprite myImg = asset_myImg.Asset as TextAsset;

//通过IAsset接口释放资产
asset_myImg.Release();
```

`IAsset`接口内部实现了`IDisposable`接口，与.NET设计本意不同的是：每次调用`Dispose()`相当于调用一次`Release()`方法，而不会使整个资产立即被gc.

通过`IDisposable`接口，我们可以使用C#的语法糖对`IAsset`的使用进行一定程度的简化：

``` csharp
using(var asset_txt = await vfs.LoadAssetAsync<TextAsset>("Assets/Data/demo.json"))
{
    Debug.Log(asset_txt.Get<TextAsset>().text);
}
```

<br>

## UnloadUnusedAssets

当一个资源被VFS内部彻底卸载之后，该资源相关对象（就比如`IAsset`）还是会被VFS暂时保留。我们可以适时的调用一次`vfs.UnloadUnusedAssets()`方法，来清理这些可能已经不再有用的对象。

?> "我们可以适时的调用一次"指的是例如切换场景、等待读条、关闭某些UI页等，偷懒做法也可以定时（如每分钟执行一次）。<br>可能有的开发者会觉得这个操作有点麻烦，需要手动执行`UnloadUnusedAssets()`方法是综合权衡性能开销的设计，我们觉得每次释放非托管资源之后立即自动执行清理操作的开销成本会大于定期由开发者可控地主动执行。


<br>

## 通过VFS加载场景

对于Unity来说，场景也是一种特殊的资产。通过VFS, 我们也可以将场景以资产的形式打包、热更、加载。

由于场景是一种特殊的资产，对它的操作没有其他资产那么便利，VFS加载到的场景均以`ISceneAsset`接口操作。

``` csharp
//异步加载场景（同步加载类似）
ISceneAsset myScene = await vfs.LoadSceneAsync("Assets/Scenes/Game.Map1.unity");

//加载完成之后，并不会自动切换场景，需要执行：
myScene.OpenScene(LoadSceneMode.Single); //参数是Unity自己的UnityEngine.SceneManagement.LoadSceneMode, 默认值为Single
```

<br>

## 从StreamingAssets加载

!> 请注意`StreamingAssets`本小节讨论的都是加载**文件**而非Unity 资产(Asset).

我们知道Unity有个特殊的目录叫`StreamingAssets`.凡是存放在这个目录下的文件，在打包发布的时候会原封不动的跟着一起打包。

在实际开发中，这个目录随着平台的不同，也会有不同的特性，例如在诸如Windows等平台，`StreamingAssets`目录就直接位于发布文件的根目录下，我们可以直接通过.NET公共库的`System.IO`来加载文件.

而在诸如Android之类的平台，`StreamingAssets`目录被打包在了jar包里，不存在于真实的文件系统上，这时候我们就无法通过常规的IO方式来访问文件。

当然，Unity也提供了自己的统一的加载方式，在旧Unity中是`UnityEngine.WWW`类，新Unity则统一使用`UnityWebRequest`来访问，这两种方式都是通过协程来异步等待的。

在VFS中，我们也提供了两个方法来加载StreamingAssets中的文件：

``` csharp 
byte[] myFile = await LoadFileFromStreamingAssetsAsync("folders/myfile.txt"); //这里的path是StreamingAssets的相对路径

//或者回调方式
LoadFileFromStreamingAssetsAsync("folders/myfile.txt", (myFile, error) =>
{
    if(error != null)
    {
        //xxxxxx
    }
});
```

<br>

## 注意事项

!> 在使用`async/await`方式开发异步代码时，千万不要在主线程使用`xxx.Result`或者`xxx.Wait()`等方法堵塞等待，在部分平台（尤其移动端）有极大概率会随机卡死。<br>几乎所有内部封装了Unity自己的异步代码的Task返回值上都不要这么干！<br>不仅仅是VFS, 其他地方也一样，哪怕出了TinaX框架也一样
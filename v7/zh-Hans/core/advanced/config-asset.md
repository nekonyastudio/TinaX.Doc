# 配置资产深入

## 配置资产的加载

加载配置资产的统一接口位于模块包`TinaX.Core`中, 命名空间`TinaX.Services.ConfigAssets`下的`IConfigAssetService`接口。

例如加载配置资产代码如下：

``` csharp
var service = XCore.MainInstance.Services.Get<IConfigAssetService>(); //获取接口
var conf = service.GetConfig<MyConfig>("MyConf");
```

这会加载资产`Assets/Resources/TinaX/Configurations/MyConf.asset`, 如配置了定义符号则加载`Assets/TinaX/Configurations/MyConf.asset`.

<br>

## 配置资产加载接口内部是如何运作的

首先，我们需要了解TinaX的[资产服务接口](zh-Hans/common/manual/asset-management#IAssetService).

当调用加载方法后，配置资产加载服务内部会执行以下流程：

| 次序 | 步骤名称 | 步骤说明|
|:-----|:-------|:-------|
|`1`|`ReadyLoadFromAssetService`|准备从资产服务接口（`IAssetService`）加载资产|
|`2`|`LoadFromAssetService`|正式从资产服务接口（`IAssetService`）加载资产|
|`3`|`ReadyLoadFromResources`|准备用UnityEngine.Resources类加载资产|
|`4`|`LoadFromResources`|正式用UnityEngine.Resources类加载资产|

当然根据设置会有所差异，比如设置了“No Resources”之后，步骤3和4会被忽略。

<br>

## 修改加载逻辑

继续上述案例，一开始我们调用的`service.GetConfig<MyConfig>("MyConf")`方法传入的loadPath是`MyConf`, 然后在步骤1的时候，根据是否是“No Resources”的推断，得出加载路径，比如说是`Assets/TinaX/Configurations/MyConf.asset`，然后步骤2根据步骤1得出的加载路径，调用`IAssetService`服务接口进行加载。

这时候问题来了，比方说我们项目中实现`IAssetService`接口的不是[TinaX.VFS](zh-Hans/vfs/README),而是自己实现的服务，然后自己实现的服务并不认识`Assets/TinaX/Configurations/MyConf.asset`这个路径，而是要传入`"a://TinaX.Configurations.MyConf.asset"`这个路径（瞎编的，举个例子），怎么办呢。

两个思路，一个是在我们自己实现的`IAssetService`服务那边做兼容，比如
``` csharp
if(loadPath.StartWith("Assets/TinaX/Configurations"))
{
    //xxxxx
}
```

另一个思路就是改变我们的配置资产加载服务的加载逻辑，怎么改呢，其实简单，我们上述的四个步骤是用Pipeline(管道)的思路实现的，我们可以在上述的步骤1和步骤2之间，插入一段自己的代码。

### 编写Handler

上面的每一个步骤，我们称之为Handler，我们编写一个类如下：

``` csharp
using TinaX.Core.ConfigAssets.Pipelines;

public class MyHandler : ILoadConfigAssetHandler
{
    public string HandlerName => "Custom load path";

    public bool LoadConfigAsset(ref LoadConfigAssetPayload payload, ILoadConfigAssetHandler next)
    {
        payload.AssetServiceLoadPath = $"a://TinaX.Configurations.{payload.LoadPath}.asset";    //修改loadPath
        return true; //返回值表示pipeline是否继续往下执行，如果返回false的话，pipeline就会在执行完当前的Handler后中段
    }
}
```

### 插入到Pipeline

然后，用如下代码将其插入到原有的步骤中：

``` csharp
var service = XCore.MainInstance.Services.Get<IConfigAssetService>();
service.LoadConfigAssetPipeline.InsertNext(handler => handler.HandlerName == "ReadyLoadFromAssetService", new MyHandler());
```

然后，我们自己写的Handler就会被插入到`ReadyLoadFromAssetService`后面。

而`ReadyLoadFromAssetService`这个字符串的名称，推荐使用常量`TinaX.Core.ConfigAssets.Pipelines.LoadConfigAssetHandlerNameConsts.ReadyLoadFromAssetService`代替，其他也同理。

<br>

### 异步方法

`IConfigAssetService`中有两个加载方法，一个是同步加载，另一个是异步加载。对应的也有两个Pipeline，上文演示的是对同步方法的Pipeline的修改，根据实际需求也有可能要修改异步方法的Pipeline.

?> 不仅仅可以在现有的基础上修改，实际上我们完全可以在Pipeline的开头插入一个自己的Handler完全覆盖掉后续的流程。
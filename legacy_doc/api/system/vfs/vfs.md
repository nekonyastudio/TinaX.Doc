# 虚拟文件系统

虚拟文件系统(Virtual File System)可实现通过工程目录的路径结构进行资源管理、加载释放，，是TinaX重要的底层系统之一。

!> 这个文档是从早期的内部文档中搬运过来的，有些东西的名字不太一样，正在调整，请先以实物为准。

!> VFS在TinaX很久以前版本是好几个系统组起来的，命名空间很乱，有的叫`Asset`，有的叫`AddrFile`，后续将逐渐统一为`VFS`.


------


TinaX的UI系统

- 命名空间：__TinaX__
- C#入口：__AssetsMgr.Instance__
- Lua入口：__XCore.Asset__


------



## 加载资源

### CSharp

原型：

``` csharp
T LoadAsset<T>(string path) where T : UnityEngine.Object

UnityEngine.Object LoadAsset(string path, System.Type type);
```

参数

| 参数 | 类型 | 说明 | 默认值 |
| ------| ------ |------ | ----|
| path | string | 资源路径 | - |

泛型T: 欲加载的资源类型


案例 泛型

``` csharp
public Image img;
img.sprite = AssetsMgr.Instance.LoadAsset<Sprite>("Assets/Game/Art/UI/1.png");
```

案例

``` csharp
public Image img;
img.sprite = (Sprite)AssetsMgr.Instance.LoadAsset("Assets/Game/Art/UI/1.png",typeof(Sprite));

```

### Lua

原型：

``` lua
XCore.Asset.LoadAsset(path,type)
```

用例

``` lua
img.sprite = XCore.Asset.LoadAsset("Assets/Game/Art/UI/1.png",typeof(CS.UnityEngine.Sprite))
```


------


## 加载资源（异步方式）

### CSharp

原型：

`void LoadAssetAsync<T>(string path, Action<T> callback) where T: UnityEngine.Object`

`void LoadAssetAsync(string path, System.Type type, Action<UnityEngine.Object> callback)`

参数

| 参数 | 类型 | 说明 | 默认值 |
| ------| ------ |------ | ----|
| path | string | 资源路径 | - |
| callback | System.Action| 加载完成的回调 | -|

泛型T: 欲加载的资源类型


案例 泛型

``` csharp
public Image img;

AssetsMgr.Instance.LoadAssetAsync<Sprite>("Assets/Game/Art/UI/1.png",(res)=>{
    img.sprite = res;
});
```

案例

``` csharp
public Image img;

AssetsMgr.Instance.LoadAssetAsync("Assets/Game/Art/UI/1.png",typeof(Sprite),(res)=>{
    img.sprite = res;
});
```

### Lua

原型：

``` lua
XCore.Asset.LoadAssetAsync(path,type,callback)
```

用例

``` lua
XCore.Asset.LoadAssetAsync("Assets/Game/Art/UI/1.png",typeof(CS.UnityEngine.Sprite),function(res)
    img.sprite = res
end)
```

------

# 释放资源

和很多基于AssetBundle的资源方案一样，TinaX的框架底层会对资源引用进行计数；

TinaX内部在加载资源时，会自动向计数器发送资源卸载的事件，比如[xImage](system_uikit_uicomponent_ximage)或者[UIKit](system_uikit_advanced_mode)之类的，
但是，如果开发者自己手动加载资源的话，就得在用完了之后，手动调用一下卸载方法咯。


方法原型：

C# 

``` csharp
void RemoveUse(string path);`
```

Lua: 

``` lua
XCore.Asset.RemoveUse(path)
```

传入的参数就是加载时候的那个路径。


然后，还有个方法，用来手动释放**引用计数器中，引用数量为0的资源**

方法原型：

C# 

``` csharp
void GC();
``` 

Lua: 

``` lua
XCore.Asset.GC(path)
```

目前框架层还不会自动调用GC, 因为在不少能参考到的框架中，这种GC是定时的，比如30秒一次，或者在切场景什么的时候触发一次。总觉得这么搞不是很只能，打算再琢磨琢磨，就先没写。
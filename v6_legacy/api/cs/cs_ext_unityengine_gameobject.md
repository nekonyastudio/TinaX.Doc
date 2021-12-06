# C#方法扩展 ： UnityEngine.GameObject

TinaX中对UnityEngine类进行了一些常用的扩展。


- 命名空间： `TinaX`

## IsNull 是否为空

用例：

``` csharp
var b = gameObject.IsNull();
```

?> 我们在C#中，可以使用 `gameObject == null` 来判断gameobject**是否被销毁**（Destroy），这是因为Unity重载了`==`运算符，实际上这时候gameObject对象本身是存在的。这就意味着，如果你在lua中同样用`gameObject == nil` 来判断gameobject是否被销毁，始终会得到`true`，即使这个gameObject已经被Destroy了。所以在lua中，我们应该使用`gameObject:IsNull()` 来判断

## DestroySelf 销毁自身


用例：

``` csharp
GameObject.Find("cube").DestroySelf();
GameObject.Find("cube").DestroySelf(1.0f); //delay time
```


## DestroyNow 立即销毁自身

相当于`GameObject.DestroyImmediate();` 

用例：

``` csharp
GameObject.Find("cube").DestroyNow();
```



## Show 显示

相当于`SetActive(true);` 

- 返回值：`GameObject` 返回对象自身


用例：

``` csharp
var go = GameObject.Find("cube");
go.SetActive(false);
go.Show();
```


## Hide 隐藏

相当于`SetActive(false);` 

- 返回值：`GameObject` 返回对象自身


用例：

``` csharp
var go = GameObject.Find("cube");
go.Hide();
go.Show();
```





## Name 改名

相当于`gameObject.name = "xxx";` 

- 返回值：`GameObject` 返回对象自身


用例：

``` csharp
var go = GameObject.Find("cube").Name("box");
```






## DontDestroy 载入新场景时不要销毁

相当于`GameObject.DontDestroyOnLoad();` 

- 返回值：`GameObject` 返回对象自身


用例：

``` csharp
var go = GameObject.Find("cube").DontDestroy();
```




## GetComponentOrAdd 获取组件，如果不存在则新建

- 返回值：`T`  获取或创建的Component对象


用例：

``` csharp
var image = gameObject.GetComponentOrAdd<Image>();
var text = gameObject.GetComponentOrAdd(typeof(Text));
```



## RemoveComponentIfExists 如果指定组件存在，则移除它

- 返回值：`GameObject`  返回对象自身


用例：

``` csharp
gameObject.RemoveComponentIfExists<Image>();
gameObject.RemoveComponentIfExists(typeof(Image));
gameObject.RemoveComponentIfExists("Image");
```




## RemoveComponentsIfExists 如果指定组件存在，则移除它

> 与上一个命令的区别：如果GameObject上有多个相同的Component，前者只会移除一个，本命令会全部移除。

- 返回值：`GameObject`  返回对象自身


用例：

``` csharp
gameObject.RemoveComponentsIfExists<Image>();
gameObject.RemoveComponentsIfExists(typeof(Image));
```

## HasComponent 组件是否存在

-   返回值：`bool` 如果存在，返回true

用例：

```csharp
var b = gameObject.HasComponent<Image>();
var b = gameObject.HasComponent(typeof(Image));
var b = gameObject.HasComponent("Image");
```



## SetLayerRecursive 设置Layer(递归)

> 将GameObject与其子GameObject全部设置为指定Layer。

- 返回值：`GameObject`  返回对象自身


用例：

``` csharp
gameObject.SetLayerRecursive(5);
```




## FindOrCreateGameObject 获取子GameObject，如果不存在则新建



- 返回值：`GameObject`  寻找到或新建的指定名称的子GameObject


用例：

``` csharp
var sub_Go = gameObject.FindOrCreateGameObject("sub");
var sub_Go = gameObject.FindOrCreateGameObject("sub",typeof(Canvas)); //params System.Type[]
```

## CreateGameObject 为当前GameObject创建子级GameObject

-   返回值：`GameObject` 新建的子级GameObject对象

用例：

``` csharp
var sub_go = gameObject.CreateGameObject("sub");
var sub_go = gameObject.CreateGameObject("sub",typeof(Canvas)); //params System.Type[]
```



## SetParent 指定父级GameObject



用例：

``` csharp
gameObject.SetParent(GameObject.Find("Camera"));
```



## 设置常用的Transform属性

用例：

``` csharp
gameObject.SetPosition(Vector3.zero);
gameObject.SetLocalPosition(Vector3.zero);
gameObject.SetLocalScale(Vector3.one);
//......
```

## 





------

### 链式编程

以上大部分返回自身的命令可以被用于链式编程，如下：

``` csharp
    var go = GameObject.Find("Cube")
        .FindOrCreateGo("sub_cube")
        .SetLayerRecursive(6)
        .DontDestroy()
        .Hide();

```

------



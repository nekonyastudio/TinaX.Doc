# 开始使用UIKit

?> 在开始之前，请阅读[TinaX.UIKit基本概念](/zh-Hans/uikit/manual/basic)。


## 添加模块

安装UIKit包之后，我们需要调用`.AddUIKit()`方法将其注册到框架的[XCore](/zh-Hans/core/manual/xcore)中。

``` csharp
var core = XCore.CreateDefault()
    .AddUIKit(); //添加TinaX.UIKit模块功能到框架中
await core.RunAsync();
```

不过通常情况下这还不够，因为UIKit是一个不包含具体UI系统实现的抽象框架，我们需要配合诸如[TinaX.UIKit.UGUI](/zh-Hans/uikit/ugui/README)等模块使用，则注册代码类似这样：

``` csharp
var core = XCore.CreateDefault()
    .AddUIKit(builder => //设置UIKit功能
    {
        builder.AddUGUI(); //给UIKit添加uGUI支持（需要安装TinaX.UIKit.UGUI包）
    });
await core.RunAsync();
```
?> 通常，TinaX框架所有模块往XCore添加功能时的"AddXXX()"方法都位于`TinaX.Services`命名空间中。

## 服务接口

TinaX.UIKit包对外提供的服务接口是`TinaX.UIKit.IUIKit`, 我们可以从服务容器中获取该接口：

``` csharp
using TinaX;
using TinaX.UIKit;

var uikit = core.Services.Get<IUIKit>();
```

<br>

!> 如基本概念所描述，UIKit包是一个不包含具体UI实现的抽象业务包，因此以下描述的代码操作是未经简化的，主要用于展示框架的工作原理。<br>对于开发者而言，通常在业务代码中使用具体`UIKit提供者`所提供的服务接口和API完成UI操作。

## 打开UI页

如[基本概念](/zh-Hans/uikit/manual/basic#open-ui-page)中所描述的，打开UI页实际上包括了获取UI页和推入UI Group两个步骤。

``` csharp
var page = await uikit.GetUIPageAsync("page uri"); //获取UI Page （page uri将在下文讨论）
var group = xxx; //Group从哪儿获取在下文讨论说明
group.Push(page); //将page推入group，完成显示。
```

虽然我们说UIKit是一个不包含具体实现的抽象框架，但当我们项目中添加了具体的实现之后，依然可以只使用UIKit的`IUIKit`接口完成大多数操作。但涉及到具体特有功能（如让uGUI显示背景遮罩等），就需要使用对应UIKit提供者所提供的服务接口了。

## Page控制器

Page控制器是我们编写的UI逻辑代码，我们新建一个class并继承自`TinaX.UIKit.Page.Controller.PageControllerBase`.

``` csharp
using TinaX.UIKit.Page.Controller;
using UnityEngine;

public class HelloScreen : PageControllerBase
{
    void Awake()
    {
        Debug.Log("Hello, World");
        //UIKit本身没有关于View的具体实现，因此只能搞些输出Hello world的操作.
    }
}
```

之后在获取Page时传入控制器参数, 以使它与Page相关联。

``` csharp
var page = await uikit.GetUIPageAsync("page uri", new HelloScreen());
```

`PageControllerBase`是一个抽象类，不继承MonoBehaviour，上文的`void Awake()`实现也与MonoBehaviour无关。

## 导航器

我们可以在Page Controller中使用导航器来打开其他UI页.

``` csharp
void Awake()
{
    await Navigation.OpenUIAsync("page uri", new OnePageController());
}
```

注意这里打开UI使用的方法直接是`OpenUIAsync`而不是`GetUIPageAsync`，这是因为导航器具有上下文关系，它会自己把新的UI页推给与当前UI同级的UI Group中，而不需要如前文一样手动获取group再推入, 也因此，`OpenUIAsync`的传递参数会与`GetUIPageAsync`有所不同。

## 构建器

上文我们演示代码中，OpenUI包括了"Page Uri"和"Page Controller"两个参数，而实际业务中，这些参数可能会非常多，比如是否使用UI背景遮罩、是否使用UI动画、是否给UI传递消息参数、自定义反射逻辑等等，于是这会导致调用`OpenUIAsync`方法从传入的参数非常多而混乱。

于是理所当然的，我们设计了一个方法的重写，把所有参数都放在一个class中传入

``` csharp
OpenUIAsync(OpenUIArgs args, CancellationToken cancellationToken = default);
```

这样调用起来，代码就类似下文这样：

``` csharp
void Awake()
{
    await Navigation.OpenUIAsync(new OpenUIArgs("page uri"){ PageController = new OnePageController() });
}
```

我们在TinaX 6.x版本中确实也使用了这样的设计，但业务代码中出现这些代码也确实不够美观优雅。因此现在我们可以使用构建器来简化调用：

``` csharp
void Awake()
{
    await Navigation.CreateOpenUI("page uri") //创建构建器
        .SetController<OnePageController>() //加入各种参数
        .OpenUIAsync(); //执行OpenUI
}
```
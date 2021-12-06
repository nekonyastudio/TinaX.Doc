# TinaX中的依赖注入


依赖关系注入（DI, Dependency injection）是一种软件设计模式，这是一种在类及其依赖关系之间实现[控制反转](https://docs.microsoft.com/zh-cn/dotnet/architecture/modern-web-apps-azure/architectural-principles#dependency-inversion)的技术。依赖注入是TinaX的核心思想之一。

关于依赖注入的概念、实践和理解，可参考链接：[知乎：如何用最简单的方式解释依赖注入？依赖注入是如何实现解耦的?](https://www.zhihu.com/question/32108444)

<br>

<br>


## IXCore - 注册服务

`IXCore` 是 TinaX框架的核心，我们可以通过`IXCore`接口使用如`IXCore.BindService<TService, TConcrete>()`等方法进行简单的服务注册和获取服务实例的功能。

但详细完整的依赖注入容器服务，请通过`IServiceContainer`接口访问。

## IServiceContainer - 注册服务

我们可以通过`IXCore`接口的`IXCore.Services`属性访问到`IServiceContainer`接口. （`IServiceContainer`接口位于命名空间`TinaX.Container`）

### 注册（绑定）服务

我们可以使用`IBindData Bind<TService, TConcrete>()`方法来绑定服务，例如：

``` csharp
public interface IMeow
{
    void SayMeow();
}
public class Meow : IMeow
{
    public void SayMeow() { }
}

IXCore core = XCore.GetMainInstance();
core.Services.Bind<IMeow, Meow>();
```

在之后，我们可以通过如下方式获取到依赖注入对象：

``` csharp
IXCore core = XCore.GetMainInstance();
var meow = core.Services.Get<IMeow>();
meow.SayMeow();
```

通过`Bind<TService, TConcrete>()`方法绑定注册的服务，在每次获取时，都会生成一个全新的对象。而有些场景中，我们希望在多出使用同一个对象的话，我们可以注册一个单例服务。

### 注册单例服务

我们可以使用`IBindData Singleton<TService, TConcrete>()`方法来注册一个单例服务。注册方式与`Bind`方式一致：

``` csharp
IXCore core = XCore.GetMainInstance();
core.Services.Singleton<IMeow, Meow>();
```

之后，我们每次获取`IMeow`时，得到的都是同一个对象。


### 注册一个已实例化的对象

在前两种方式中，我们只注册了服务的接口和类名，具体类的实例化操作，是由TinaX框架内部进行的。而在有些特殊场景下，我们希望把一个已经实例化的对象注册进服务容器，我们可以使用`object Instance<TService>(object instance)`方法，例如：

``` csharp
var meow = new Meow();

IXCore core = XCore.GetMainInstance();
core.Services.Instance<IMeow>(meow);
```

**被注册的对象是以单例模式存在的**，即每次获取`IMeow`时，都会得到我们注册进去的同一个对象。

<br>

------

## 被注册的服务类

比如说有个类 `MeowA`

``` csharp
public interface IMeowA
{
    string SayMeow();
}

public class MeowA : IMeowA
{
    public string SayMeow()
    {
        Debug.Log("Meow");
    }
}
```

我们事先把类`MeowA`注册进服务容器：

``` csharp
IXCore core = XCore.GetMainInstance();
core.Services.Bind<IMeow, Meow>();
```

接下来我们再写一个类`MeowB`, 注意，这里的`MeowB`也是要注册进服务容器的，并且通过服务容器获取到`MeowB`.

或者说，`MeowB`也是被依赖注入系统所管理的类, 那么，在`MeowB`类中，我们就可以使用更简单的方式来获取到`MeowA`.

### 通过构造函数注入

``` csharp
public class MeowB
{
    private readonly IMeowA _MeowA;

    public MeowB(IMeowA meowA)
    {
        //通过构造函数获取到服务容器中的其他服务
        _MeowA = meowA;
    }
}
```
当我们从依赖注入服务中获取`MeowB`的对象时，依赖注入服务会自动对构造函数中的参数进行赋值。

> 注意，只有构造函数为公开(`public`)时才能够被注入。

<br>

### 通过属性选择器或变量注入

服务容器采用`特性标记(Attribute)`的方法来标识那些属性或变量是允许被注入的。

``` csharp
using TinaX;

public class MeowB
{
    [Inject] //依赖注入标记
    public IMeowA _MeowA {get;set;}
}
```

任何可以被注入的属性选择器或变量比如满足以下规则：
- 变量或属性选择器必须标记`[Inject]`特性
- 属性选择器`set;`的访问级别必须为`public`.
- 变量的访问级别比如为`public`

!> 注意，不可以在`构造函数`中访问（调用）被标记为注入的`属性选择器`或`变量`的实例，因为属性选择器或变量的注入流程会在构造函数之后进行，如果您在构造函数中访问了标记为注入的属性选择器，这将会导致一个`NullReferenceException`异常。

<br>

------
### 对已存在的对象进行注入

对于一个已存在的对象，如下

``` csharp
using TinaX;

public class MeowB
{
    [Inject] //依赖注入标记
    public IMeowA _MeowA {get;set;}
}

var mB = new MeowB(); //已经实例化的对象
```

当对象被实例化的时候，对象中的`_MeowA`理所当然是空的。
这时候，我们也可以对它进行依赖注入处理。

``` csharp
var mB = new MeowB(); //已经实例化的对象

IXCore core = XCore.GetMainInstance();
core.Services.Inject(mB); //对已实例化的对象进行依赖注入处理
```

执行操作之后，`mB`中的`_MeowA`既被赋值。


<br>

------

<br>

## CatLib

TinaX的依赖注入功能，底层实现采用了第三方库[CatLib](https://catlib.io/).

在TinaX`IServiceContainer`接口中提供了属性`CatApplication`可以直接访问到`CatLib.Application`实例。通过`CatLib.Application`实例，我们可以使用更多TinaX本身尚未提供的高级功能。

### ILRuntime 开发者

但是对于使用`ILRuntime`的开发者需要注意的是：依赖注入是一个大量依赖反射的功能。TinaX自身所有依赖注入方法，在ILRuntime中使用时，都是经过CLR重定向处理的，原生`CatLib.Application`配合`ILRuntime`直接使用会出现不可预知的问题，需要开发者执行编写CLR重定向方法。
# TinaX中的依赖关系注入

TinaX支持依赖关系注入（DI）软件设计模式，这是一种在类及其依赖项之间实现控制反转 (IoC) 的技术。

TinaX中的依赖注入功能由XCore提供，几乎所有的框架模块都依赖依赖注入功能实现, 它是TinaX中一个重要概念。

<br>

## 依赖关系

假设有如下两个类：

``` csharp
public class Driver
{
    public SportsCar _MyCar;

    public void Driver(SportsCar car)
    {
        this._MyCar = car;
    }

    public void GoHome()
    {
        _MyCar.StartEngine();
        _MyCar.Move();
    }
}
```
``` csharp
public class SportsCar
{
    public void StartEngine() { /* */}

    public void Move() { /* */}
}
```

其中，`Driver`类直接依赖于`SportsCar`类，硬编码的依赖性会产生一些问题，应该避免使用，原因如下：
- 如果我们要把`SportsCar`替换成`Truck`，则必须也得修改`Driver`
- 因为`Driver`有一个依赖性，则使用到`Driver`类的地方必须为其设置一个依赖性。在一个大型项目中可能会有很多地方使用`Driver`，这将导致配置`Driver`依赖性的代码混乱的出现在项目各处。
- 很难对这种代码进行单元测试，如果我们需要对`Driver`进行单元测试，最简单的方法应该是模拟一个`Car`,而不是给它一个真正的Car，但上述代码必须给`Driver`一个`SportsCar`才行。

于是，人们提出了依赖关系注入（Dependency Injection）这个概念，它通过以下方式尝试解决问题：
- 使用`interface`（接口）或基类将依赖关系进行抽象化，比如给`SportsCar`和`Truck`定义一个接口`ICar`
- 提出一个`服务容器`的概念来存储服务（Service），上述的`Driver`和`SportsCar`都可以认为是一个服务。
- 由服务容器等底层框架处理服务之间的依赖关系

我们修改上述代码如下：

``` csharp
public class Driver
{
    public ICar _MyCar;

    public void Driver(ICar car)
    {
        this._MyCar = car;
    }

    public void GoHome()
    {
        _MyCar.StartEngine();
        _MyCar.Move();
    }
}
```
``` csharp
public interface ICar
{
    void StartEngine();
    void Move()
}

public class SportsCar : ICar
{
    public void StartEngine() { /* */}

    public void Move() { /* */}
}
```

<br>

## 服务容器

TinaX中的服务容器接口为`TinaX.Container.IServiceContainer`, 我们可以在`IXCore`中获得到服务容器：
``` csharp
using TinaX;
using TinaX.Container;

IServiceContainer services  = XCore.MainInstance.Services; //获取XCore中的服务容器
```
<br>

## 注册服务

我们可以向服务容器注册上述示例中的服务：

``` csharp
services.Bind<ICar, SportsCar>();
services.Bind<Driver>();
```

<br>

## 获取服务

我们可以通过服务容器来获取服务：

``` csharp
var driver1 = services.Get<Driver>();
```

此时请留意，`Driver`类的构造函数中存在一个`ICar`类型的参数，这就是一种依赖关系的表达。在我们通过服务容器获取到`Driver`类的实例时，服务容器在背后自动分析了`Driver`类的依赖，并把`SportsCar`注入了进去。
这种依赖关系的注入方式又叫做“构造函数注入”。

<br>

## 服务生存期

现在，我们每次通过服务容器获取到的`Driver`对象都是不同的新实例。如果我们希望每次获取到的都是相同的实例的话，可以换一种注册方式，使用`.Singleton`方法代替`.Bind`方法：

``` csharp
services.Singleton<ICar, SportsCar>();
services.Singleton<Driver>();
```
接下来，我们每次获取到的就都是相同的`Driver`了。

再比如如下写法：

``` csharp
services.Singleton<ICar, SportsCar>();
services.Bind<Driver>();
```

这意味着，每次获取到的`Driver`都是不同的新实例，但是每个`Driver`实例中构造函数注入的`ICar`是同一个实例。

<br>

## 注册已存在的实例

假如出于某些缘由，我们一开始就得到了一个`SportsCar`实例对象，我们需要直接使用这个实例对象而不是让服务容器来帮我们创建一个新的实例对象，我们也可以直接把已存在的对象注册进去：

``` csharp
var myCar = new SportsCar();
services.Singleton<ICar>(myCar);
```

这样我们每次从服务容器中获取到的`ICar`就是一开始自己`new`出来的那个。

## 移除

如果出于某些原因，我们注册进去的服务不想要了，可以移除它们：

``` csharp
services.Unbind<ICar>(myCar);
```

## 属性注入

之前我们提到过一种构造函数注入的方式：把依赖项写在构造函数中。

而TinaX还提供另一种属性注入的方式，将上述代码修改如下：

``` csharp
using TinaX;

public class Driver
{
    [Inject]
    public ICar _MyCar { get; set; }

    public void GoHome()
    {
        _MyCar.StartEngine();
        _MyCar.Move();
    }
}
```

我们移除了构造函数，把原来的Field（字段）给写成了Property（属性），并给它添加了一个`[Inject]` Attribute(特性)（命名空间：`TinaX`）.

这样我们的服务容器也会根据`[Inject]`识别到依赖关系，并把依赖的对象注入进去。

默认情况下，如果标记了`[Inject]`的属性类型不存在于服务容器的话，向服务容器获取`Driver`服务时会抛出异常，我们把特性修改成`[Inject(Nullable = true)]`的话，服务容器则会忽略对这个属性的注入而不会抛出异常。

!> 注意，属性注入过程发生在构造函数之后，如果在构造函数中尝试访问它们的话，它们必然是空的。

<br>

## 相关功能

### 创建实例

我看可以用以下方法直接创建实例

``` csharp
var instance = (TargetType)services.CreateInstance(typeof(TargetType));
```

创建过程中会对构造函数和属性进行依赖注入。

<br>

### 注入已存在对象

如果我们有一个已存在的对象，我们可以调用以下方法进行属性注入。

``` csharp
var instance = new TargetType(); 
services.Inject(instance);
```
# IXCore
class in `TinaX`

## 介绍

TinaX.Core框架核心的访问入口。

## 属性

| 项目 | 类型 |  说明 |
| :---- | ---- | :---- |
|BaseGameObject|`UnityEngine.GameObject`| Core会在Scene中创建一个GameObject,并约定所有需要使用GameObject的服务使用该GameObject，或将GameObject创建为该GameObject的子项。 |
| LocalStoragePath_TinaX | `string` |系统路径，约定TinaX所有服务如需读写文件，都会存放在该路径下。该路径默认是Unity`Application.persistentDataPath`的子路径|
| LocalStoragePath_App|`string`| 系统路径，约定业务开发如需读写文件，存放在该路径下。该路径默认是Unity`Application.persistentDataPath`的子路径|
| FrameworkVersionName | `string` | TinaX的主要版本名 |
| IsRunning | `bool` | Framework是否已启动并正在运行|
| ProfileName | `string` | Framework当前所使用的Profile名称 |
| DevelopMode | `bool` | 是否为开发模式，该项在Framework 的 Profile中定义，并非Unity的开发模式判定 |

<br>


<br>

## 方法

### GetService

获取服务接口

方法原型:

``` csharp
TService GetService<TService>(params object[] userParams);

object GetService(Type type, params object[] userParams);
```

| | |
|---|----|
| TService | 泛型，服务接口类型 |
| type | 服务接口类型 |
| userParams | 首次获取服务接口时，传递给接口的构造函数参数|

<br>

### TryGetService

尝试获取服务接口，成功返回true,失败返回false.

方法原型：

``` csharp
bool TryGetService<TService>(out TService service, params object[] userParams);

bool TryGetService(Type type, out object service, params object[] userParams);
```

| | |
|---|----|
| TService | 泛型，服务接口类型 |
| type | 服务接口类型 |
| `out` service | 如果获取服务接口成功，可由此得到所获取的服务 |
| userParams | 首次获取服务接口时，传递给接口的构造函数参数|

<br>

### RegisterServiceProvider

注册服务提供者到Core.

该方法请在Framework启动之前执行。

方法原型：

``` csharp
IXCore RegisterServiceProvider(IXServiceProvider provider);
```

返回值为所访问的`IXCore`自身，可用于链式编程。

| | |
|---|----|
| provider | 欲注册的服务提供者 |

<br>

### BindService

绑定服务接口 （依赖注入服务），通常供服务提供者使用。

被绑定的类在实例化时会被依赖注入。

方法原型:

``` csharp
void BindService<TService, TConcrete>();
```

| | |
|---|----|
| TService | 绑定接口 |
| TConcrete | 实现被绑定接口的类 |

<br>

### BindSingletonService

绑定全局单例服务（依赖注入服务），通常供服务提供者使用。

被绑定的类在实例化时会被依赖注入。

方法原型:

``` csharp
IBindData BindSingletonService<TService, TConcrete>();

IBindData BindSingletonService<TService, TBuiltInInterface, TConcrete>() where TBuiltInInterface : TinaX.Services.IBuiltInService;
```

| | |
|---|----|
| TService | 绑定接口 |
| TConcrete | 实现被绑定接口的类 |
| TBuiltInInterface | 绑定内置服务接口 |

<br>

### TryGetBuiltinService

尝试获取内置服务接口，成功返回true,失败返回false.

方法原型：

``` csharp
bool TryGetBuiltinService<TBuiltInInterface>(out TBuiltInInterface service) where TBuiltInInterface : TinaX.Services.IBuiltInService;
```

| | |
|---|----|
| TBuiltInInterface | 内置服务接口类型 |

<br>

### IsBuiltInServicesImplementationed

查询内置接口是否已被某个服务实现。

方法原型：

``` csharp
bool IsBuiltInServicesImplementationed<TBuiltInInterface>() where TBuiltInInterface : TinaX.Services.IBuiltInService;
```

| | |
|---|----|
| TBuiltInInterface | 内置服务接口类型 |

<br>

### InjectObject

（依赖注入）注入对象：传入一个任意对象，如果该对象有包含*已被注册的服务接口*类型的变量或属性时，则将对应的服务赋值给它。

由于传入的是已实例化的对象，所以注入对构造函数中的参数无效。

方法原型
``` csharp
void InjectObject(object obj);
```

<br>

### OnServicesInitException

捕获服务提供者初始化时，可预料的异常。

可预料的异常： 由服务抛出的继承自`TinaX.XException`的异常。由于其他预料之外的因素导致的异常不会触发该方法，将在运行时直接抛出并中断框架启动。

方法原型：

``` csharp
IXCore OnServicesInitException(Action<string, XException> callback);
```

<br>

### OnServicesStartException

捕获服务提供者启动时，可预料的异常。

可预料的异常： 由服务抛出的继承自`TinaX.XException`的异常。由于其他预料之外的因素导致的异常不会触发该方法，将在运行时直接抛出并中断框架启动。

方法原型：

``` csharp
IXCore OnServicesStartException(Action<string, XException> callback);
```

<br>

### CreateInstance

实例化对象。

内部实现为.NET的`System.Activator.CreateInstance()`

方法原型

``` csharp
object CreateInstance(Type type, params object[] args);
```

| | |
|---|----|
| type | 对象类型 |
| args | 构造方法参数 |

?> 请优先使用该方法替代`System.Activator.CreateInstance()`方法：在使用`ILRuntime`库时，该方法已被ILRuntime绑定CLR重定向，可以实例化解释器中的对象类型。

<br>

### RunAsync

启动框架（异步）

方法原型

``` csharp
Task RunAsync();

void RunAsync(Action<System.Exception> finishCallback);
```

| | |
|---|----|
| finishCallback | 启动完成回调，如果启动过程中出现任何异常，则启动流程中断，回调接收到异常参数。如果启动流程正常结束，回调接收参数为空。 |

!> 使用`Task`方式启动框架时，如果丢弃Task而不`await`的话，所有启动阶段的异常都将无法被编辑器捕获。包括Lua和ILRuntime的入口文件/方法的调用栈上的异常。

### CloseAsync

停用框架（异步）

方法原型

``` csharp
Task CloseAsync();
```
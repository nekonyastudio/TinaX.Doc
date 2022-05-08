# 事件中心

事件中心是TinaX提供的一个实现“发布-订阅”模式（以可称之为观察者模式）的功能。在有些领域，类似的事物亦被称之为事件总线（Event Bus）.是软件开发中一种常见的功能之间的解耦合手段。

- 主要namespace:`TinaX.Core.EventCenter`
- 主要类:`TinaX.Core.EventCenter.EventCenter`
- 常用接口:`TinaX.Core.EventCenter.IEventSubscribe`

事件中心提供多播发布-订阅功能，这意味着可以有多个发布者发布单个事件，同时可以有多个订阅者订阅该同一事件。

<br>

## 基本概念

**事件名**: 事件名为字符串格式，用于订阅和发布事件。

**组**：组为字符串格式，它提供了一个范围 或者说 namespace的概念，不同的组可以有相同的事件名而互不干涉。

<br>

## 发布事件消息

以下代码为最简单的事件消息发布方式，它对外发布了"Hello"消息:

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Send("Hello");
```

我们可以在发布消息时，传递有效负载（参数）给订阅者：

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Send("Hello", "John");
```

以上代码中，"John"是有效负载（参数）。在C#中，上述代码省略了参数类型的制定而由编译器自动推断，我们也可以显式的指定负载类型：

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Send<string>("Hello", "John");
```

如需传递多个参数，可使用class, struct, record等类型定义负载，亦可使用数组、元组、动态类型等:

``` csharp
EventCenter.Send<(string, int)>("Hello", ("John", 10));     //元组（tuples）
EventCenter.Send("Hello", new { name = "John", age = 10 }); //匿名类型 （anonymous-types）
```
在使用Lua等语言时，也可以直接传递`table`.

?> **跨语言使用提示** <br> 由于实际开发业务时使用的语言可能并非C#而是Lua、JavaScript等，则调用C#的泛型方法多有不便。<br>为此，`Send`方法提供了非泛型重载`Send(string eventName, object? args = null)`, 即以`object?`类型接受参数内容。<br>请注意，在此时订阅者必须同样使用**非泛型**方法订阅才可接收到事件。<br>更多内容将在下文描述订阅方法时详细讲解。

<br>


如有需要，我们可在第三个参数中传递`group`名称，以便于在不同的分组中发布消息。

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Send("Hello", "John", "Group1");
```

组功能将事件中心拆分成了一个个平行世界，每个分组可以发布订阅相同的事件名，而不会影响其他分组的事件。

<br>

### 发布者

在仅使用C#语言时，我们可以在发布时携带发布者信息，代码如下:

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Send<MainPage, string>(this, "Hello", "John");
```

上述案例中，泛型参数1表示发布者的类型，泛型参数2表示发布携带的负载类型。对于发布者来说，必须以泛型形式明确指定发布者类型，故只能在C#语言中携带发布者。（而订阅者不必，可接收`object`类型的发布者信息）

<br>

<br>

## 订阅事件消息

使用`Subscribe`方法进行注册以接收消息，如下:

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Subscribe<string>("Hello", name => {
    Console.WriteLine(msg);
});
```

该订阅方法亦包含一些重载方法，如不接收负载（参数），进订阅事件:

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Subscribe("Hello", () => {
    Console.WriteLine("Hello World!");
});
```

也可以指定订阅者类型，如:

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Subscribe<MainPage, string>("Hello", (sender, name) => {
    Console.WriteLine(name);
});
```

使用泛型订阅方法明确指定订阅者类型时，则只有当订阅者类型与发布者类型相同时，才能接收到消息。如果`Send`方法不传递发布者类型，则该订阅者无法接收到消息。

订阅方法亦提供非泛型重载，如:

``` csharp
using TinaX.Core.EventCenter;

EventCenter.Subscribe("Hello", (sender, args) =>
{
    Console.WriteLine($"[{sender}]({sender?.GetType().FullName}) {args}");
});

EventCenter.Subscribe("Hello", args => { }); //亦有不接收sender参数的重载方法
```

此时，所接收的`sender`和`args`方法均为`object?`类型。

只要事件名一致，不同的发送者类型所发送的事件消息均可接收到，而因为`Send`方法可以不传递`sender`，因此该值可能接收到`null`.

而`args`也有可能接收到`null`，因为`Send`方法可以不携带负载。

<br>

如有需要，可在第三个参数中传递`group`名称信息以便于在不同的分组中订阅事件消息。`group`留空时只能接收到`Send`的group也留空的消息，而不会接收到所有group的事件消息。

``` csharp
EventCenter.Subscribe("Hello", () => { }, "Group1");
```

<br>

发送者和订阅者的可达性关系总结：
- 发送者和订阅者的`Group`名称应该一致（默认值`null`本身也是一个group）
- 订阅者不订阅`Sender`时，可以收到所有同名同组事件消息.(委托类型为`EventHandlerEmpty`或 `EventHandlerWithObjectArgs`)
- 订阅者订阅`object?`类型`Sender`时, 可收到所有同名同组事件消息.（委托类型为`EventHandlerWithObjectSenderAndArgs`）
- 订阅者泛型指定了负载类型`TArgs`时，则只有同样指定了泛型负载类型发送方法所发事件消息才会被接收。(委托类型为`EventHandler<TArgs>`)
- 订阅者泛型指定了发送者类型`TSender`时，则只有同样指定了泛型发送者类型发送方法所发事件消息才会被接收。(委托类型为`EventHandler<TSender, TArgs>`)

<br>

使用方式推荐：

- 如仅使用C#语言开发业务逻辑（包括跨越热更工程，如ILRuntime）时，建议优先使用泛型订阅和发送方法，避免不必要的装箱和拆箱。（绝大多数情况下，C#语法可以省略掉泛型的`< >`符号由编译器自动推断，因此写法上并不会更麻烦）
- 跨语言开发业务（如热更层为Lua、TypeScript、JavaScript）时，可在C#中使用泛型方法发送事件消息，而在其他语言中使用`object`类型的非泛型方法订阅事件消息。
- 跨语言开发业务时，可在C#层封装特殊的Sender类以实现其他语言之间的`sender`传递。

<br>

## 取消订阅

在订阅事件时，会得到返回值，类型为接口`IEventSubscribe`, 调用其`Unsubscribe`方法即可取消订阅。

``` csharp
using TinaX.Core.EventCenter;

var subscribe = EventCenter.Subscribe("Hello", () => { });
subscribe.Unsubscribe(); //取消订阅
```

如在继承自`MonoBehaviour`类的组件代码中，应该在`OnDestroy`方法中调用`Unsubscribe`方法，以免出现意料之外的结果。

?> 请注意不要持久将`IEventSubscribe`保存在某处，可能会导致内存泄漏。

如不存下订阅事件方法的返回值，也可使用`EventCenter.Unsubscribe`方法取消订阅，但这样就不能使用lambda表达式了, 因为`Unsubscribe`中需要传递和`Subscribe`方法中一致的callback参数。

``` csharp
void OnEvent_Hello()
{

}

EventCenter.Subscribe("Hello", OnEvent_Hello);

EventCenter.Unsubscribe("Hello", OnEvent_Hello);
```


<br>

## 自动取消订阅

通常，如果我们放置在`GameObject`上的组件需要订阅事件时，我们需要在`OnDestroy`方法中调用`Unsubscribe`方法以免意外。

但如果有大量的订阅需要手动调用`Unsubscribe`方法，一方面可能会出现编写代码上的疏忽导致意外，另一方面也很麻烦。

通常我们可以使用框架提供的[DisposableGroup](/v6.6/index.html#/cmn-hans/core/manual/DisposableGroup ':ignore :target=_blank')类来统一管理订阅的取消。

又或者，我们可以将事件订阅与`GameObject`进行绑定，待GameObject销毁时，该事件也会自动随之销毁，代码如下：

``` csharp
EventCenter.Subscribe("Hello", () => { }).BindTo(this); 
```

?> 如果在继承自MonoBehaviour的类中，`BindTo`可直接传入`this`. 或者可传入`GameObject`对象。


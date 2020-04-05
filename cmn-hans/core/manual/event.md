# 事件系统

事件系统是游戏中常用的解耦合手段。它又被称为事件分发系统、消息系统、消息广播系统等等。

?> 事件系统位于`TinaX`命名空间下的`XEvent`类。

?> `TinaX.Lua`中包含了事件系统的Lua封装: `XCore.Event`

## 注册事件监听

``` csharp
XEvent.Register("eventName", param =>
{
    // do something
});
```

方法原型：

``` csharp
IEventTicket XEvent.Register(string EventName, Action<object> handler, string EventGroup = DefaultGroup)
```

|项|说明|
|:---|:---|
|`IEventTicket`| 返回值，可用于取消当前注册的事件监听|
|`EventName` | 监听事件名 |
|`handler`| 处理事件监听的方法，可接收`object`类型参数|
|`EventGroup`|事件组，通常可留空，默认值为`"default"`|

#### Lua 用法说明

?> `TinaX.Lua`服务中默认提供了事件系统的Lua封装，可以直接调用

``` lua
XCore.Event.Register("eventName",function(data)
    -- do something.
end);
```

Lua 方法原型：

``` lua
XCore.Event.Register(eventName, handler, eventGroup)
```

其中，`eventGroup`可留空，参数和返回值与C#一致

<br>

<br>

## 触发事件

``` csharp
XEvent.Call("eventName");

XEvent.Call("eventName", "hello"); //携带参数
XEvent.Call("eventName", 10); //携带参数
XEvent.Call("eventName", new string[]{"hello","world"}); //携带参数
```

方法原型：

``` csharp
void Call(string eventName, object param = null, string eventGroup = DefaultGroup)
```

|项|说明|
|:---|:---|
|`eventName` | 触发的事件名 |
|`param`| 触发事件时携带的参数，可留空 |
|`eventGroup` | 事件组，通常留空，默认值为`"default"`.|

#### Lua 用法说明


``` lua
XCore.Event.Call("eventName");

XCore.Event.Call("eventName", "hello"); -- 携带参数
XCore.Event.Call("eventName", {id = 0, num = 2}); --携带lua table参数
XCore.Event.Call("eventName", "hello", "eventGroup"); --指定group
```

Lua 方法原型：

``` lua
XCore.Event.Call(eventName, param, eventGroup)
```
参数和返回值与C#一致

<br>

<br>

## 移除事件注册

请先看以下案例：

**C#** 

``` csharp
void EventHandler(object param)
{
    // do something;
}

XEvent.Register("meow", EventHandler);
XEvent.Register("meow 2", EventHandler);
```

**Lua**

``` lua
function event_handler(data)
    -- do something    
end

XCore.Event.Register("meow", event_handler);
XCore.Event.Register("meow 2", event_handler);
```

当我们需要移除事件注册时，可以调用`Remove`方法，传递注册时传入的"handler"参数即可：

**C#**

``` csharp
XEvent.Remove(EventHandler);
```

**Lua**

``` lua
XCore.Event.Remove(event_handler);
```

如果只传递handler的话，所有使用该handler注册的事件都会被移除。也就是案例中的“meow”和“meow 2”两个事件都被被移除。

假如说，我们只想移除某个具体的注册，保留其他注册到该handler的事件的话，可以传入具体的事件名和组：

**C#**

``` csharp
XEvent.Remove(EventHandler,"meow"); 
```

**Lua**

``` lua
XCore.Event.Remove(event_handler,"meow");
```

如上所示，"meow"事件对应的注册已经被移除，但是该handler依然可以响应"meow 2"的事件监听。

<br>

### IEventTicket

如果我们注册事件监听时使用的是匿名函数表达式的话，上述的`Remove`方法就没法用了。

除此之外，我们还可以利用注册事件时返回的`IEventTicket`来取消事件注册。

``` csharp
var event_meow = XEvent.Register("meow", p => { }); //注册
event_meow.Unregister(); //取消注册
```

**Lua**

``` lua
local event_meow = XCore.Event.Register("meow", function(data)
    -- do something
end)
event_meow:Unregister();
```

?> `IEventTicket` 位于 `TinaX.Systems` 命名空间
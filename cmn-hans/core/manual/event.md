# 事件系统

事件系统是游戏中常用的解耦合手段。它又被称为事件分发系统、消息系统、消息广播系统等等。

?> 事件系统位于`TinaX`命名空间下的`XEvent`类。


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

<br>

## 触发事件

``` csharp
XEvent.Call("eventName");

XEvent.Call("eventName", "hello"); //携带参数
XEvent.Call("eventName", 10); //携带参数
XEvent.Call("eventName", new string[]{"hello","world"}); //携带参数
```
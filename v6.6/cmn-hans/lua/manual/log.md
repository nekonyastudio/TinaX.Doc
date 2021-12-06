# Lua Logger

TinaX对Lua的Log消息做了封装。

## 输出消息

### print

`print`是lua原来就有的api，但在TinaX中`print`被重写并对应到Unity的`Debug.Log`方法。

你可以正常使用`print("message")`的方式输出log，同时也可以在第二个参数中传入上下文以在Unity编辑器中强调内容（与`Debug.Log`完全一致）

``` lua
print("hello");

local go = CS.UnityEngine.GameObject("aaa");
print("myGo",go);
```
### printW / printE
类似的，与Unity中`Debug.LogError`和`Debug.LogWarning`对应的分别是`printE()`和`printW()`， 使用方法一致

## dump

我们可以使用`dump()`方法将lua table中的内容打印在console中。

``` lua
local t = {
    id = 1,
    lv = 1,
    data = {
        age = 16,
        height = 150,
    }
}

dump(t);
```

`dump`方法的第一个参数传入lua元素，第二个参数可以传入一个字符串，它会作为标题打印在console中，可留空。

`dump`的第三个参数可传入一个number, 它会作为dump的打印深度。如上述案例代码中的table "t"，当dump深度为1时，`t.data`中的子项将不会被详细输出。

## 调用堆栈

在默认状态下，TinaX在处理log输出时会在log消息后附加该log在lua中的调用堆栈（lua traceback）.

相应的，这意味着会在输出log时消耗更多的资源。如果有需要的话，我们可以使用以下代码关闭它：

``` lua
XCore.Logger.EnableTraceback = false; 
-- 想再打开就再设为true即可。
```

该设置的有效范围是全局的lua部分。
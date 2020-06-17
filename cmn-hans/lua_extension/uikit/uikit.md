# 针对 TinaX.UIKit 的Lua扩展

代码入口：`XCore.UIKit`

## 打开UI

方法原型：
``` lua
XCore.UIKit.OpenUI(ui_param, ...)
```


参数`ui_param`接收一个`table`或一个`string`，传入`table`时，其中可包含如下信息：

| key | 类型 | 可否留空 | 默认值 | 说明 | 
|----|-------|----------|------|-------|
| `name` | `string` | `false` | - | UI名称 |
| `useMask` | `boolean` | `true`| `false` | 使用UI遮罩 |
| `closeByMask` | `boolean` | `true`|`false`|可否通过点击UI遮罩来关闭UI |
|`maskColor`|`UnityEngine.Color`|`true`|项目设置中的值 | UI遮罩的颜色 |
| `root`|`UnityEngine.Transform`|`true`| UIKit内部分配 | UIRoot, UI文件的父transform，例如如果希望把UI显示在场景中而非屏幕上，可以将场景中某个点的transform复制给`root`。通常留空 |
| `behaviour` | `TinaX.XComponent.XBehaviour` | `true` | `nil` | 如果被打开的UI的`UI Handler`是一个`XComponent`的话，可通过该参数传递对应的behaviour. 对于使用LuaBehaviour的UI不用管 |
| `DI` | `boolean` | `true` | `true` | 依赖注入，在上一个参数`behaviour`传入有效的对象时，该参数表示是否要对传入的对象进行依赖注入。对于Lua管理的UI不用管|

当传递参数类型为`string`时，则表示“UI Name”，而上方列表所示的其他项皆为默认值。

在`ui_param`之后为变长参数，用于向被打开的UI传递“UI被打开时的参数”

<br>

异步方法原型

``` lua
XCore.UIKit.OpenUIAsync(ui_param, callback ,...)
```

参数`callback`为lua `function` ,可接收两位参数：`IUIEntity`和打开时的异常，正常打开时异常为`nil`。

其余参数与同步方法一致。

示例：

``` lua
-- 打开UI （同步）
XCore.UIKit.OpenUI("mainScreen");

XCore.UIKit.OpenUI({
    name = "msgBox",
    useMask = true
},"hello","meow");

-- 打开UI (异步)

XCore.UIKit.OpenUIAsync("mainScreen", function(ui_entity,err)
    if err ~= nil then 
        -- xxxxx
    end 
end)

```

<br>

## 关闭UI

方法原型：
``` lua
XCore.UIKit.CloseUI(ui_name, ...)
```

参数`ui_name`接收UI名，之后为可变参数，用于向被关闭的UI传递“UI关闭时的消息”

示例

``` lua
XCore.UIKit.CloseUI("mainScreen");
```
# UIKit API

TinaX的UI系统

- 命名空间：`TinaX.UIKit`
- C#入口：`TinaX.UIKit.UIKit.Instance` or `TinaX.UIKit.UIKit.I`
- C# [接口文件参考](https://github.com/yomunsam/TinaX/blob/master/Assets/TinaX/Core/System/UIKit/Script/IUIKit.cs)
- Lua入口：`XCore.UIKit`


# C#属性

| 属性 | 类型 | 说明 |
|------| ------|-----|
| `IsAdvanced` | `bool` | 是否为UIKit 高级模式 |
| `UIKit_UIRoot_RectTrans` | `UnityEngine.RectTransform` | 获取UIKit中UI根级节点（UIRoot)对应的`RectTransform` |
| `UIKit_UICamera_GameObject` | `UnityEngine.GameObject` | 获取UIKit中UI相机所在的GameObject |
| `UIKit_UICamera` | `UnityEngine.Camera` | 获取UIKit中的UI相机对象|

### C#属性与对应的Lua封装

| 属性 | 对应Lua **function** 封装 |
|------| ------|
|  `IsAdvanced`         | `XCore.UIKit.IsAdvanced()`|
|`UIKit_UIRoot_RectTrans`| `XCore.UIKit.GetUIRootRectTransform()` |
|`UIKit_UICamera_GameObject`| `XCore.UIKit.GetUICameraGameObject()` | 
| `UIKit_UICamera` | `XCore.UIKit.GetUICamera()`|


# 方法

## 设置UI

设置UI系统当前使用的UI组。

如果有在UIKit配置中设置默认UI组的话，在启动游戏后，UI系统会载入并启用配置文件中的默认UI组。

### CSharp

原型：

``` csharp
void SetUIGroup(UIGroupConf ui_group)
```

- 参数： UI组对象，可通过Resources或TinaX资源系统加载它。

用例

``` csharp
using TinaX.UIKit;

var uiGroup = Resources.Load<UIGroupConf>("Game/UI/defalutGroup");
UIKit.Instance.SetUIGroup(uiGroup);
```

### Lua

原型： 
``` lua
XCore.UIKit.SetUIGroup(ui_group)
```

- 参数： UI组对象，可通过Resources或TinaX资源系统加载它。



------

## 打开UI

> TinaX 5.x开始默认规范使用 __UI组中设置的名称__ 作为打开UI的标识 （但保留了使用路径名加载的功能）

高级模式和普通模式，打开UI的方式完全一致

### CSharp 通过UI名打开UI

原型：

``` csharp
IUIEntity OpenUI(string ui_name);
IUIEntity OpenUI(string ui_name, System.Object ui_param);
IUIEntity OpenUI(string ui_name, bool use_mask, bool close_by_mask);
IUIEntity OpenUI(string ui_name, System.Object ui_param, bool use_mask, bool close_by_mask = false);
```

参数

| 参数 | 类型 | 说明 | 默认值 |
| ------| ------ |------ | ----|
| ui_name | `string` | UI名称 | - |
| ui_param | `System.Object` | 如果不为空，此处参数将作为UI的启动参数传递给对应UI的__UI主管理对象__| `null`|
| use_mask| `bool` | 是否使用UI遮罩 | - |
| close_by_mask| `bool` | 可否在点击遮罩时关闭UI（点击空白处关闭） | - |


用例：

``` csharp
    //打开一个UI
    UIKit.Instance.OpenUI("mainScreen");    //"mainScreen"是在UI组中设置绑定的UI名称

    //打开一个提示窗，使用遮罩,传递参数给提示框
    UIKit.Instance.OpenUI("msgBox","请按关机键继续下一步",true,true);    
        //"msgBox"是在UI组中设置绑定的UI名称
        //"请按关机键继续下一步"这段文本会作为UI的启动参数传递给"msgBox" 这个UI
```
### Lua 整合后的打开UI

原型：

``` lua
XCore.UIKit.OpenUI(table)
```

其中table的传入值为：

| key | 类型 | 说明 | 默认值 |
| ------| ------ |------ | ----|
| ui_name | string | UI名 | nil |
| ui_path | string | UI路径 | nil |
| use_mask | bool | 是否使用遮罩 | false| 
| smart_close | bool | 在使用遮罩时，点击遮罩可否关闭该UI | false |
| data | table / Object | 如果不为空，此处参数将作为UI的启动参数传递给对应UI的__UI主管理对象__ | nil |

- 返回 IUIEntity UI实例对象接口
- ui_name和ui_path**至少要传入一个**

!> 当传给UI的启动参数 data 为table类型时，接收方的UI主管理对象必须为LuaBehaviour，如果接收方的UI主管理对象是CSharp的对象，则不会“收到触发启动参数”的方法.


用例

``` lua
    //打开一个UI
    XCore.UIKit.OpenUI({
        ui_name = "mainScreen"
    })

    //打开一个提示窗，使用遮罩,传递参数给提示框
    XCore.UIKit.OpenUI({
        ui_name = "msgBox",
        use_mask = true,
        smart_close = false,
        data = {                --UI接收方的 UI主管理对象 将收到data的完整的table
            title = "恭喜"，
            content = "请按关机键继续下一步",
        }
    })

    //传递给CSharp管理的UI时，data参数不可以为table
    XCore.UIKit.OpenUI({
        ui_name = "msgBox",
        use_mask = true,
        smart_close = false,
        data = "这里是字符串"
    })

```

------

## 关闭UI 

关闭UI时可传递参数给被关闭的UI，使用方法与相关事项与打开UI类似

### CSharp 

通过名称或ID

``` csharp
//普通模式
void CloseUI(string ui_name, System.Object Param = null);

//高级模式，传入创建UI时分配的ID
void CloseUI(int ui_id, System.Object Param = null);
```

通过路径（高级模式不可用）

``` csharp
//普通模式
void CloseUIByPath(string ui_path, System.Object Param = null);
```

### Lua

通过名称或ID (string/number)

``` lua
XCore.UIKit.CloseUI(ui_name_or_id,data) --data为传入参数，可为空
```

通过路径(string)（高级模式不可用）

``` lua
XCore.UIKit.CloseUI_ByPath(ui_path,data)  --data为传入参数，可为空
```


------

## 隐藏UI 

UI打开后，可以通过该命令隐藏。
注意： UI隐藏后，`MonoBehaviour` 和 `LuaBehaviour` 将被暂停，如果有不暂停的需求，需要在编辑器中`UIEntity`组件上勾选`在隐藏时不要暂停`

### CSharp 

通过名称或ID

``` csharp
//普通模式
void HideUI(string ui_name);

//高级模式
void HideUI(int id);
```

通过路径，高级模式不可用

```csharp
void HideUIByPath(string ui_path);
```

### Lua

通过名称或ID (string/number)

``` lua
XCore.UIKit.HideUI(ui_name_or_id)
```

通过路径(string)，高级模式不可用

``` lua
XCore.UIKit.HideUI_ByPath(ui_path)
```

------

## 显示被隐藏的UI 

### CSharp 

通过名称或ID

``` csharp
//普通模式
void ShowUI(string ui_name);

//高级模式
void ShowUI(int ui_id);
```

通过路径，高级模式不可用

``` csharp
//普通模式
void ShowUIByPath(string ui_path);
```

### Lua

通过名称或ID (string/number)

``` lua
XCore.UIKit.ShowUI(ui_name_or_id)
```

通过路径(string)，高级模式不可用

``` lua
XCore.UIKit.ShowUI_ByPath(ui_path)
```




<details>
<summary>其他说明</summary>
    
    1. OpenUI之后，UI已经是显示的了，不需要调用ShowUI再打开一遍。

    2. UI的加载操作是同步加载，因此不建议在UI中放置大量资源，应在UI被打开之后，通过异步加载方式载入。

</details>

------

## 坐标转换：获取某个UI元素相对UIKit UIRoot（全屏）的坐标

在实际UI开发中，我们有时候可能想要获得某个塞在一层层嵌套对象中的UI元素相对全屏的UI坐标，这种时候只要把这个元素的transform传入该方法即可

C# 原型：

``` csharp
Vector2 GetUIScreenLocalPoint(Transform trans); 
```

Lua 原型：

``` lua
XCore.UIKit.GetUIScreenLocalPoint(ui_transform);
```



------

## 代码案例

### 普通模式的UI操作（Lua）

``` lua
--打开UI
local msgBox = XCore.UIKit.OpenUI({
    ui_name = "msgBox",
    use_mask = true,
    smart_close = true
})

--隐藏UI 方法1
XCore.UIKit.HideUI("msgBox")

--隐藏UI 方法2
msgBox:Hide()

--显示UI 方法1
XCore.UIKit.ShowUI("msgBox")

--显示UI 方法2
msgBox:Show()

--关闭UI 方法1
XCore.UIKit.CloseUI("msgbox")

--关闭UI 方法2
msgBox:Close()
msgBox = nil
```

### 高级模式下的UI操作（Lua）

``` lua
--打开UI
local msgBox = XCore.UIKit.OpenUI({
    ui_name = "msgBox",
    use_mask = true,
    smart_close = true
})

local msgBox_ID = msgBox:GetID()

--隐藏UI 方法1
XCore.UIKit.HideUI(msgBox_ID)

--隐藏UI 方法2
msgBox:Hide()

--显示UI 方法1
XCore.UIKit.ShowUI(msgBox_ID)

--显示UI 方法2
msgBox:Show()

--关闭UI 方法1
XCore.UIKit.CloseUI(msgBox_ID)

--关闭UI 方法2
msgBox:Close()

msgBox = nil
msgBox_ID = nil
```

### 普通模式下的UI操作（C#)

``` csharp
//普通模式

using TinaX.UIKit;
using UnityEngine;

public class DemoUIKit : MonoBehaviour
{
    void Start()
    {
        //普通模式

        //打开UI

        var msgBox = UIKit.I.OpenUI("msgbox", "喵！", true, true); //UIKit.I 是 UIKit.Instance 的简写，意思一样的，TinaX的Instance模块基本都支持这么写

        //隐藏UI
        msgBox.Hide();
        //隐藏UI 2
        UIKit.I.HideUI("msgbox");

        //显示UI
        msgBox.Show();
        //显示UI 2
        UIKit.I.ShowUI("msgbox");

        //关了
        msgBox.Close();
        //或者
        UIKit.I.CloseUI("msgbox");
    }

}
```


### 高级模式下的UI操作（C#)

``` csharp
using TinaX.UIKit;
using UnityEngine;

public class DemoUIKit : MonoBehaviour
{
    void Start()
    {
        //高级

        //打开UI
        var msgBox = UIKit.I.OpenUI("msgbox", "喵！", true, true); //UIKit.I 是 UIKit.Instance 的简写，意思一样的，TinaX的Instance模块基本都支持这么写
        var id = msgBox.GetID();

        //隐藏UI
        msgBox.Hide();
        //隐藏UI 2
        UIKit.I.HideUI(id);

        //显示UI
        msgBox.Show();
        //显示UI 2
        UIKit.I.ShowUI(id);

        //关了
        msgBox.Close();
        //或者
        UIKit.I.CloseUI(id);
    }

}
```
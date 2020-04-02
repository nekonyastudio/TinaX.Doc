# Lua Script Runtime

在TinaX中使用Lua语言愉快的开发。

-----

## 概述：LuaMainRuntime 和 LuaBehaviour

[LuaBehaviour](/api/system/lua_runtime/luabehaviour) 是为了让Lua代码像`MonoBehaviour`一样可以作为`Component`被`GameObject`绑定而开发的一个组件。

`LuaBehaviour`的环境和TinaX的Lua主运行环境是相对独立的，在使用上也有着一定的区别：

- `LuaBehaviour`拥有部分`MonoBehaviour`的生命周期，在运行时可被创建、销毁；而Lua主运行环境通常在游戏运行开始时启动，通常也不会被销毁。
- `LuaBehaviour`相对独立，LuaBehaviour可以调用Lua主运行环境中的全局方法，而LuaBehaviour中的全局方法，只在当前LuaBehaviour脚本中有效。
- 通过lua方法`require`或 TinaX下的C#入口`LuaScript`中的`RunFile`, `DoString`方法运行的Lua代码，在Lua主运行环境中，通过`LuaBehaviour`组件绑定的代码文件，运行在`LuaBehaviour`中


## LuaScript API

`LuaScript` 是 TinaX 的C# 侧API入口，提供如下方法

### RunFile

原型

``` csharp
void RunFile(string path)
```

该方法本质上是在调用Lua中的`require`方法，传入的路径需要包含在资源管理范围内。

用例

``` csharp
using TinaX;

LuaScript.I.RunFile("Assets/Lua/init.txt");
```

### DoString

原型：

``` csharp
void DoString(string LuaCodeStr, string chunkName = "chunk", LuaTable luaEnv = null);
```
> 这个方法本质上就是xLua里的DoString， 与lua里的dostring是类似的

用例：

``` csharp
using TinaX;

LuaScript.I.DoString("print(\"hello world\");");
```

### CSharp侧调用Lua全局方法

TinaX侧提供了CSharp调用Lua全局方法的方法。

原型：

``` csharp
void RunLuaFunc(string FuncPath);

R RunLuaFuncR<R>(string FuncPath);

void RunLuaFunc<T>(string FuncPath, T Param1);

R RunLuaFuncR<T, R>(string FuncPath, T Param1);

void RunLuaFunc<T1, T2>(string FuncPath, T1 Param1, T2 Param2);

R RunLuaFunc<T1, T2, R>(string FuncPath, T1 Param1, T2 Param2);
```

| 项  | 说明 |
|----|-----|
| FuncPath  | lua 方法在Global的调用路径                       |
| Param*    | 调用方法附带的传入参数                          |
| T / T*        | 泛型参数：调用Lua方法时附带的参数的类型           |
| R         | 泛型参数：如需Lua方法提供返回值的话，返回值的类型  |

<!-- 为了便于区分各个泛型的用途，带返回值的方法，结尾加了个“R”, -->

用例：

Lua： 

``` lua
MainScreenManager = {}

function MainScreenManager.Hello()
    print("喵喵喵")
end

function MainScreenManager.Hello2(num)
    return num + 1
end
```

CSharp:

``` csharp
using TinaX;

LuaScript.I.RunLuaFunc("MainScreenManager.Hello");

int meow = LuaScript.I.RunLuaFuncR<int,int>("MainScreenManager.Hello2", 666);
```


------

?> 那么，如果我想在C#侧调用LuaBehaviour中的方法怎么办呢。 

目前没什么好办法，推荐走[事件系统](system_event)，TinaX的事件系统是跨语言的
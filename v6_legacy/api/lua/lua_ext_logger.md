# [Lua扩展]Logger

<!-- more -->

## print输出

类似于Unity/C#中的`Debug.Log()`，TinaX对Lua中原生的print方法进行了重写。

```lua
print(msg)
```

同时支持

```lua
print(msg,gameObject)
```

的方式将输出内容在控制台与GameObject绑定。

------

## echo

支持`echo(msg)` 和 `echo(msg,gameObject)` 方法，与print作用一致。

------

## 输出警告

```lua
printW(msg)
```

```lua
printW(msg,gameObject)
```


------

## 输出错误

```lua
printE(msg)
```

```lua
printE(msg,gameObject)
```

------

<details>
<summary>说明</summary>
    Lua层的输出方法封装自`TinaX.XLog`，而非UnityEngine的Debug类，与Debug.Log之类的方法也并不完全一致。

    UnityEngine的Debug类在输出的时候会进行堆栈打印，因此我们会感觉到它明显的比较占资源，尤其在移动端的时候，这个打印堆栈的操作就更难以忍受了。

    TinaX的XLog类则对这些东西进行了优化，在编辑器模式，XLog类与Unity的Debug类无异，而在打包发布时，可以指定某些Log不经过Debug类。
    如“只输出Error”, “全部Log直接远程回收，不走Debug类”之类的玩法。
</details>


------

## dump

`dump`是一种很常见但Lua中又没有的东西，然后TinaX的Lua里就给弄出来一个ヽ(ﾟ∀ﾟ*)ﾉ

dump命令用于将Lua变量中的数据全部打印出来，它和print的区别在于，它不但可以打印字符串和数值，还可以打印布尔、table之类的东西。

用法：

``` lua
dump(data)
```

传入需要打印的变量内容。

也可以使用稍微复杂的用法

```lua
dump(data,"标题",6)
```

第三个参数为打印深度。

比如： 

``` lua

local demo_table = {
    a = 10, 
    b = 12,
    c = "13",
    d = {
        d1 = 5,
        d2 = function()
            --emmmm
        end
    }
}

dump(demo_table);

```

运行后的输出结果为：

    [Log] [TinaX Lua]dump from: Assets/xxx/Lua/init.lua.txt:xxx: in main chunk
    [Log] [TinaX Lua]- ""<var>"" = {
    [Log] [TinaX Lua]-     ""a"" = 10
    [Log] [TinaX Lua]-     ""b"" = 12
    [Log] [TinaX Lua]-     ""c"" = ""13""
    [Log] [TinaX Lua]-     ""d"" = {
    [Log] [TinaX Lua]-         ""d1"" = 5
    [Log] [TinaX Lua]-         ""d2"" = function: 00000282E29C8D70
    [Log] [TinaX Lua]-     }
    [Log] [TinaX Lua]- }

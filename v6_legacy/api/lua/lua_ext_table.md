# Lua扩展 - table

## 复制 table

lua中的参数传递什么的默认都是传的引用类型，这有时候会造成不方便，因此需要复制table.

原型：

``` lua
table.clone(table_object);
```

------

## C#数组转table

C#传过来的数组是没法直接按照lua的习惯操作的，比如下标依然从0开始，比如无法被迭代器识别。

原型：
```lua
table.cs_array_to_table(arr);
```

------

## 实验性功能：可监听的table

用例：

```lua

--生成一个可监听的table
local demo_table = table.Create()
demo_table.a = 1

--也可以传入一个已有的table
local demo_table2 = {
    a = 1
}
demo_table2 = table.Create(demo_table2)

--然后我们就可以监听这个table里的数据变动了
local function OnValueChanged(key,value)
    print("key: " .. key .. "的值发生变动：" .. value) 
end

table.Listen(demo_table,OnValueChanged)

--同时也可以只监听某一个key
local function On_A_Changed(value)
    print("key a 的值发生变动:" .. value)
end

table.ListenKey(demo_table2,"a",On_A_Changed,false); --参数4：是否在注册成功后，无论数据是否变动立即收到一次回调，留空默认为false


--在不需要的时候，移除监听
table.RemoveListen(demo_table,OnValueChanged);

table.RemoveListenKey(demo_table2,"a",On_A_Changed)


```
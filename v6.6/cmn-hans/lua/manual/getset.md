# Lua table 变动事件

在C#中，我们可以通过`get/set`的形式声明一个属性(property),以此来监听数值的变动：

``` csharp
string _meow = "meow";
string Meow {
    get => _meow;
    set{
        _meow = value;
        //do something...
    }
}
```

在TinaX中，我们可以利用table来实现相似的用法。该功能使用lua元表实现。

首先，我们创建一个可监听值变动的table.

``` lua
local myTable = table.create();
```

或者我们也可以把一个已经存在的table给变成可监听变动的table：

``` lua
local t = {
    id = 1,
    lv = 1,
    score = 100
}
local myTable = table.create(t);

-- 注意：该案例中，table "t" 与 "myTable" 之间是独立的，对其中一方的修改并不会影响另一方。
```

<br>

<br>

然后，我们可以对该table创建一个数值变动的监听：

``` lua
local ticket = myTable:listen(function(key,value)
    print("key " .. key .. " 的值发生变动:" .. value);
end)

-- 或

local ticket = table.listen(myTable,function(key,value)
    print("key " .. key .. " 的值发生变动:" .. value);
end)
```

上述方式是对整个table中所有key的监听，如果有需要的话，我们也可以指监听某一个key:

``` lua
local ticket = myTable:listenKey("score",function(value)
    print("score 发生变化：" .. value);
end, true) --第三个参数传递一个布尔值,如果为true的话，在设置监听后立即就会触发第二个参数传入的方法，可留空，默认为false;

-- 或

local ticket = table.listenKey(myTable,"score", function(value) 
    print("score 发生变化：" .. value);
end, true)
```


<br>

<br>

注册监听之后，当table中对应值变动时，会触发注册监听的处理function:

``` lua
myTable.score = 99; -- 监听全局和监听`score` key的处理function都被被触发

myTable.data.location = 0; --不会触发任何监听，因为监听只对table的直接子项有效
```

<br>

<br>

为了安全性，有时候我们需要移除之前注册的监听。在注册监听时得到的返回值可以帮助我们快速移除监听：

``` lua
local ticket = myTable:listenKey("key name",function(value) end);

ticket:remove(); --移除，上面代码注册的function将不会再被触发。
```

而如果没有使用匿名函数的话，我们也可以用下面这种方法移除：

``` lua
local function onValueChanged(value)
    -- xxx
end
myTable:listenKey("key name",onValueChanged); --注册

table.removeListenKey(myTable,"key name",onValueChanged); --移除

-------------------------------------------------------------
local function onAnyKeyChanged(key,value) 
    -- xxx
end

myTable.listen(onAnyKeyChanged); --注册

table.removeListen(myTable,onAnyKeyChanged); --移除
```
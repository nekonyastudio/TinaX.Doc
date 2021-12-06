# API - table

### table.clone

克隆table. 

通常table是作为引用类型传递的，而如果我们希望在某处更改table不会影响别处，就需要克隆table.

``` lua
table.clone(table)
```

<br>

### table.cs_array_to_table

将C# 数组转换为table. 

在lua中，如果我们直接访问C#数组的成员，索引下标是从0开始的。而转换成table，索引下标是从1开始的。

``` lua
table.cs_array_to_table(cs_array)
```

<br>

<br>

### table.create 

创建一个可绑定监听的table. 详见[table 监听变动](/cmn-hans/lua/manual/getset.md)

``` lua
table.create(table) --参数可留空
```

### table.listen

对`可监听数据变动的table`创建针对所有key的全局监听，详见[table 监听变动](/cmn-hans/lua/manual/getset.md)

``` lua
table.listen(table,func)
```

### table.removeListen

移除`可监听数据变动的table`中某个事先注册的全局监听，详见[table 监听变动](/cmn-hans/lua/manual/getset.md)

``` lua
table.removeListen(table,func)
```

### table.listenKey

对`可监听数据变动的table`创建针对某个key的监听，详见[table 监听变动](/cmn-hans/lua/manual/getset.md)

``` lua
table.listenKey(table,key,func,invoke_now)
-- invoke_now: 是否在注册监听后立即触发处理该key变动的function，可留空，默认false
```

### table.removeListenKey

移除`可监听数据变动的table`中某个事先注册的针对某个key的监听，详见[table 监听变动](/cmn-hans/lua/manual/getset.md)

``` lua
table.removeListenKey(table,key,func)
```
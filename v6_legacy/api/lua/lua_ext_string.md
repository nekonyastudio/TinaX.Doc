# Lua扩展 - String

lua 字符串扩展

<!-- more -->

## string.split 分割字符串

原型

``` lua
string.split(str,sep)
```

- 参数1： 待分割的字符串
- 参数2： 分割字符串的文本

用法

``` lua
local str = "E_30_2"
local str_arr = string.split(str,"_")

--分割后的结果分别为"E"  "30"  "2"

```

## string.trim 删首尾空

传入一段字符串，返回删掉其首尾空格后的结果

原型：

```lua
string.trim(str)
```


## string.trim_start 删首空格

传入一段字符串，返回删掉其开头空格后的结果

原型：

```lua
string.trim_start(str)
```


## string.trim_end 删尾空格

传入一段字符串，返回删掉其尾部空格后的结果

原型：

```lua
string.trim_end(str)
```


## string.isNilOrEnpty 是否为空字符串

如果传入变量为nil或者为""，则返回true

原型

```lua
string.isNilOrEnpty(str)
```

## string.filterNullChar 过滤字符串中的空字符

原型：

``` lua
string.filterNullChar(str)
```

## string.ToBase64 加密为Base64

原型：

```lua
string.ToBase64(str)
```

> 这里的Base64转换是Lua实现的，没调用C#那边的扩展方法。哪个运行效率高没测过。

## string.Base64ToStr 解码Base64

原型：

```lua
string.Base64ToStr(base64)
```
# C#方法扩展 ： String 字符串

TinaX中对C#方法进行了一些常用的扩展。


- 命名空间： `TinaX`

## IsMail 是否为邮箱

用法：

``` csharp
    string mail = input.text;
    if(!mail.IsMail())
    {
        Debug.Log("输入的邮箱格式不正确");
        return;
    }
```



## Contains 高级比较

可传入`System.Text.StringComparison` 进行高级比较，如忽略大小写等，用法与原生的Contains无异




## IncludeChinese 是否包含中文

用法：

``` csharp
    string user = input.text;
    if(user.IncludeChinese())
    {
        Debug.Log("输入的内容包含中文内容");
        return;
    }
```



## Reverse 反转字符串

用法：

``` csharp
    string balabala = "abcde"
    Debug.Log(balabala.Reverse());
    //输出结果为：edcba
```

> 这里其实是对[CatLib](https://catlib.io/v1/helper/str.html#!Reverse)的封装。

 



## ToBase64 字符串编码Base64

用法：

``` csharp
    string balabala = "abcde"
    Debug.Log(balabala.ToBase64());
```



## Base64ToStr Base64解码到字符串

用法：

``` csharp
    string balabala = "abcde"
    string b64 = balabala.ToBase64();

    Debug.Log(b64.Base64ToStr());
```



## IsNullOrEmpty 是否为空字符串

用法：

``` csharp
    string balabala = "abcde"
    if (balabala.IsNullOrEmpty())
    {
        //字符串为空
    }else
    {
        //字符串不为空
    }
```





## GenRandomStr 生成随机字符串

``` csharp
StringHelper.GenRandomStr(int Length)
```

参数为欲生成的字符串长度


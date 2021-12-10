# LitJson 集成

Json序列化是开发中非常经常需要用到的功能，考虑到其通用性，因此ILRuntime对LitJson这个序列化库进行了集成

## 初始化

默认情况下，框架启动时将自动注册LitJson到ILRuntime. 

如不希望框架自动注册LitJson，可在`AddILRuntime()`方法处进行如下配置：

``` csharp
IXCore core = XCore.CreateDefault()
    .AddILRuntime(options =>
    {
        options.UseLitJson = false; //设为false后不会自动注册
    });
await core.RunAsync();
```

## 使用

LitJson的使用非常简单，将一个对象转换成json字符串，只需要下面这行代码即可

``` csharp
string json = JsonMapper.ToJson(obj);
```

将json字符串反序列化成对象也同样只需要一行代码

``` csharp
JsonTestClass obj = JsonMapper.ToObject<JsonTestClass>(json);
```

?> 具体用法请参考[LitJson](https://litjson.net/ ':ignore')官方文档。
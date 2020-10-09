# TinaX配置框架

自`TinaX.Core`包版本`6.6.15`开始，提供配置框架(`Configuration`)功能。

- 位于包: `TinaX.Core` (`io.nekonya.tinax.core`)
- 包版本：`6.6.15` 及以上
- 基础命名空间 `TinaX.Systems.Configuration`

可用于依赖注入的接口或类：

|   Type    |   说明    |   备注    |
|-----------|-----------|-----------|
|`IConfiguration`| 读取配置信息的主要接口 | 在`XCore`生命周期处于`Start`及之后阶段时可用 |
|`IConfigurationBuilder` | 用于配置`Configuration`来源等用途 | **仅**在`XCore`生命周期处于`Init`阶段时可用|

?> 关于TinaX`依赖注入`功能的详细说明，请参考文档：[TinaX 依赖注入](/cmn-hans/core/manual/DependencyInjection.md)

<br>

## Microsoft.Extensions.Configuration

TinaX的配置框架在设计时，尽可能与微软的[Microsoft.Extensions.Configuration](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.extensions.configuration?view=dotnet-plat-ext-3.1)保持了一致。

后者是`Asp .Net Core`等微软第一方框架中的默认配置扩展。

如果您有使用微软`Microsoft.Extensions.Configuration`经验的话，（如使用Asp .Net Core的经验）, 基本可以无需学习直接使用TinaX配置框架。
# TinaX System

通常，我们把TinaX 中提供各个服务的模块称为系统`System`

通常每个System有如下特点：

1. 所有System都在C# `TinaX`命名空间下 和 Lua `XCore` table下有直接的入口。（UIKit特例，UIKit的C#入口在`TinaX.UIKit`命名空间下。（以后可能会调整））
2. 所有System的C#接口`xxx.Instance`都可以简写成`xxx.I`;
3. 大部份System之间是相互依赖的，如`UIKit`依赖`VFS`，除了部分强制分离开的System，如`Lua Runtime`
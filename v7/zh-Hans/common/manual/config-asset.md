# TinaX 的配置资产

TinaX框架的很多功能模块是需要进行设置的，而其中很多设置是以Unity资产(`*.asset`)的形式存放在工程中的。

## 资产存放位置

默认情况下，TinaX框架约定各个模块的配置资产存放在项目的`Assets/Resources/TinaX/Configurations`路径中，这是一个较为轻量化的状态：

- 它们位于`Resources`目录中，可以直接使用`UnityEngine.Resources`类加载它们，而无需引入`AssetBundle`、`Addressables`等概念。
- 而对于使用资产热更新方案的项目来说，它们打包在`Resources`目录下的这一份资产就是完全冗余的了.

上述这种状态适用于中小型项目，可以尽可能减少需要配置准备的工作而尽快写有用的代码。但对于中大型项目而言，放在`Resources`目录中并不是一个好的设计，中大型项目往往具备`AssetBundle`等资产管理方案，因此不需要将其放在Resources目录中。

### No Resources

在项目设置中添加编译定义符号（亦俗称C#宏）`TINAX_CONFIG_NO_RESOURCES`, 之后框架的约定资产存放位置将改为`Assets/TinaX/Configurations`.

当然，在进行了这项设置后，也就必须为项目实现一个资产管理方案了。

<br>

## 使用配置资产

虽然上面描述了配置资产的存放位置，但实际上作为使用框架的业务开发者，我们几乎是不需要知道它们在哪儿的，也不需要直接操作它们。几乎所有的框架功能配置，都可以在编辑器的`Project Settings`窗口中进行操作。

<br>

## 细节深入

关于配置资产功能的细节，请见文档[配置资产深入](/zh-Hans/core/advanced/config-asset)
# TinaX.Core

`TinaX.Core`是TinaX Framework的核心库

package name: `io.nekonya.tinax.core`

## 使用

`TinaX.Core`使用包形式提供，您可使用git地址在Unity Package Manager直接安装：

```
git://github.com/yomunsam/TinaX.Core.git
```

或使用其他方式安装本包。

### 依赖

`TinaX.Core`依赖如下包，请在安装本包前确保以下包已被安装：

- [com.neuecc.unirx](https://github.com/yomunsam/UniRx.UPM) :`git://github.com/yomunsam/UniRx.UPM.git`
- [com.cysharp.unitask](https://github.com/yomunsam/UniTask.UPM) :`git://github.com/yomunsam/UniTask.UPM.git`

## 功能

**[XCore](/cmn-hans/core/manual/XCore.md)**负责整个启动和管理整个框架，您可以查看它的[说明](/cmn-hans/core/manual/XCore.md)了解如何启动框架，或查看它的详细[API文档](/cmn-hans/core/api/IXCore.md).

除此之外，TinaX.Core包中提供了一些基础系统，如**[事件广播系统](/cmn-hans/core/manual/event.md)**.
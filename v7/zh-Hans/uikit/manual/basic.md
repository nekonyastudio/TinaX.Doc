# TinaX.UIKit 基本概念

本文将介绍一些UIKit相关的基本概念。

## UI系统

当前，Unity生态中有很多UI方案，如第一方的uGUI（Unity UI）、IMGUI、UI Toolkit, 如第三方的NGUI（Next-Gen UI kit）、FairyGUI等。

它们本身有的自称UI系统，有的自称UI框架、有的自称UI解决方案，为了避免混乱，本文档中这些事物我们都称之为`UI系统`。

## UIKit 提供者

UIKit模块是作为业务开发框架中的UI框架，对业务UI进行了一些概念定义和规范，只有一些纯逻辑层面的东西，并没有针对某个具体的`UI系统`开发功能。或者说，UIKit是一个抽象的框架，没法直接拿来用。

因此，在实际使用中，我们还需要另一个具体包含了`UI系统`相关功能的模块来为UIKit提供具体的实现，这种模块我们称之为`UIKit提供者`。

TinaX 框架中有基于 uGUI（Unity UI）开发的UIKit 提供者：[TinaX.UIKit.UGUI](/zh-Hans/uikit/ugui/README)，为开发者提供一个开箱即用的UI方案。

但在使用`TinaX.UIKit.UGUI`模块之前，请继续阅读完本文档，了解一些UIKit的通用约定概念。


## UI Page

UIKit约定的UI基本单位是Page（页）。我们对UI的绝大多数操作（加载UI、隐藏显示UI、关闭UI等）都是以页来处理的。

一个 UI Page之下，又细分以下概念：

### Page View

页视图，UIKit中定义的View是一个抽象的对象。而在具体的实现中，视图可能是不同的存在形式，如在`TinaX.UIKit.UGUI`模块中，视图就是一些GameObject（以及prefab）.

### Page Controller

页控制器，与很多App、Web框架类似，从名字可以看出，这儿是我们编写逻辑代码的地方。

一页UI的所有相关逻辑，都应该编写在页控制器中。

### MVC? MVVM? :id=mvc-or-mvvm

Page View 和 Page Controller 两者，一个显示内容，一个管理逻辑，实现了最基本的能把UI功能跑起来的结构。

但往往开发者们并不满足于此，会尝试引入一些UI的设计模式，如MVC、MVP、MVVM等等。

UIKit本身为实践UI设计模式提供了一些便利，如依赖注入、数据双向绑定等基础功能。但本身并没有明确的规范如何实践UI设计模式。开发者可以自由在业务中实践格式设计模式，当然，不同的`UIKit 提供者`中可能会有更具体的规范和功能，如`TinaX.UIKit.UGUI`模块的文档中就包含了对uGUI实践不同设计模式的进一步探讨。


## UI Group

UI组是一个实现UI栈的对象。

当我们在Unity 项目中打开了很多页UI之后，如果没有什么东西将这些UI管理起来的话，就会混乱。UI组就是这样一个存在，它将许多UI页管理起来，形成一个后进先出（LIFO）的堆栈，让UI之间有了层级关系。

若要从一页打开另一页，程序会将新页推入堆栈，并将新页变成活跃页面。

![将页面推送到导航堆栈](basic.assets/pushing.png)

若要返回到前一页，程序会从堆栈中弹出当前页面，使位于顶层的页面称为活跃页面。

![从导航堆栈中弹出页面](basic.assets/popping.png)

?> 上图来自微软 .NET Xamarin.Forms 框架的文档，虽然不是同一个东西，但原理相同。

当然，UI Group**也可以**不是一个严格的后进先出的UI堆栈，可以从中间移除某个页面。


## UI 树

UI树不是一个具体对象，而是一个概念。

UI Group自身也是一个UI Page, 因此一个UI Group本身也可以被当作是UI Page而被推入另一个UI Group, 形成了一个多叉树结构。


## UIKit Canvas

UIKit Canvas其实直接叫Canvas就挺好的，但是容易与Unity中UGUI的Canvas概念混淆，于是加了个前缀叫UIKit Canvas.

UIKit Canvas是一个UI树的根级。


## 打开UI :id=open-ui-page

“打开UI”是一个比较通俗的说法，实际上在UIKit中打开一页UI应该分为两个步骤：
1. 创建UI Page
    - 包括创建Page、Controller、View的关系结构
    - 同时也可能需要加载资产（如uGUI是基于GameObject的，所以View需要加载prefab）
2. 将创建好的UI Page推入UI Group，完成显示（`Display`）流程。

多数情况下，UIKit有提供一些方法可以简化这两个步骤。

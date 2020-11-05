# TinaX 国际化与本地化

TinaX I18N 基于key/value的形式提供国际化文本功能。接下来，我们将实现一个简单的i18n实例：

## 创建I18N字典

首先，我们需要创建key/value的文本字典。在中小型规模的项目中，我们可以直接使用Unity Asset形式来创建字典。

![image-20201105145620115](QuickStart.assets/image-20201105145620115.png)

![image-20201105145925219](QuickStart.assets/image-20201105145925219.png)

然后，我们在Unity`Inspector`界面中各设置一个key/value记录:

![image-20201105145848918](QuickStart.assets/image-20201105145848918.png)

![image-20201105150059246](QuickStart.assets/image-20201105150059246.png)

![image-20201105150115518](QuickStart.assets/image-20201105150115518.png)

<br>

## 设置地区

打开项目`Project Settings`中的`X I18N`项，我们可以设置地区：

![image-20201105150309650](QuickStart.assets/image-20201105150309650.png)

### 在地区中设置I18N字典

![image-20201105150408282](QuickStart.assets/image-20201105150408282.png)

### 默认地区

在TinaX I18N中，有一个默认地区的概念。

首先我们可以在`Project Settings`中设置一个默认地区字段，该值即为“地区配置”列表中的“地区名”项。这样一来，在游戏/应用启动时，I18N框架将默认以该地区提供国际化文本查询。

同时，I18N框架也提供了自动匹配（Auto Match）功能，当启用该功能后，在游戏/应用启动时，I18N框架将读取当前系统语言，并在地区配置列表中找到第一个绑定了对应系统语言的地区，以此作为I18N框架的默认地区。

<br>

## 地区和语言

其实我们发现，在不少其他的I18N框架中，会直接以语言作为划分国际化的标识，其实这是不准确的。比如仅以我们当前所阅读的简体中文来讲，就有“中国”、“新加坡”、“马来西亚”三个国家或地区使用。

而同样是中文，可能同一个事物在不同的地区说法是不同的，例如：“智能手机/智慧手机”、“程序/程式”。

而更甚者，同样都叫“繁体汉字（正体汉字）”，不同地区的写法可能也不一样（图源自知乎）：

![preview](https://pic1.zhimg.com/v2-d53cc64ce194b102e8a5741ec1ed591e_r.jpg?source=1940ef5c)

不仅仅是中文如此，其他语言也都有类似的问题，甚至问题更严重。

所以严谨来看，TinaX I18N框架使用地区概念来进行国际化处理，而非直接使用语言。

> 在TinaX I18N中，未避免不必要的无趣争议，仅有“地区”概念，没有“国家”概念。

<br>

## 创建UI

接下来，我们创建一个UGUI的UI页面，以便进行接下来的步骤。

?> 由于TinaX I18N 需要在TinaX框架启动完成之后才可正常运行，所以我们应该在框架启动之后再打开UI. <br> 借助`TinaX.UIKit`工具，我们可以很容易地做到。

我们创建一个空的UI页面，并在其中放置一个Text组件：

![image-20201105153345984](QuickStart.assets/image-20201105153345984.png)

<br>

## 在UGUI中使用I18N

我们选中Text组件的GameObject，点击“Add Component”，添加`Text Localized` （`TinaX->I18N->Text Localized`）组件。

![image-20201105153539754](QuickStart.assets/image-20201105153539754.png)

填写"I18N Key"字段为我们之前设置在字典里的`hello`



### 编辑器下实时预览

我们发现，在安装了TinaX I18N包之后，在编辑器顶部的右上角，多了一个图标：

![image-20201105153721710](QuickStart.assets/image-20201105153721710.png)

点击图标，在弹出的菜单中选择“Refresh Data”，之后图标右侧会多出一个下拉菜单：

![image-20201105153831904](QuickStart.assets/image-20201105153831904.png)

其中的cn表示“当前编辑器下采用的地区为cn”。

这时候，我们在刚才UI的Text Localized组件上点击"Refresh"按钮，观察到Text的值已被改变：

![image-20201105154910255](QuickStart.assets/image-20201105154910255.png)


<br>

## 编辑器运行时切换语言

通过编辑器右上角的按钮，即可在编辑器运行时实时切换语言：

![switchReg](QuickStart.assets/switchReg.gif)


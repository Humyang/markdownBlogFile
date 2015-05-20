layout: [post]
title: "iOS 笔记 《View Controller Programming Guide for iOS：Introduction》"
date: 2015-05-14 08:34:26
tags: 
- iOS
categories: 
- iOS 开发
- 翻译
id: "VCP1"

---

记录关于学习过的 iOS 文档

<!-- more -->

[原文地址](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007457-CH1-SW1)

---

# 视图控制器基础
运行在 iOS 设备的应用程序显示内容时会受到屏幕空间量的限制因此必须有创造性的为用户呈现信息。有大量信息需要显示的应用程序因此只能显示开始的一部分，然后隐藏的额外内容在用户与应用程序交互时显示。视图控制器对象提供基础设施管理内容和协调内容的显示和隐藏。不同的视图控制类控制用户界面分离的不同部分，由你来拆分用户界面的实现过程为更小的更易管理的单元。

在你可以在应用程序使用视图控制器之前，你需要对主要的类在 iOS 应用程序用来显示什么内容有基本的了解，包括窗口和视图。任何视图控制器的实现过程的关键部分是管理使用视图显示它的内容。不管怎样，管理视图不仅仅对视图控制器工作。大多数视图控制器在需要过渡时会跟其它视图控制器通信和协调。因为视图控制器管理着许多连接，无论是向内看的视图和关联的对象还是向外看的其它控制器，弄清楚对象之间的连接有时候是很困难的。作为替代，使用界面构造器创建 storyboards。Storyboards 轻松的使你的应用程序之间的关系可视化和大大简化在运行时初始化对象需要的功夫。


## 屏幕，窗口，和视图创建视觉界面

图 1-1 展示了一个简单的界面。在左边，你可以见到组成这个界面的对象和明白它们如何连接彼此。

**图 1-1**窗口和它的目标屏幕和内容视图

![](./window_view_screen_2x.png)

这里工作着三个主要对象：

* [UIScreen](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIScreen_Class/index.html#//apple_ref/occ/cl/UIScreen) 对象标识连接到设备的物理屏幕.
* [UIWindow](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIWindow_Class/index.html#//apple_ref/occ/cl/UIWindow) 对象提供对屏幕绘制的支持。
* [UIView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) 对象的集合执行绘制操作。这些对象与窗口绑定和当窗口询问它们时绘制内容。

图 1-2 展示这些类 (和关联的重要的类) 如何在 UIKit 中定义

**图 1-2** 视图系统中的类

![](./class_representation_2x.png)

尽管你不需要弄清楚与视图有关的所有事情来弄清楚视图控制器，但它可以帮助你研究视图的大部分显著特征：

* 视图代表用户界面元素。每一个视图覆盖指定区域。在该区域内，它负责显示内容或响应用户事件。
* 视图可以嵌套入视图层次结构。子视图相对它的父视图定位和绘制。因此，当父视图移除时，它的子视图跟着移除。视图层次结构通过把它们放入一个共用的父视图中轻松的组合一组相关的视图。
* 视图可以动画它们的属性值。当以动画形式更改一个属性值时，这个值会在已定义的时间周期内逐渐变化直到到达新值。可以在单个视图动画协调更高跨越多个视图的多个属性。<br /> 动画效果对于 iOS 应用程序开发是非常重要的。因为大部分应用程序一次只能显示一部分内容，动画效果允许用户见到过渡的发生和新值的变化。一个瞬间的过渡可能会使用户迷惑。
* 视图很少明白它们在你的应用程序扮演的角色的作用。例如，图 1-1 展示一个按钮 (标题 Hello)，它是特殊种类的视图，称为 *control*。Controls 知道如何响应这个区域内的用户交互，但他们不知道它们该控制什么。作为解决，当用户与 control 交互时，它发送消息到你的应用程序的其他对象中。这种做法可以让单个类 ([UIButton](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIButton_Class/index.html#//apple_ref/occ/cl/UIButton)) 为多个按钮提供实现过程，每个配置发出不同的动作。

一个复杂的应用程序需要许多视图，通常把它们组合到视图层次结构。它需要动画这些视图的子级在屏幕出现或离开以提供一种单个巨大的屏幕的感觉。最后，保持视图类的可重用性，视图类需要知道它们在应用程序中执行的具体作用。因此应用程序的逻辑－大脑－需要放置在其他位置。你的视图控制器是统领你的应用程序的视图的大脑。

## 视图控制器管理视图

每一个视图控制器都组织和控制一个视图；这个视图通常是视图层次结构的根视图。视图控制器在 MVC 模式中是控制器对象，但视图控制器也有 iOS 期望它执行的特定任务。所有视图控制器都继承自定义了这些任务的 [UIViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController) 类。所有视图控制器都会执行视图和资源管理任务；其它职责基于如何使用视图控制器决定。

图 1-3 展示的界面来自 图 1-1，但更新为使用视图控制器。你永远不会分配视图到窗口。相反，你会分配视图控制器到窗口，并且视图控制器自动添加它的视图到窗口。

**图 1-3** 视图控制器绑定到窗口并自动添加它的视图作为窗口的子视图

![](./window_view_controller_screen_2x.png)

视图控制器仔细的加载它的视图只有视图被需要时才会加载。它也可以在某些条件下视图视图。根据这些原因，视图控制器在管理你的应用程序的资源扮演重要部分。

视图控制器是用来协调已连接的视图的动作的天然的地方。当一个按钮按下后，它发送消息到视图控制器。尽管视图它自己不知道它所执行的任务，但视图控制器清楚什么按钮被按下和应该做出怎样的响应。控制器可以更新数据对象，动画或更改保存在视图的属性，或者甚至显示其它视图控制器内容到屏幕上。

通常，每一个被你的应用程序实例化的视图控制器只能见到你的应用程序的数据的子集。它知道如何显示这个特定数据的集合，不需要知道关于其他类型的数据。因此，应用程序的数据模型，用户界面设计，和你创建的所有视图控制器彼此之间互相影响。

图 1-4 展示一个应用程序管理食谱的例子。这个应用程序显示三个相关联但不一样的视图。第一个视图列出应用程序管理的食谱。触击食谱进入第二个视图，它详细描述这个食谱。在详细视图触击食谱的图片显示第三个视图，一个巨大的图片的版本。每一个视图通过不同的视图控制器对象管理它们的工作是呈现相关的内容，填充子视图的数据，和响应用户在视图层次结构内的交互。

![](./recipe_views_2x.png)

这个例子演示了视图控制器的几个共同因素：

* 每个视图只被一个视图控制器控制。当视图被分配到视图控制器的 [viwe](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/view) 属性后，视图控制器就有用了它。如果视图是一个子视图，它可能被同样的视图控制器或不同的视图控制器控制。当你在学习容器视图控制器时你将会学习到更多关于如何使用多个视图控制器组织单个视图层次结构。
* 每个视图控制器与你的应用程序的数据的子集交互。例如，图像控制器只需要知道什么图像需要显示。
* 因为每个视图控制器只提供用户体验的一个子集，视图控制器彼此之间必须进行通信使用户顺畅体验。它们也可能与其他控制器通信，例如数据控制器或文档对象。

## 视图控制器的分类

图 1-5 展示在 UIKit 框架中可用的视图控制器类以及其它对视图控制器重要的类。例如，UITabBarController 对象管理 UITabBar 对象，它实际显示标签相关连的和标签栏的界面。其它框架定义的额外的视图控制器类没在图中显示。

**图 1-5** UIKit 中的视图控制器类

![](./view_controller_classes_in_uikit_2x.png)

视图控制器，包括 iOS 提供的和你定义的那些，可以分成两大类－内容视图控制器和容器视图控制器－这反映了视图控制器在应用程序中扮演的功能。

### 内容视图控制器显示内容
*内容视图控制器* 使用视图或一组视图组织的视图层次结构在屏幕上呈现内容。有描述到这一点的控制器就是内容视图控制器。内容控制器通常会知道关于它在应用程序中扮演的功能的应用程序数据的子集。

这里是应用程序使用内容视图控制器的常用例子：

* 对用户显示数据
* 从用户收集数据
* 执行指定任务
* 对可用的命令或选项集之间导航，例如在游戏的启动画面

内容视图控制器使应用程序的主要协调对象因为它们知道提供给用户的指定的数据和任务的细节。每个你创建的视图控制器对象负责管理单个视图层次结构内所有的视图。视图控制器和视图层次结构中的视图之间一对一的对应是关键的设计考虑。你不应该使用多个内容视图控制器管理同样的视图层次结构。同样，你不应该使用单个内容视图控制器对象管理多个屏幕的内容。

关于定义你的内容视图控制器和实现需要的行为，见 [Creating Custom Content View Controllers](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/BasicViewControllers/BasicViewControllers.html#//apple_ref/doc/uid/TP40007457-CH101-SW1)。

### 关于表格视图控制器
许多应用程序显示表格式的数据。因为这个原因，iOS 提供已定义的内置 UIViewController 类的子类特别用来管理表格式数据，这个类是 [UITableViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/occ/cl/UITableViewController)，管理表格视图和添加许多标准的表格相关的行为例如选择管理，行编辑，和表格配置的支持。这些额外的支持最大限度减少你必须写的创建和初始化基于表格的界面的代码量。你也可以在 UITableViewController 的子类添加其他自定义行为。

图 1-6 展示一个使用表格视图控制器的例子。因为它是 [UIViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController) 类的子类，表格视图控制器始终有一个指向界面的根视图的指针 (通过他的 [view](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instp/UIViewController/view) 属性) 但它也有一个单独的指针指向在界面显示的表格视图。

**图 1-6** 管理表格式数据

![](./managing_a_tabular_data_2x.png)

更多信息关于表格视图，见 [*Table View Programming Guide for iOS*](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/TableView_iPhone/AboutTableViewsiPhone/AboutTableViewsiPhone.html#//apple_ref/doc/uid/TP40007451)。

### 容器视图控制器布置其他控制器的内容
容器视图控制器包含其他视图控制器拥有的内容。这些其它视图控制器明确的分配给容器视图控制器作为它的子集。容器视图控制器可以呈现其他控制器和其它控制器的子集。最终，这个控制器的组合建立视图控制器的层次结构。

每种类型的容器视图控制器成立的用户界面是它的子集在操作。不同类型的容器视图控制器之间的用户界面的视觉呈现和它的子集的设计有很大的不同。例如，这里有一些区分不同容器视图控制器的方式：

* 容器提供它自己的 API 管理它的子集。
* 容器决定它的子集之间是否有关系和是什么关系。
* 容器管理视图层次结构就像其它视图控制器的做法。容器也可以添加任意它的子集的视图到它的视图层次结构。容器决定这样的视图的添加时间和如何调整它的尺寸适应容器的视图层次结构，但其它的子视图控制器仍然负责视图和它的子视图。
* 容器可能加强在它的子集上具体设计考虑。例如，容器可能限制它的子集为某些视图控制器类，或它可能期待这些控制器提供一些额外的内容用作配置容器的视图。

这些内置的容器类每一个都有组织的围绕着一个重要的用户界面原则。你使用这些通过容器管理的用户界面组织复杂的应用程序。


### 关于导航控制器
*导航控制器*呈现有组织的层级结构的数据并且它是 [UINavigationComtroller]() 类的一个实例。这个类的方法提供对管理基于栈的内容视图控制器集合的支持。栈代表用户通过分级数据获取的路径，栈的底部代表起始点而顶部代表数据中用户的当前位置。图 1-7 展示的屏幕来自一个联系人应用程序，它使用导航控制器呈现为用户呈现联系人信息。每个页面顶部的导航栏是属于导航控制器。每个屏幕的其它部分对用户显示通过内容视图控制管理的特定的数据分层级别的信息。用户在界面与控件交互时，这些控件告诉导航控制器显示序列中的下一个视图控制器或退出当前视图控制器。

**图 1-7** 导航分级数据

![](./navigation_interface_2x.png)


尽管导航控制器的主要工作是管理它的子视图控制器，但它也管理少数视图。具体来说，它管理导航栏 (显示用户当前在数据层次结构的位置信息)，按钮 (导航返回之前的屏幕)，和任何当前视图需要的自定义控件。你不要直接修改视图控制器拥有的视图。而应该配置导航控制器的控件通过已设置的属性在每一个子视图控制器上显示。

关于如何配置和使用导航控制器对象的信息，见 [Navigation Controllers](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/NavigationControllers.html#//apple_ref/doc/uid/TP40011313-CH2)。

### 关于标签栏控制器
*标签栏控制器*是划分你的应用程序为两个或更多不同的操作模式的容器视图控制器。标签栏控制器是 [UITabBarController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITabBarController_Class/index.html#//apple_ref/occ/cl/UITabBarController) 类的一个实例。标签栏有多个标签，每一个都代表子视图控制器。选择标签后会使标签栏控制器在屏幕上显示相关联的视图控制器的视图。

图 1-8 展示几个时钟应用程序的模式与相应的视图控制器之间的关系。每个模式都有内容视图控制器管理主要内容区域。在这时钟应用程序的案例中，Clock 和 Alarm 视图控制器都显示了导航样式的界面在屏幕的顶部容纳额外的控件。其它模式使用内容视图控制器在单个屏幕呈现。

**图 1-8**不同的时钟应用程序模式。

![](./tabbar_interface_2x.png)

你可以使用标签栏控制器显示不同类型的数据或以不同的方式呈现相同的数据。

更多信息关于如何配置和使用标签栏控制器，见 [Tab Bar Controllers](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/TabBarControllers.html#//apple_ref/doc/uid/TP40011313-CH3)。

### 关于分割视图控制器
*分割视图控制器*将屏幕划分为多个部分，每个部分可以分别的更新。分割视图控制器的外观非常依赖它的方向。分割视图控制器是 [UISplitViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISplitViewController_class/index.html#//apple_ref/occ/cl/UISplitViewController) 类的一个实例。分割视图控制器的内容来自两个子视图控制器。

图 1-9 展示 [MultipleDetailViews](https://developer.apple.com/library/ios/samplecode/MultipleDetailViews/Introduction/Intro.html#//apple_ref/doc/uid/DTS40009775) 简单应用程序的分割视图界面。在纵向模式，只显示详细视图。列表视图是可弹出的部分。在横向模式显示时，分割视图控制器显示侧边和侧边子集的内容。

**图 1-9** 在横向和纵向模式中的主－次界面

![](./SplitView_view_2x.png)

分割视图控制器只对 iPad 支持并且是设计来帮助你利用大屏幕设备的优点。这是在 iPad 应用程序中实现主次界面的完美方式。

关于如何配置和使用分割视图控制器，见 [Popovers](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/Popovers.html#//apple_ref/doc/uid/TP40011313-CH5)。

### 关于弹出控制器
再看一次图 1-9.当分割视图控制器在纵向模式显示时，主视图在特定的控件中显示，它称为 *popover*。在 iPad 应用程序中，你可以使用弹出控制器 ([UIPopoverController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPopoverController_class/index.html#//apple_ref/occ/cl/UIPopoverController)) 实现在你的应用程序中的弹出功能。

弹出控制器实际上不是一个容器；它没有从 [UIViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController) 继承。但在实践中，弹出控制器是与容器相同的，因此你在使用它们时应该应用同样的编程守则。

关于如何配置和使用弹出控制器，见 [Popovers](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/Popovers.html#//apple_ref/doc/uid/TP40011313-CH5)。

### 关于分页视图控制器
*分叶视图控制器*是一个容器视图控制器用作实现分页布局。这个布局让用户想翻书一样浏览不同页面的内容。分页视图控制器是 [UIPageViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPageViewControllerClassReferenceClassRef/index.html#//apple_ref/occ/cl/UIPageViewController) 类的一个实例。每个页面的内容都由内容视图控制器提供。
分页视图控制器管理页面之间的过渡效果。当需要新的页面时，分页视图控制器调用已关联的数据源为下一个页面获取视图控制器。

关于如何配置和使用分页视图控制器，见 [Page View Controllers](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/PageViewControllers.html#//apple_ref/doc/uid/TP40011313-CH4)。

## 视图控制器的内容能以许多方式显示
要使视图控制器的内容对用户可见，它必须与窗口关联，下面有许多种你可以在你的应用程序操作的方式：

* 使视图控制器称为窗口的根视图控制器。
* 使视图控制器成为容器的子集。
* 在弹出控件显示视图控制器。
* 在另一个视图控制器呈现它。

图 1-10 展示一个联系人应用程序的例子。当用户按下添加按钮添加新的联系人时，联系人视图控制器呈现新的联系人视图控制器。新的联系人屏幕一直保持可见直到用户取消操作或提供足够多的关于联系人的信息然后将它保存到联系人数据库。信息会发送给联系人视图控制器，然后撤销被呈现的控制器。

**图 1-10**呈现的视图控制器
![](./presenting_a_modal_view_controller_2x.png)

被呈现的视图控制器不是特殊类型的视图控制器－被呈现的视图控制器可以是内容或容器视图控制器与绑定的内容视图控制器。在实际操作中，内容视图控制器特别设计为通过其他控制器呈现，因此可以认为它是内容视图控制器的变种。尽管容器视图控制器在它管理的视图控制器之间定义了特殊的关系，使用呈现允许你在将要被呈现的视图控制器和将要呈现一个视图控制器之间定义它们的关系。

大多数时间，呈现视图控制器是为了从用户收集信息或因为某些目的而吸引用户注意力。一旦目的达成，呈现中的视图控制器会销毁被呈现的视图控制器并返回标准应用程序界面。

值得注意的是被呈现的视图控制可以自己呈现其它视图控制器。当你需要按顺序执行几个模态动作时这个链接视图控制器的能力很有用。例如，在图 1-10 中如果用户触击新联系人屏幕中的添加图片按钮并想选择一个已有的图片，新联系人控制器会呈现一个图片选取界面。用户必须分别退出图片选取屏幕然后退出新联系人屏幕才能返回联系人的列表。

在呈现视图控制器时，由一个视图控制器决定使用多少屏幕空间呈现视图控制器。这一部分的屏幕空间默认被称作 *presentation context*。

更多关于如何在你的应用程序中呈现视图控制器的信息，见 [Presenting View Controllers from Other View Controllers](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ModalViewControllers/ModalViewControllers.html#//apple_ref/doc/uid/TP40007457-CH111-SW1)。

## 视图控制器之间协同创建应用程序的界面
视图控制器管理它们的视图和其它关联的对象，但视图控制器之间也彼此协同工作提供无缝连接的用户界面。你的应用程序的视图控制器之间的工作的安排和通信是使它们工作的必要部分。因为这些关系对于构造一个复杂的应用程序是非常重要的，接下来的章节预览已经讨论过的关系和对它们更进一步的详细描述。

### Parent-Child Relationships 代表 Containment
视图控制器层次结构以单个父辈开始，窗口的根视图控制器。如果该视图控制器是一个容器，它可能有提供内容的子集。这些控制器反过来说，也可以是一个容器与它们自己的子集。图 1-11 展示一个视图控制器层次结构的例子。根视图控制器是一个标签视图控制器与四个标签。第一个标签使用导航控制器它有自己的子集而其它三个标签通过内容视图控制器管理和没有自己的子集。

**图 1-11** 父－子关系
![](./parent-child_view_controller_relationships_2x.png)

每个视图控制器的区域由它的父辈决定如何填充。根视图控制器的区域由窗口决定。在图 1-11中，标签视图控制器从窗口获取它的尺寸。它为标签栏保留空间和剩余的空间给予它的子集。如果导航控制器是当前显示的控件，它会为导航栏保留空间并把剩余部分交给内容控制器。在每一步中，子视图控制器的视图是通过它的父辈调整尺寸并放入父辈的视图层次结构。

视图和视图控制器的组合也在为你处理应用程序事件的响应者队列中成立。

### Sibling Relationships 代表容器内的 Peers 
这种类型的容器定义的关系(如果存在)是通过共享它的子集。例如，比较标签视图控制器和导航控制器。

* 在标签视图控制器中，标签代表不同内容的屏幕；标签栏控制器没有定义它的子集之间的关系，尽管你的应用程序可以选择这样做。
* 在导航控制器中，同级显示分配在堆中的相关的视图。同级的通常会与其它同级共享连接。

图 1-12 展示与导航控制器关联的视图控制器常用配置。第一个子集 master，显示可用的内容但没有显示所有的细节。当其中一个项被选中后，它把新的同级放入导航控制器使用户可以见到额外的细节。同样，如果用户需要更多细节，这个同级可以放入其它视图控制器对用户展示最详细的内容。当同级如这个例子中定义了明确的关系，它们彼此之间会常常配合，直接或通过容器控制器。

**图 1-12**在导航控制之间的同级关系。

![](./sibling_controller_relationships_2x.png)


### Presentation 代表短暂显示其它界面
当视图控制器想执行一些任务时会呈现其它视图控制器。由正在呈现的视图控制器掌控这个行为。它会配置被呈现的视图控制器，从那接收信息，最后会退出被呈现的视图控制器。不管怎样，当它开始呈现后，被呈现的视图控制器是临时添加到窗口的视图层次结构的。

在图 1-13，内容视图控制器关联到标签视图中呈现一个视图控制器执行任务。Content 控制器是正在呈现的视图控制器，而 Modal 视图控制器是被呈现的视图控制器。

**图 1-13** 通过内容视图模态呈现一个视图控制器
![](./presenter-presented_view_controller_relationships_2x.png)

当视图控制器被呈现后，它所覆盖的屏幕的那一部分在另一个视图控制器提供的呈现上下文 (presentation content) 中定义。提供呈现上下文的视图控制器不需要与将要被呈现的视图控制器相同。图 1-14 展示与在图 1-13 呈现的相同的视图层次结构。你可以见到内容视图呈现了一个视图控制器，但没有提供呈现上下文。不仅如此，视图控制器还通过标签控制器呈现。因为这个原因，尽管呈现中的视图控制器只覆盖标签控制器提供的屏幕的一部分，但被呈现的视图控制器也可以使用属于标签控制器的完整区域。
//注意要突出是由跟视图控制器执行
**图 1-14** 实际呈现由根视图控制器执行。

![](./presentation_context_relationships_2x.png)

### 控制流代表内容控制器之间的协调
在一个有多个视图控制器的应用程序中，视图控制器总是在应用程序的整个生命周期中创建和销毁。在它们的生命周期期间，视图控制器彼此互相通信为用户呈现无缝的用户体验。这些关系代表你的应用程序的控制流。最常见的，控制流发生在当新的视图控制器实例化时。通常，视图控制器会因为其它视图控制器的动作而实例化。对于第一个视图控制器，称为源视图控制器 (*source view controller*) 指挥第二个视图控制器，目标视图控制器 (*destination view controller*)。如果目标视图控制器需要对用户呈现数据，数据通畅由源视图控制器提供。同样，如果源视图控制器需要从目标视图控制器获取信息，它负责建立两个视图控制器之间的连接。

图 1-15 展示这些关系最常用的例子。

在图中：

* 导航控制器的子集推送其它子集到导航堆中。
* 视图控制器呈现其他视图控制器。
* 视图控制器在弹出视图中显示其它视图控制器。

**图 1-15** 源视图控制器和目标视图控制器之间的通信

![](./data_flow_relationships_2x.png)

每一个视图控制器都由一个呈现它的控制器配置。当多个控制器一起工作时，它们在整个应用程序中建立通信队列。

在这个队列中的每一个链接的控制流是由目标视图控制器定义。源视图控制器使用目标视图控制器提供的约定。

* 目标视图控制器提供用作配置它的数据和呈现的属性。
* 如果目标视图控制器需要与在队列中前面的视图控制器通信，可以使用委托。当源视图控制器配置目标视图控制器的其它属性时，它可以要求提供一个实现委托协议的对象。

使用控制流公约的好处是每一对源视图控制器和目标视图控制器之间分工明确。当源视图控制器要求目标视图控制器执行任务时数据流向下传递；由源视图控制器驱动处理。例如，它可以提供目标控制器应该显示的特定数据对象。在其他方向，当视图控制器需要传输信息返回给催生它的源视图控制器时数据流向上传输。例如，当任务完成时它可以传输信息。

而且，通过坚持实现这个控制了模型，可以确保目标视图控制器不会知道过多关于配置它们的源视图控制器。当它知道在队列中前面的有关的视图控制器时，它只知道类实现的委托协议，不知道该类的类。通过保持不知道过多视图控制器彼此之间，个体控制器可重用性变得更高。其他人阅读你的代码时，一致实现的控制流模型使得清晰的看到任意控制器对之间的通信路径。

## Storyboards 帮助你设计用户界面

当你在使用 storyboards 实现你的应用程序时，使用界面构造器组织你的应用程序的视图控制器和任何相关联视图。图 1-16展示一个从界面构造器的界面布局例子。可视化的界面构造器让你的应用程序流程一目了然。你可以见到应用程序实例化了什么视图控制器和实例化的顺序。不仅如此，你还可以在 storyboards 配置复杂的视图集合和其它对象。最终 storyboards 会以文件形式保存在你的项目中。当你编译项目时，在你的项目中的 storyboards 会被处理并复制到应用程序包，由你的应用程序在运行时加载。

**图 1-16** 界面构造器中的 storyboard 图表。

![](./after_modal_segue_2x.png)

通常，iOS 可以在需要时自动实例化在你的 storyboards 中的视图控制器。同样，每个视图控制器相关联的视图层次机构当它们需要显示时也会自动加载。视图控制器和视图都会实例化为与你在界面构造器所配置的相同属性。因为大多数的行为都会自动的，它大大的简化在你的应用程序中使用视图控制器所需要的工作。

创建 storyboards 的完整细节在 [*Xcode Overview*](https://developer.apple.com/library/ios/documentation/ToolsLanguages/Conceptual/Xcode_Overview/index.html#//apple_ref/doc/uid/TP40010215) 中描述。现在，你需要知道一些在应用程序中实现 storyboards 时用到的必要技术。

场景 (*scene*) 代表在屏幕上通过视图控制器管理的区域。你可以把场景认为是视图控制器和它关联的视图层次结构。

你可以在同一个 storyboards 的场景之间建立**关系**。关系在 storyboards 直观表现为一个场景到另一个场景的连接箭头。在你使两个对象之间连接时界面构造器通通常自动推断新的关系的细节。存在两种重要的关系类型。

* **包含**代表两个场景之间的父子关系。视图控制器被包含在其它控制器中当它的父控制器被实例化后它也会被实例化。例如，从导航控制器到其它场景的第一个连接定义了第一个视图控制器被放入导航堆栈。当导航控制器被实例化后这个控制器会自动实例化。<br /> 在 storyboards 中使用包含关系的一个优点是界面构造器可以调整它的子视图控制器的外观来反映其存在祖先。
* *segue*代表一个场景到另一个的视觉过渡。在运行时，segues 可以被各种动作触发。当 segue 被触发时，它可以使新视图控制器被实例化和在屏幕上过渡。<br />尽管 segue 总是从一个视图控制器到另一个，但有时候第三个对象可以参与到处理中。这个对象实际上触发 segue。例如，如果你使源视图控制器的视图层次机构中的按钮连接到目标视图控制器，当用户触击按钮时，segue 会触发。当 segue 是直接从源视图控制器到目标视图控制器时，它通常代表你打算以编程方式出发 segue。<br />不同类型的 segues 提供两个不同的视图控制器之间所需要的常用过渡效果：
	* *push segue* 把目标视图控制器放入导航控制器的堆栈。
	* *modal segue* 呈现目标视图控制器。
	* *popover segue* 以弹出方式显示目标视图控制器。
	* *custom segue* 允许你设计属于你的过渡效果显示目标视图控制器。

	segue 共享了常用的编程模型。在这个模型中，目标控制器是通过 iOS 自动实例化然后源视图控制器被调用来配置它。这个行为适配的控制流模式在 [Control Flow Represents Overall Coordination Betweent Content Controllers](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/AboutViewControllers/AboutViewControllers.html#//apple_ref/doc/uid/TP40007457-CH112-SW27) 中描述。
	
你也可以创建视图控制器和保存在同一个场景内的对象之间的连接。outlets 和 action 让你仔细的定义视图控制器和与它关联的对象之间的关系。连接通常不能在 storyboards 见到它自己但可以在界面构造器的连接

---

系列文章

[*iOS 笔记 《View Controller Programming Guide for iOS：Introduction》*](../VCP0)

*iOS 笔记 《View Controller Programming Guide for iOS：View Controller Basics》*

[*iOS 笔记 《View Controller Programming Guide for iOS：Using View Controllers in Your App》*](../VCP2) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Creating Custom Content View Controllers》*](../VCP3) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Resource Management in View Controllers》*](../VCP4) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Responding to Display-Related Notifications》*](../VCP5) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Resizing the View Controller's Views》*](../VCP6) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Using View Controllers in the Responder Chain》*](../VCP7) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Supporting Multiple Interface Orientations》*](../VCP8) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Accessibility from the View Controllers's Perspective》*](../VCP9) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Presenting View Controllers from Other View Controllers》*](../VCP10) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Coordinating Efforts Between View Controllers》*](../VCP11) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Enabling Edit Mode in a View Controller》*](../VCP12) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Creating Custom Segues》*](../VCP13) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Creating Custom Container View Controllers》*](../VCP14)




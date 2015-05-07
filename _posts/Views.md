layout: [post]
title: "iOS 笔记 《View Programming Guide for iOS：Views》"
date: 2015-05-04 16:04:32
tags: 
- iOS
categories: 
- iOS 开发
- 翻译
id: "iOS-Note-View-Programming-Guide-for-iOS-Views"
---

记录关于学习过的 iOS 文档

<!-- more -->

[原文地址](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009503-CH1-SW2)



---

#视图
因为视图对象是应用程序与用户交互的主要方式，它负责很多职责。下面仅是少数方面：

- 布局和子视图管理
	- 视图定义了相对于它的父视图的默认缩放行为。
	- 视图可以管理子视图的列表。
	- 视图可以重写子视图的尺寸和位置。
	- 视图可以把自己坐标系统的点转换成其他视图或窗口的坐标系统的点。
- 绘制和动画效果
	- 视图可以在它的矩形区域绘制内容。
	- 一些视图属性可以设置新的动画值。
- 事件处理
	- 视图可以接收触摸事件。
	- 视图参与到响应链中。

这个章节的焦点在创建，管理，和绘制视图的步骤和视图层次的管理和处理布局。关于如何处理触摸事件 (和其他事件) 的信息，见 [*Event Handling Guide for iOS*](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009541)。

##创建和配置视图对象
你可以使用编程方式活界面构造器创建独立的视图，然后组合到视图层次中使用。

###使用界面构造器创建视图对象
最简单的创建视图的方式是使用界面构造图形化组装它们。使用了界面构造器，你可以添加视图到你的界面，布置这些视图到层次中，配置每一个视图的设置，和为你的代码连接视图相关的行为。因为界面构造器使用实时视图对象－意思是，真实的视图的实例－你在设计时看到的就是你在运行时看到的。你可以随后将这些实时对象保存在 nib file，它是保留你的对象的状态和配置的资源文件。

通常创建 nib file 是为了应用程序的视图控制器的其中一个存储完整的视图层次。最高级别的 nib file 通常包含代表你的视图控制器的视图的单独视图对象。(视图控制器它自己通常代表文件拥有者对象。) 最高级别的视图尺寸应该与目标设备适应并且包含所有其他将要呈现的视图。只保存你的视图控制器的视图层次的一部分是很罕见的 nib file 用法。

当使用视图控制器与 nib file 时，所有你需要做的是在初始化视图控制器与 nib file 信息。视图控制器处理相应时间的加载视图和卸载视图。如果你的 nib file 没有关联视图控制器，你可以手动使用 NSBundle 或 UINib 对象加载 nib file 内容，它会在 nib file 中使用数据重组你的视图对象。

更多关于如何使用界面构造器创建和配置你的视图的信息，见 *Interface Builder User Guide*。关于视图控制器如何加载和管理他们关联的 nib files，见 [*View Controller Programming Guide for iOS*](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007457) 中的 [Creating Custom Content View Controllers](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/BasicViewControllers/BasicViewControllers.html#//apple_ref/doc/uid/TP40007457-CH101)。

更多关于如何从 nib file 以编程的方式加载视图的信息，见 [*Resource Programming Guide*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/LoadingResources/Introduction/Introduction.html#//apple_ref/doc/uid/10000051i) 中的 [Nib Files](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/LoadingResources/CocoaNibs/CocoaNibs.html#//apple_ref/doc/uid/10000051i-CH4)。

###编程方式创建视图对象
如果你准备以编程方式创建视图，你可以使用标准的 allocation/initialization 对。默认初始化视图的方法是 [initWithFrame: ](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instm/UIView/initWithFrame:)，它为视图设置 (将要建立的视图) 相对父视图的初始化尺寸和位置。例如，创建一个新的通用 UIView 对象，你可以使用类似下面的代码：

```
CGRect  viewRect = CGRectMake(0, 0, 100, 100);
UIView* myView = [[UIView alloc] initWithFrame:viewRect];
```

> 注意：尽管所有视图都支持 initWithFrame: 方法，但一些首选的 initialization 方法你应该替代 initWithFrame: 方法使用。关于自定义的初始化方法的具体信息，见该类的引用文档。

在你创建视图之后，你必须把他添加到窗口 (或窗口的其他视图中) 它才可以变为可见的。如何添加视图到你的视图层次中，见 [Adding and Removing Subviews](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/CreatingViews/CreatingViews.html#//apple_ref/doc/uid/TP40009503-CH5-SW11)。

###设置视图的属性
UIView 类声明了几个属性控制视图的外观和行为。这些属性操作了视图的尺寸和位置，视图的透明度，背景颜色，和它的渲染行为。这些属性都有相应的默认值你可以按需要更改。你也可以在界面构造器的 Inspector 窗口配置许多的这些属性。

表 3－1 列出大多数常用的属性 (和一些方法) 和它们的使用方法。有关联的属性列在一起因此你可以见到影响视图某些方面的选项。

**表 3-1 ** 某些关键视图属性的用法

Properties | Usage 
------------ | ------------- 
[alpha]()，[hidden]()，[opaque]() | 这些属性影响到属性的不透明度，alpha 和 hidden 属性直接更改视图的不透明的。<br />opaque 属性告诉系统它应该如何合成你的视图。如果你的视图的内容是完全不透明的并且因此不需要在它底部显示任何其他视图的内容，那么设置这个属性为 YES。设置为 YES 之后消除不需要的合成操作从而提升性能。
bounds,frame,center,transform | 这些属性影响视图的尺寸和位置。center 和 frame 属性代表视图相对父视图的位置。frame 也包括视图的尺寸。bounds 属性定义视图在它的坐标系统中的可见内容区域。<br/>transform 属性用复杂的方式动画或移动整个视图。例如，你可以使用 transform 旋转或伸展视图。如果当前的转换不是恒等转换，那么 frame 属性值为为定义并且会忽略该值。<br/>关于 bounds，frame，和 center 属性之间的关系，见 [The Relationship of the Frame,Bounds,and Center Propertires](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW6)。关于转换如何影响视图的信息，见 [Coordinate System Transformations](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW7)。  
autoresizingMask，autoresizeSubviews | 这些属性影响视图和它的子视图的自动缩放行为。autoresizingMask 属性控制视图的父视图边界发生变化时视图应该如何响应。autoresizeSubviews 属性控制当前视图的所有子视图是否同时缩放。  
contentMode,contentStretch,contentScaleFactor | 这些属性影响视图内的内容的渲染行为。contentMode 和 contentStretch 属性决定在视图的宽度或高度变更时应该如何处理它的内容。contentScaleFactor 属性只在视图需要在高分辨率屏幕下自定义绘制行为时使用。<br /> 更多关于内容模式如何影响视图的信息，见 [Content Modes](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW2)。关于内容伸展区域如何影响你的视图，见 [Stretchable Views](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW13)。关于如何处理伸缩因素，见 [*Drawing and Printing Guide for iOS*](https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010156)  中的 [Supporting High-Resolution Screens In Views](https://developer.apple.com/library/ios/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/SupportingHiResScreensInViews/SupportingHiResScreensInViews.html#//apple_ref/doc/uid/TP40010156-CH15)  
gestureRecognizers，userInteractionEnabled，multipleTouchEnabled，exclusiveTouch | 这些属性影响你的视图如何处理触摸事件。gestureRecognizers 属性包含视图被绑定的手势检测。其他属性控制相应的视图事件。<br />关于如何相应视图的事件，见 [*Event Handling Guide for iOS*](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009541)  
backgroundColor，subviews，drawRect: 方法，layer，(layerClass 方法) | 这些属性和方法帮助你管理你的视图实际的内容。在一个简单的视图中，你可以设置背景颜色和添加一个或多个子视图。subview 属性包含了只读的子视图列表，但它提供了几个添加和移除子视图的方法。对于视图的自定义绘制行为，你必须重写 drawRect: 方法。<br />需要更多先进的内容，你可以直接在核心动画层工作。要为视图指定一个完全不同的层类型，你必须重写 layerClass 方法。 

关于视图所有的通用基础属性，见 [*UIView Class Reference*](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/doc/uid/TP40006816)。更多关于视图的特定属性，见视图的引用文档。

###为视图将来的使用而添加标签
UIView 类包含标签 (tag) 属性，你可以用整型值来标记单个视图对象。你可以使用标签独特标记一个你的视图层次中的视图对象并且在运行时搜索这些视图。(基于标签搜索的速度快过遍历视图层次。) 标签属性的默认值是 0。

要搜索已标签的内容，使用 UIView 的 viewWithTag: 方法。这个方法对接收的参数和它的子视图执行深度优先搜索。它不搜索父视图和视图层次的其他部分。因此，从视图层次的根视图搜索会搜索全部视图而指定特定的视图则会搜索该视图的子集。

##创建和管理视图层次结构
管理视图的层次结构是开发应用程序的用户界面的重要部分。视图的组织方式影响到应用程序的外观和影响到应用程序对发生变化时和事件的响应。例如，父与子关系的视图层次结构判断哪些对象能处理特定的触摸事件。同样，父与子关系也定义了每个视图如何响应界面方向的变化。

图 3-1 例子说明视图何如分层创建所需的应用程序视觉效果。在这个时钟应用程序的案例中，视图的层次结构是来自不同来源的复杂的视图的组合。标签栏和导航视图是特定的视图层次结构提供了标签栏和导航控制器对象用来管理整体用户界面的一部分。这些栏之间的所有东西都属于时钟应用程序提供的自定义层级结构。

**图 3-1 ** 时钟应用程序内的视图层次

![图 1-7 UIKit 与你的视图对象交互](./windowlayers.jpg)

这里有几种方式在 iOS 中建立视图层次结构，包使用界面构造器的图形化方式和使用代码的编程方式。下面的章节说明如何组合你的视图层次结构，之后会说明如何在层次结构中查找视图和如何在不同的视图坐标系统之间转换。

###添加和移除子视图
界面构造器是构建视图层次结构最便利的方法因为它提供了图形化的视图，可见看见视图之间的关系，并且这些视图会准确的在运行时出现。当使用界面构造器时，你可以把结果保存在 nib 文件中，当应用程序在运行时可以加载需要的相应的视图。

如果你喜欢使用编程的方式创建，你可以创建并初始化它们然后使用下面的方法把它们安排到层次结构：

- 要添加子视图到父视图，调用父视图的 [addSubview:]()。这个方法添加子视图到父视图的子视图列表的结尾。
- 要插入子视图到父视图的子视图列表中间，调用父视图的类似 insertSubview:... 的方法。子视图插入列表中间时后进入的子视图会在视图后面可见区域显示。
- 要重新排序父视图内部已有的子视图，调用父视图的 [bringSubviewToFront:](),[sendSubviewToBack:]()，或 [exchangeSubviewAtIndex:withSubviewAtIndex:]() 方法。使用这些方法比移除子视图然后重新插入更快。
- 要从父视图移除子视图，调用子视图 (非父视图) 的 [removeFromSuperview]() 方法。 

在添加子视图到父视图时，子视图的当前框架矩形代表它在父视图的初始位置。子视图的框架剪切时默认不会超出父视图的可见边界。如果你想你的子视图能被剪切到父视图的边界外，你必须明确的设置父视图的 [clipsToBounds]() 属性为 YES。

大多数常见的添加子视图到其他视图的例子发生在几乎所有应用程序的 application:didFinishLaunchingWithOptions: 方法中。清单 3-1 说明从应用程序的主视图控制器安装视图到应用程序窗口的方法的版本。窗口和视图控制器都是保存在应用程序的主 nib 文件，它们在这个方法被调用之后加载。也就是说，直到 view 属性可以被访问前通过视图控制器管理的视图层次结构并未被加载。

<br />
**清单 3-1** 添加视图到窗口

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Override point for customization after application launch.
 
    // Add the view controller's view to the window and display.
    [window addSubview:viewController.view];
    [window makeKeyAndVisible];
 
    return YES;
}
```

另一个常用的添加子视图到视图层次结构的位置是在视图控制器的 [loadView]() 或 [viewDidLoad]() 方法。如果你使用编程方式构建你的视图，你可以把你的视图创建代码放置到视图控制器的 loadVIew 方法。无论你是使用编程方式或从 nib 文件加载它们，你都可以在 viewDidLoad 方法中添加额外的视图配置代码。

清单 3-2 展示了简单应用程序 [*UICatalog:Creating and Customizing UIKit Controls (Obj-C and Swift)*]() 中的 TransitionsViewController 类的 viewDidLoad 方法。TransitionsViewController 类管理两个视图之间转换的关联动画。应用程序通过加载 nib 文件初始化视图层次结构 (由一个根视图和工具栏组成)。这段代码在 viewDidLoad 方法之后创建容器视图和图像视图用作管理转换效果。容器视图的目的是简化实现两个图像视图之间的转换动画效果需要的代码。容器视图自己没有实际的内容。

<br />
**清单 3-2** 添加视图到已有的视图层次结构

```
- (void)viewDidLoad
{
    [super viewDidLoad];
 
    self.title = NSLocalizedString(@"TransitionsTitle", @"");
 
    // create the container view which we will use for transition animation (centered horizontally)
    CGRect frame = CGRectMake(round((self.view.bounds.size.width - kImageWidth) / 2.0),
                                                        kTopPlacement, kImageWidth, kImageHeight);
    self.containerView = [[[UIView alloc] initWithFrame:frame] autorelease];
    [self.view addSubview:self.containerView];
 
    // The container view can represent the images for accessibility.
    [self.containerView setIsAccessibilityElement:YES];
    [self.containerView setAccessibilityLabel:NSLocalizedString(@"ImagesTitle", @"")];
 
    // create the initial image view
    frame = CGRectMake(0.0, 0.0, kImageWidth, kImageHeight);
    self.mainView = [[[UIImageView alloc] initWithFrame:frame] autorelease];
    self.mainView.image = [UIImage imageNamed:@"scene1.jpg"];
    [self.containerView addSubview:self.mainView];
 
    // create the alternate image view (to transition between)
    CGRect imageFrame = CGRectMake(0.0, 0.0, kImageWidth, kImageHeight);
    self.flipToView = [[[UIImageView alloc] initWithFrame:imageFrame] autorelease];
    self.flipToView.image = [UIImage imageNamed:@"scene2.jpg"];
}

```

>**重要提示：**父视图自动 retain 它们的子视图，因此之后嵌入的子视图它会安全的释放。事实上，是推荐这样做的因为它防止你的应用程序长期保留视图导致以后的内存泄漏。只需记住如果你从父视图移除子视图并打算重复使用它，你必须再次 retain 子视图。从父视图移除子视图后 removeFromSuperview 方法自动释放子视图。如果你下次重复使用时没有 retain 子视图，该视图将会被释放。<br /> 更多关于 Cocoa 的内存管理约定，见 [*Advanced Memory Management Programming Guide*]()。

当你添加子视图到其他视图时，UIKit 通知父视图和子视图已发生更改。如果你实现自定义视图，你可以通过重写一个或更多方法截取这些通知：[willMoveToSuperview:]()，[willMoveToWindow:]()，[willRemoveSubview:]()，[didAddSubview:]()，[didMoveToSuperview](),或 [didMoveToWindow]()。你可以使用这些通知更新任何状态信息相关的到你的视图层次结构中或执行额外的任务。

创建了视图层次结构后，你可以以编程的方式使用视图的 superview 和 subviews 属性浏览它。每个视图的 window 属性包含当前显示视图的窗口 ( 如果它有)。因为跟视图在视图层次结构中没有父视图，它的 superview 属性被设置为 nil。对于当前屏幕的视图，窗口对象是视图层次结构的根视图。

###隐藏视图
要从视觉上隐藏视图，你可以设置 [hidden]() 属性为 YES 或更改它的 [alpha]() 属性为 0.0。一旦隐藏视图后将不再从系统接收触摸事件。并且，隐藏视图后不再参与视图层次关联的自动缩放和其他布局操作。隐藏视图提供便利的操作替代从视图层次结构移除视图，尤其是你计划在再次显示视图时。

>**重要提示：**若你隐藏的视图是当前层次第一个响应者，视图不会自动移除它的响应者身份。事件传递的目标仍然是第一个响应者，也就是被隐藏的视图。要避免这个情况，你应该在隐藏视图时强制使视图移除它的第一响应者状态。更多关于响应队列的信息，见 [*Evebt Handling Guide for iOS*]()。

如果你想视图在隐藏和显示时添加动画效果，你必须使用视图的 alpha 属性。hidden 属性是不可动画的属性，因此你变更改属性后会立即生效。

###在视图层次结构定位视图
有两种方式在视图层次结构定位视图：

- 在适当的位置保存相应的视图指针，例如在视图控制器自己的视图中。
- 为每个视图的 [tag](http://) 属性分配无符号整形并使用 [viewWithTag:](http://) 方法定位它。

保存相应视图的引用是最通用的定位视图途径并且访问这些视图非常方便。如果你使用界面构造器构建你的视图，你可以在你的 nib 文件 (包括代表管理控制器对象的文件拥有者对象) 使用 [outlet](http://) 连接对象到其他一个。如果你使用编程方式，你可以保存视图的引用为私有成员变量。无论你使用 outlets 或 私有成员变量，你都要负责必要的保持和释放它们。最好的确认对象被正确保留和释放的方式是使用 [declared properties](http://)。
  
  Tag 是有效的减少硬编码依赖的方式并且支持更多动态和灵活的解决方案。与保存视图指针相比，你可以使用 tag 定位它。Tag 也是更持久的指向视图的方式。例如，你如果你想在应用程序中保存当前可见视图的列表，你会写出每个可见视图的 tag 到文件中。这比归档实际视图对象要简单，尤其在你正在追踪仅是当前可见的视图的情况下。当你的应用程序随后被加载，你就可以使用已保存的 tag 列表重新创建你的视图设置每个视图的可见性，从而使视图层次结构回到之前的状态。

###翻转，伸展，和旋转视图
每个视图都有关联的仿射转换，你可以用来翻转，伸展，或旋转视图的内容。视图转换后完成视图的外观渲染并且提供用作实现滚动，动画效果，或其他视觉效果。

视图的 [transform](http://) 属性包含 [CGAffineTransform](http://) 结构体应用转换效果。默认情况下，这个属性设置为恒等转换，不会修改视图的外观。你可以在任意时间为属性分配新的转换。例如，旋转视图 45 度，你可以使用下面的代码：

```
// M_PI/4.0 is one quarter of a half circle, or 45 degrees.
CGAffineTransform xform = CGAffineTransformMakeRotation(M_PI/4.0);
self.view.transform = xform;

```
应用上面的代码到视图中后视图将会围绕中心顺时针旋转。图 3-2 说明对应用程序嵌入的图片应用的转换效果。

<br />
**图 3-2**  旋转视图 45 度
![image](./rotated_view.jpg)

当对视图应用了多个转换效果时，转换效果添加到 CGAffineTransform 结构体中的顺序是相当重要的。旋转视图然后翻转它不等于翻转它然后旋转。即使旋转和翻转的数量是一样的情况，转换效果的顺序会影响到最终的后果。除此之外，所有你添加的到视图的转换效果应用都是相对视图的中心点的。因此，旋转效果围绕视图中心点旋转。伸展视图变更视图的宽度和高度但不会变更它的中心点。

更多关于创建和使用仿射转换的信息，见 [*Quartz 2D Programming Guide*]() 中的 [Transforms](http://)。

###在视图层次结构中转换坐标
在不同的时间，尤其在处理事件时，应用程序可能需要从一个引用的框架转换坐标值到另一个。例如，触摸事件报告在窗口坐标系统中每个触摸的位置但视图对象通常需要视图的本地坐标系统信息。 [UIView](http://) 类定义了下面的方法将坐标从视图的本地坐标系统转出或转换到本地坐标系统：

- [convertPoint:fromView:](http://)
- [convertRect:fromView:](http://)
- [convertPoint:toView:](http://)
- [convertRect:toView:](http://)

convert...:fromView: 方法从一些其他视图的坐标系统转换到当前视图的本地坐标系统  (边界矩形)。反过来，convert...:toView: 方法从当前视图的坐标系统 (边界矩形) 转换到指定视图的坐标系统。如果将这些函数的引用视图指定为 nil，这些转换会转出或转入包含该视图的窗口的坐标系统。

除了 UIView 的转换方法，[UIWindow](http://) 类也定义了几个转换方法。这些方法类似 UIView 版本除了转换的目标是视图的本地坐标系统，这些方法转换窗口的坐标系统。

- convertPoint:fromWindow:
- convertRect:fromWindow:
- convertPoint:toWindow:
- convertRect:toWindow:

当在一个已旋转的视图转换坐标时，UIKit 假设你想用已返回的矩形反映屏幕被源矩形覆盖的区域的前提下转换矩形。图 3-3 例子说明旋转是如何导致矩形尺寸在转换期间的改变。在这个图中，外部分视图包含已旋转的子视图。从子视图的坐标系统转换到父视图的坐标系统后的产生一个物理上较大的矩形。这个较大的矩形实际上是在外部视图 (outerView) 的边界中完全包围已旋转的最小的矩形。

<br />
**图 3-3 在已旋转视图转换值**

![image](./uiview_convert_rotated.jpg)

##在运行时调整视图的尺寸和位置
每当视图的尺寸更改，它的子视图的尺寸和位置也必须跟随更改。[UIView](http://) 支持在视图层次结构中的视图的自动和手动布局。对于自动布局，你只需设置每个视图应该如何跟随当父视图调整时的规则，然后就可以不管之后的调整操作了。对于手动布局，根据需要手动调整视图的尺寸和位置。

###布局变更的准备
布局变更会在每当下面的事件在视图中发生时：
- 视图边界矩形的尺寸更改。
- 界面方向发生更改，这个事件通常会在根视图的边界矩形触发。
- 与核心动画子层的集合关联的视图的层发生变更并需要布局。
- 应用程序通过调用视图的 [setNeedsLayout](http://) 或 [layoutIfNeeded](http://) 方法发生强制布局。
- 应用程序通过调用视图的底层对象的 [setNeedsLayout](http://) 方法强制布局。

###使用自动调整规则自动处化理布局变更
当你更改视图的尺寸时，被嵌入的子视图的位置和尺寸通常需要根据父视图的新尺寸变更。父视图的 [autoresizesSubviews](http://) 属性决定所有子视图是否需要调整。如果属性设置为 YES，视图会使用每个子视图的 [autoresizingMask](http://) 属性决定子视图的位置和尺寸。每个子视图的尺寸变更会触发它们嵌入的子视图同样的布局调整。

在你的视图层次结构中的每个视图，设置 autoresizingMask 属性一个适当的值对于自动化处理布局变更是非常重要的一部分。表 3-2 列出你可以应用的自动调整选项并且描述了它们在布局操作时的效果。你可以使用 OR 运算符组合这些常量或在分配他们到 autoresizingMask 属性之前把它们加到一起。如果你是使用界面构造器组合你的视图，你可以在 Autosizing inspector 设置这些属性。

<br />
**表 3-2** 自动调整掩码常量

自动调整掩码 | 描述 
------------ | ------------- 
UIViewAutoresizingNone | 视图不会自动调整。(这是默认值。)
UIViewAutoresizingFlexibleHeight | 当父视图高度变更时视图的高度也变更。如果这个常量没被包含，视图的高度将不会变更。
UIViewAutoresizingFlexibleWidth | 当父视图宽度变更时视图的宽度也变更。如果这个常量没被包含，视图的宽度将不会变更。
UIVIewAutoresizingFlexibleLeftMargin | 视图的左边缘和父视图的左边缘之间的距离根据需要增长或缩短。如果这个常量没有设置，视图的左边缘与父视图的左边缘之间的距离保持固定。
UIViewAutoresizingFlexibleRightMargin | 视图的右边缘和父视图的右边缘之间的距离根据需要增长或缩短。如果这个常量没有设置，视图的右边缘与父视图的右边缘之间的距离保持固定。
UIViewAutoresizingFlexibleBottomMargin | 视图的底部边缘和父视图的底部边缘之间的距离根据需要增长或缩短。如果这个常量没有设置，视图的底部边缘与父视图的底部边缘之间的距离保持固定。
UIViewAutoresizingFlexibleTopMargin | 视图的顶部边缘和父视图的顶部边缘之间的距离根据需要增长或缩短。如果这个常量没有设置，视图的顶部边缘与父视图的顶部边缘之间的距离保持固定。
 
图 3-4 显示的图像说明自动调整掩码中的选项如何应用到视图中。已设置的常量说明视图的指定的方面是灵活的和父视图的边界变更时视图也会相应变更。没设置的常量说明视图的布局在某些方面是固定的。当你在视图的一个轴上配置一个或多个灵活属性时，UIKit 会把尺寸的更改均匀的分配到相应的空间。

**图 3-4** 视图的自动调整掩码常量
![image](./uiview_autoresize.jpg)

最简单配置自动调整规则的方式是使用界面构造器的 Size inspector 中的自动调整大小控制。在自动调整大小界面的宽度和尺寸指示器与上图中灵活的宽度和高度常量拥有相同的行为。但是，边缘指示器的行为和使用与前面的相反。在界面构造器中，已设置的边缘指示器以为着边缘有固定的尺寸而没设置的边缘指示器意味着边缘有灵活的尺寸。幸运的是，界面构造器提供了一个动画效果让你清楚自动调整行为如何影响到你的视图。

> **重要提示：** 如果视图的 [transform](http://) 属性没有设置为恒等转换，那么视图的 frame 是未定义的所以它可以自动调整。

所有受影响的视图已经应用了自动调整规则后，UIKit 返回每个视图并给它机会对父视图做必要的手动调整。更多关于如何手动管理视图的布局的信息，见 [Tweaking the Layout of Your Views Manually](http://)。


###手动调整你的视图布局
每当你的视图尺寸发生变化时，UIKit 应用视图的父视图的自动调整尺寸行为然后调用视图的 [layoutSubviews](http://) 方法让它可以手动更改。当自定义视图的自己自动调整行为没有产生你想要的结果时你可以实现自定义视图的 layoutSubviews 方法。在这个方法的实现中你可以做一下行为：

- 调整任意直系子视图的位置和尺寸。
- 添加或移除子视图或核心动画层。
- 通过调用 [setNeedsDisplay](http://) 或 [setNeedsDisplayInRect:](http://) 方法强制子视图重绘。

应用程序经常手动布局子视图的地方是在实现巨大的可滚动区域时。因为在一个巨大的视图中完成滚动内容是不切实际的，应用程序经常会实现一个包含小片视图的数字的根视图。每一个小片代表可滚动内容的一部分。当滚动事件发生时，根视图调用 setNeedsLayout 方法初始化布局变更。layoutSubviews 方法然后基于已发生的滚动数量重新定位片视图。对于已滚动出去视图可见区域的片，layoutSubviews 方法移动该片到输入边缘，在处理过程中更换它们的内容。

当你在写布局代码时，请确认你的代码已在以下方面测试：

- 更改视图方向确认布局在所有的方向看都是正确的。
- 确认你的代码适当的响应状态栏高度的更改。当电话呼叫是活动时，状态栏高度会提升，而当用户挂断电话时，状态栏尺寸会降低。

关于自动调整行为如何影响你的视图的尺寸和位置，见 [Handling Layout Changes Automatically Using Autoresizing Rules](http://)。关于如何显现视图分片的例子，见案例 ScrollViewSuite。




##在运行时修改视图
当应用程序从用户中接收到输入，它们会在响应输入时调整用户界面。应用程序可能重新排列视图，更改尺寸或位置，隐藏或显示它们，或加载一个完整的新的视图集合。在 iOS 应用程序中，这里是执行这些动作的种类几个地方和方式：

- 在视图控制器中：
	- 视图控制器在显示视图之前已创建它们。视图控制器可以从 nib 文件加载视图或使用编程方式创建它们。当这些视图已经长期不需要时，它会抛弃它们。
	- 当设备变更方向时，视图控制器可能会调整视图的尺寸和位置适配。作为调整新的方向的一部分，它可能会隐藏视图和显示其他内容。
	- 当视图控制器管理可编辑的内容时，当进入编辑模式时它可能会调整视图层级结构。例如，它可能添加额外的按钮和其他控件方便编辑内容的各个方面。也可能需要调整视图的尺寸容纳额外的控件。
- 在动画效果块中：
	- 当你想添加用户界面中不同的视图集合之间的转换效果，你会隐藏一些视图和显示其他来自动画效果块内部的视图。
	- 在实现特别的效果时，你可能会使用一个动画效果块去修改视图的各个属性。例如，一个更改视图尺寸的动画，你会更改它的框架矩形尺寸。
	 
- 其他方式：
	- 当触摸事件或手势发生时，你的界面可能会通过加载新的视图集合或更改当前视图集合来响应事件。关于处理事件的信息，见 [*Event Handling Guide for iOS*](http://)。
	- 当用户与滚动视图交互时，一个巨大的可滚动区域可能会隐藏和显示分片的子视图。更多对可滚动内容支持的信息，见 [*Scroll View Programming Guide for iOS*](http://)。
	- 当键盘出现时，你可能会重新定位或调整视图使它们不会躺在键盘的下方。关于如何与键盘交互的信息，见 [*Text Programming Guide for iOS*](http://)。
	
视图控制器是初始换更改视图的常用地方。因为视图控制器管理视图层次结构关联的内容的显示，它最终负责这些视图发生的所有事情。当加载它的视图或处理方向更改时，视图控制器可以添加新的视图，隐藏或替换已有的视图，和做一些改变使视图准备显示。如果你声明了对你的内容的编辑，在 UIViewController 中的 [setEditing:animated:](http://) 方法给你一个地方让你的视图转换为它的可编辑版本。

动画效果块是另一个常用的初始化视图相关的更改的地方。动画效果支持建立到 [UIView](http://) 类中让它轻松的用动画形式更改视图的属性。你也可以使用 [transitionWithView:duration:options:animations:completion:](http://) 或 [transitionFromView:toView:duration:options:completion:](http://) 方法更换完整的视图集合为新的一个。

更多关于动画视图和初始化视图转换效果的信息，见 [Animations](http://)。

更多如何使用视图控制器管理视图相关的行为，见 [*View Controller Programming Guide for iOS*](http://)。


##与核心动画层交互

每个视图对象都有专用的核心动画层用来管理视图在屏幕上的呈现和动画效果。你可以对你的视图对象做很多事情，你也可以按需要直接在相应的层对象工作。层对象被保存在视图的 [layer](http://) 属性。

###修改与视图关联的分层类

与视图关联的层类型不能再视图被创建后更改。因此，每个视图的使用 [layerClass](http://) 类方法指定层对象的类。这个方法默认的实现是返回 [CALayer](http://) 类并且只能从子类更改这个值，重写这个方法，返回不同的值。你可以使用不同类型的层更改这个值。例如，如果你的视图使用分片显示一个巨大的可滚动区域，你可能想使用 [CATileLayer](http://) 类返回给视图。

[layerClass](http://) 方法的实现应该只是简单的创建期望的 Class 对象并且返回它。例如，视图使用分片的方式将会使用下面的代码重写这个方法：

```
+ (Class)layerClass
{
    return [CATiledLayer class];
}

```

每一个视图调用 layerClass 方法时间都早于它的初始化处理并且使用返回的类创建它的层对象。除此之外，视图总会将自己分配为层对象的委托。在这一点上，视图拥有的层和视图与层之间的关系必须不改变。你也必须不能分配同样的视图作为其他层对象的委托。更改所有者关系或视图的委托关系将导致绘制问题和应用程序出现不可预料的崩溃。

更多关于核型动画提供的不同类型的层对象，见 [*Core Animation Reference Collection*](http://)。

###在视图中嵌入层对象
如果你打算主要与层对象工作而不是与视图工作，你可以按需要把自定义层对象合并到你的视图层次结构中。自定义层对象是 [CALayer](http://) 的实例它不属于视图拥有。通常使用编程方式创建自定义层然后使用核心动画效果程序合并它们。自定义层不接收事件和不参与响应者队列但会绘制它们并且响应它们的父视图的尺寸更改或层根据核心动画效果规则的尺寸更改。

清单 3-3 展示了在一个视图控制器的 viewDidLoad 方法中创建自定义层对象并且添加它到它的根视图的例子。这个层对象被用作用动画显示静态图片。除了添加层到视图本身外，你也可以添加它到视图的底层 (underlying layer)。

**清单 3-3** 添加自定义层到视图

```
- (void)viewDidLoad {
    [super viewDidLoad];
 
    // Create the layer.
    CALayer* myLayer = [[CALayer alloc] init];
 
    // Set the contents of the layer to a fixed image. And set
    // the size of the layer to match the image size.
    UIImage layerContents = [[UIImage imageNamed:@"myImage"] retain];
    CGSize imageSize = layerContents.size;
 
    myLayer.bounds = CGRectMake(0, 0, imageSize.width, imageSize.height);
    myLayer = layerContents.CGImage;
 
    // Add the layer to the view.
    CALayer*    viewLayer = self.view.layer;
    [viewLayer addSublayer:myLayer];
 
    // Center the layer in the view.
    CGRect        viewBounds = backingView.bounds;
    myLayer.position = CGPointMake(CGRectGetMidX(viewBounds), CGRectGetMidY(viewBounds));
 
    // Release the layer, since it is retained by the view's layer
    [myLayer release];
}


```

你可以安排任意数量的子层并安排它们到子层的层次结构，如果你想的话。然而在某些时候，这些层必须被绑定到视图的层对象中。

关于如何与这些层直接工作，见 [*Core Animation Programming Guide*](http://)


##定义自定义视图

如果标准系统视图无法满足你的需要，你可以定义自定义视图。自定义视图能让你完全控制应用程序的内容的外观和处理如何如内容交互。

> **说明：** 如果你使用 OpenGL ES 完成绘制，你应该使用 [GLKView](http://) 类替代 UIVIew 的子类。更多关于如何使用 OpenGL ES 绘制的信息，见 [*OpenGL ES Programming Guide for iOS*](http://)。




###实现自定义视图需要的检查清单
自定义视图的工作是呈现内容并管理与内容的交互。但是完美的自定义视图实现涉及到很多的绘制和事件处理。下面的检查清单包含许多在实现自定义视图时你可以重写的方法 (和你可以提供的行为)。

-  为你的视图定义外观的初始化方法：
	- 如果打算以编程方式创建视图，重写 []initWithFrame:](http://) 方法或定义自定义初始化方法。 
	- 如果打算以从 nib 文件加载，重写 [initWothCoder:](http://) 方法。使用这个方法初始化你的视图并把它放入已知状态。
-  实现 [dealloc](http://) 方法处理自定义数据的清除操作。
-  要处理自定义视图的绘制，需要重写 [drawRect:](http://) 方法并在这里实现绘制代码。
-  设置视图的 [autoresizingMask](http://) 属性定义它的自动调整行为。
-  如果你的视图类管理一个或多个完整的子视图，要做以下事情：
	-  在你的视图的初始化序列期间创建这些子视图。
	- 在创建时设置每个子视图的 [autoresizingMask](http://) 属性。
	- 如果你的子视图需要自动布局，重写 [layoutSubviews](http://) 方法并且在那实现你的布局代码。
-  处理基于触摸的事件，要做以下事情：
	-  通过使用 addGestureRecognizer: 方法绑定手势识别到视图中。
	- 有你想自己处理触摸的情景，重写 [touchesBegan:withEvent:](http://)，[touchesMoved:withEvent:](http://)，[touchesEnded:withEvent:](http://)，和 [touchesCancelled:withEvent:](http://) 方法。(记住你应该始终重写 [touchesCancelled:withEvent:](http://) 方法，无论你是否重写了其他的触摸相关的方法。)
-  如果你让视图的打印版本看起来与屏幕版本不同，需要实现 [drawRect:forViewPrintFormatter:](http://) 方法。关于如何在你的视图中实现对打印的支持的详细信息，见 [*Drawing and Printing Guide for iOS*](http://)

除了重写方法外，记住还有很多你可以操作的视图已有的属性和方法。例如，[contentMode](http://) 和 [contentStretch](http://) 方法能让你更改视图的最终渲染的外观并且可以完美的绘制你的内容。除了 [UIView](http://) 类本身外，还有视图的底层 [CALayer](http://) 对象的你可以直接或间接配置的许多方面。你甚至可以更改层对象自身的类。

更多关于视图类的方法和属性的信息，见 [*UIView Class Reference*](http://)。

###初始化你的自定义视图
每个你定义的新视图对象都应该包含自定义的 [initWithFrame:](http://) 初始化方法。这个方法负责在创建时初始化类并且把你的视图对象放入已知状态。你可以在代码中使用编程方式创建视图的实例时使用这个方法。

清单 3-4 说明显示标准 initWithFrame: 方法实现的骨骼。这个方法首先调用已继承的方法实现然后在返回已初始化的对象之前初始化实例变量和类的状态信息。首先调用已继承的实现是传统的执行方式，因此如果初始化时发生问题，你可以中断初始化代码并返回 nil。

<br />
**清单 3-4** 初始化视图子类

```
 (id)initWithFrame:(CGRect)aRect {
    self = [super initWithFrame:aRect];
    if (self) {
          // setup the initial properties of the view
          ...
       }
    return self;
}
```

如果你计划从 nib 文件加载你的自定义视图类的实例，你应该注意在 iOS 中，加载 nib 的代码不会使用 initWithFrame: 方法实例化新的视图对象，而是使用 [initWithCoder:](http://) 方法它属于 [NSCoding](http://) 协议的一部分。

即使如果你的视图采用了 NSCoding 协议，界面构造器也不会知道关于你的视图的自定义属性因此也不会编码这些属性到 nib 文件中。因此，你自己的 initWithCoder: 方法无论初始化代码是否能把视图放入已知状态都应该执行。你也可以实现在你的视图类中的 [awakeFromNib](http://) 方法并且使用这个方法执行额外的初始化。

###实现你的绘制代码
对于需要自定义绘制的视图，你需要重写 [drawRect:](http://) 方法并且在此完成你的绘制。自定义绘制是最后才推荐使用的手段。通常，如果你能使用其他视图呈现你的内容的话，那才是首选。

你的 drawRect: 方法的实现应该明确一件事：绘制你的内容。这个方法不是更新你的应用程序的数据结构或执行与绘制无关的任务的地方。它应该配置绘制环境，绘制内容，和尽可能快的退出这个方法。如果你的 drawRect: 方法可能频繁的调用，你应该尽可能的优化绘制代码并且每次调用这个方法时尽可能少的绘制内容。

调用你的视图的 [drawRect:](http://) 方法之前，UIKit 为你的视图配置基础绘制环境。特别是，它会创建图形上下文和调整坐标系统并且裁剪它到适配的坐标系统和显示你的视图的边界。因此，通过调用你的 drawRect: 方法后，你可以使用原生绘制技术例如 UIKit 和 Core Graphics 开始绘制你的内容。你可以使用 UIGraphicsGetCurrentContent 函数获取当前图形上下文的指针。

> **重要提示：**当前图形上下文只在视图的 drawRect: 方法调用时生效一次。UIKit 会为随后每个调用这个方法创建不同的图形上下文，你不应该尝试缓存这个对象在以后使用。

清单 3-5 展示一个简单的 drawRect: 方法的实现它会绘制一个 10 像素点宽的红色边框视图。因为 UIKit 绘制操作使用 Core Graphics 作为它们的底层实现，你可以混合绘制调用，获得你想要的结果。

<br />

**清单 3-5** 绘制方法

```

- (void)drawRect:(CGRect)rect {
    CGContextRef context = UIGraphicsGetCurrentContext();
    CGRect    myFrame = self.bounds;
 
    // Set the line width to 10 and inset the rectangle by
    // 5 pixels on all sides to compensate for the wider line.
    CGContextSetLineWidth(context, 10);
    CGRectInset(myFrame, 5, 5);
 
    [[UIColor redColor] set];
    UIRectFrame(myFrame);
}

```

如果你清楚你的视图绘制代码总是以不透明的内容覆盖视图的整个表面，你可以通过设置视图的 [opaque](http://) 属性为 YES 提升系统性能。当你标记了视图为不透明时，UIKit 会避开在你视图背后的绘制代码。这不是唯一缩减绘制花费的时间的方式但也最大限度减少视图和其他内容之间的合成工作。因此，如果你的视图的内容是完全不透明的那么应该设置这个属性为 YES。如果你的视图不能保证内容总是不透明的，你应该设置这个属性为 NO。

另一个提升绘制性能的方式，尤其在滚动过程中，是设置你的视图的 [clearsContextBeforeDrawing](http://) 属性为 NO。当这个属性设置为 YSE 时，UIKit 自动填充将要被你的 drawRect: 方法更新的区域并且在方法调用前显示透明的黑色。设置这个属性为 NO 会消除填充操作的开销但会加重你的应用程序的负担需要通过 drawRect: 方法与内容填充更新矩形。

###事件响应
视图对象是一个响应者对象－[UIResponder](http://) 类的实例－因此有接收触摸事件的能力。当触摸事件发生时，窗口调度相应的事件对象到已发生触摸的视图。如果你的视图对事件不敢兴趣，它可以忽略或传递它到响应者队列让不同的对象处理。

除了直接处理触摸事件外，视图也可以使用手势识别检查敲击，滑动，捏，和其他类型的常用触摸相关的手势。手势识别完成困难的追踪触摸事件工作并确保它们正确和标准的触发手势。你的应用程序除了可以追踪触摸事件，你还可以创建手势识别，为它分配一只适当的触发对象和动作方法，然后在你的视图中使用 [addGestureRecognizer:](http://) 方法安装这个手势。当相应的手势发生后手势识别会调用你分配的动作方法。

如果你打算直接处理事件，你可以在你的视图中实现下面的方法：它们的详细描述在 [*Event Handling Guide for iOS:*](http://)

- [touchesBegan:withEvent:](http://)
- [touchesMoved:withEvent:](http://)
- [touchesEnded:withEvent:](http://)
- [touchesCancelled:withEvent:](http://)

视图的默认行为是同时只响应一个触摸事件。如果用户按下第二根手指，系统会忽略该触摸事件并且不会向视图报告。如果你计划在你的视图的事件处理方法追踪多手指手势，你需要通过设置你的视图的 [multipleTouchEnabled](http://) 属性为 YES 启用多点触控事件。

某些视图，例如标签和图像，默认完全禁用事件处理。你可以通过更改视图的 [userInteractionEnabled](http://) 属性值控制视图是否允许接收触摸事件。你可以暂时将这个值设置为 NO 防止用户在长时间的运算等待时操作了视图的内容。要防止事件到达你的所有视图，你也可以使用 [UIApplication](http://) 对象的 [beginIgnoringInteractionEvents](http://) 和 [endIgnoringInteractionEvents](http://) 方法。这些方法影响整个应用程序的事件的发出，不只是单个视图。

>**说明：**[UIView](http://) 的动画效果方法通常在进行动画效果处理时禁用事件处理。你可以通过配置适当的动画效果重写这个行为。更多关于动画效果的执行，见 [Animations](http://)。

由于需要处理触摸事件，UIKit 使用 UIView 的 [hitTest:withEvent:](http://) 和 [pointInside:withEvent:](http://) 方法决定事件是否发生在给定视图的边界内。尽管很少需要重写这些方法，但你可能会为你的视图实现自定义触摸行为时用到。例如，你可以重写这些方法防止子视图处理触摸事件。



###清除你的视图
如果你的视图类分配了内存，保存了任意自定义对象的引用，或保留了视图释放时也必须释放的资源，那么你必须实现 [dealloc](http://) 方法。当你的视图的引用记数到达零并且到达了销毁时间那么系统会调用 dealloc 方法。这个方法的实现过程应该释放所有对象和视图持有的资源然后调用被继承的实现过程，如清单 3-6 的代码。你不应该使用这个方法执行任何其他类型的任务。

清单 3-6 实现 dealloc 方法

```

- (void)dealloc {
    // Release a retained UIColor object
    [color release];
 
    // Call the inherited implementation
    [super dealloc];
}

``` 

---
系列文章

[*iOS 笔记 《View Programming Guide for iOS：Introduction》*](../iOS-Note-View-Programming-Guide-for-iOS-Introduction) 

[*iOS 笔记 《View Programming Guide for iOS：View and Window Architecture》*](../iOS-Note-View-Programming-Guide-for-iOS-View-and-Window-Architecture)

[*iOS 笔记 《View Programming Guide for iOS：Windows》*](../iOS-Note-View-Programming-Guide-for-iOS-Windows)

*iOS 笔记 《View Programming Guide for iOS：Views》* 

[*iOS 笔记 《View Programming Guide for iOS：Animations》*]() 

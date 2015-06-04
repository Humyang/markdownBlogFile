layout: [post]
title: "iOS 笔记 《View Controller Programming Guide for iOS：Creating Custom Content View Controllers》"
date: 2015-05-22 20:43:26
tags: 
- iOS
categories: 
- iOS 开发
- 翻译
id: "VCP4"

---

iOS 视图控制器编程指南：视图控制器的资源管理


<!-- more -->

[原文地址](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007457-CH1-SW1)

记录关于学习过的 iOS 文档

---

# 视图控制器的资源管理

视图控制器是管理应用程序的资源的必要部分。视图控制器可以使你的应用程序划分为多个部分并且只实例化所需要的部分。不仅如此，视图控制器自己也管理着不同的资源并在不同的时间实例化它们。例如，视图控制器的视图层次结构只有在视图被访问时才会实例化；通常，这种情况只会发生在视图在屏幕上显示时。如果多个视图控制器同时被推送到导航堆栈上，那么只有最上层的视图控制器的内容会显示，这意味着只有它的视图是被访问的。同样，如果视图控制器不通过导航控制器呈现，那么它不需要实例化它的导航项。通过延迟分配大多数资源是有必要的，视图控制器可以使用更少的资源。

当应用程序运行时可用内存过低，系统会自动通知所有视图控制器。这个通知会使视图控制器清除缓存和其它对象，它们可以在以后内存充足时容易的创新创建。实际的行为会根据应用程序在不同 iOS 版本运行而有所不同，这也对你的视图控制器设计造成影响。

细心的管理与视图控制器关联的资源是你的应用程序高效运行的关键。你应该也比较喜欢延迟分配；创建和维持对象是昂贵的，应该延迟分配它们并只在需要时才分配。因为这个原因，你的视图控制器应该在整个生命周期按需要分开对象因为对象只有在某些时候才需要。当你的视图控制器接收到底内存警告时，如果它不是显示在屏幕上的，应该准备缩减它的内存使用。

## 初始化视图控制器

当视图控制器首次被实例化时，它应该创建或加载它的生命周期需要的对象。它不应该创建它的视图层次结构或与它显示的内容关联的对象。它应该关注数据对象和它实现关键行为所需要的对象。

### 初始化视图控制器从故事板加载

当你在故事板中创建视图控制器时，你在界面构造器配置的属性会序列化到一个存档中。之后，当视图控制器实例化后，这个存档会加载到内存中并处理。这样可以使对象的属性设置与界面构造器设置的属性相同。这个存档通过调用视图控制器的 [initWithCoder:](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Protocols/NSCoding_Protocol/index.html#//apple_ref/occ/intfm/NSCoding/initWithCoder:) 方法加载。然后，[awakeFromNib](https://developer.apple.com/library/ios/documentation/UIKit/Reference/NSObject_UIKitAdditions/index.html#//apple_ref/occ/instm/NSObject/awakeFromNib) 方法被任何实现该方法的对象调用。你可以使用这个方法对已被实例化的对象执行任何需要其它对象的配置步骤。

关于更多 archiving 和 archiving，见 [Archives and Serializations Programming Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047i)。

### 初始化视图控制器以编程方式

如果视图控制器以编程方式分配它的资源，需要为视图控制器指定一个自定义初始化方法。这个方法应该调用它的父类的 [init](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/index.html#//apple_ref/occ/instm/NSObject/init) 方法并执行类特定的初始化任务。

通常情况下，不要写太复杂的初始化方法。而应该实现简单的初始化方法，然后为视图控制器使用者提供属性配置它的行为。

## 视图控制器实例化它的视图当视图被访问时

每当应用程序的某部分询问视图控制器它的视图对象并且该对象当前不再内存中时，视图控制器会加载视图层次结构到内存中并将它保存到 [view](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/view) 属性中以便将来使用。加载周期期间会产生以下步骤：

1.视图控制器调用它的 [loadView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/loadView) 方法。`loadview` 方法的默认实现过程做以下两件事之一：

* 如果视图控制器是与故事板关联，它会从故事板加载视图。
* 如果视图控制器没有与故事板关联，一个空的 [UIView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) 对象会被创建并分配到 `view` 属性。 

2.视图控制器调用它的 [viewDidLoad](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidLoad) 方法，它让你的子类执行任意额外的加载时间 (load-time) 任务。

图 4-1 以可视化变现了加载周期，包含几个被调用的方法。你的应用程序可以根据需要重写 `loadView` 和 `viewDidLoad` 方法方便的让视图控制器按照你的想法执行。例如，如果你的应用程序没有使用故事板但你想额外的视图能被添加到视图层次结构中，你可以通过重写 `loadView` 方法以编程方式初始化这些视图。

**图 4-1** 加载视图到内存中
![](./loading_a_view_into_memory_2x.png)

### 从故事板加载视图控制器的视图

大多是视图控制器加载它们的视图从相关联的故事板中。使用故事板的优点是它们可以让你以图形化界面布局和配置视图，可以轻松和快速的调整布局。你可以快速迭代不同的版本，细致的打磨应用程序界面。

#### 在界面构造器创建视图

界面构造器是 Xcode 的一部分，提供了直观的方式为视图控制器创建和配置视图。使用界面构造器，可以直接操作视图和控件组合它们，拖动它们进入工作空间，可以使用 inspector 窗口定位，调整尺寸，并修改它们的属性。结果会保存到故事板文件中，它保存着你组合的对象集合与所有关于你所做的自定义信息。

##### 在界面构造器配置视图的显示属性

为了帮助正确布局视图的内容，界面构造器提供了控件让你指定视图是否拥有导航栏，工具栏或其它可以影响你的自定义内容的位置的对象。如果控制器在故事板中连接到了容器控制器内，它可以从容器推断这些设置，可以轻松并准确的见到它如何在运行时显示。

##### 为你的视图控制器配置 Action 和 Outlets

通过使用界面构造器，你可以创建在你的界面中的视图和你的视图控制器之间的连接。清单 4-1 展示的自定义类 `MyViewController` 的声明中有两个自定义 outlets (通过关键字 [IBOutlet]() 定义) 和单个 action 方法 (定义为 [IBAction]() 返回类型)。它们声明在实现文件的 category 内。outlets 保存着在故事板中的按钮和文本字段的引用，action 方法响应按钮的 taps 事件。

**清单 4-1** 自定义视图控制器的类声明

```

@interface MyViewController()
@property (nonatomic) IBOutlet id myButton;
@property (nonatomic) IBOutlet id myTextField;
 
- (IBAction)myAction:(id)sender;
@end


```

图 4-2 展示你将在 `MyViewController` 类中创建的对象的连接。

**图 4-2** 故事板中的连接

![](./storyboard_art_2x.png)

当上面配置的 `MyViewController` 类创建和呈现后，视图控制器的基础架构自动从故事板加载视图并重新配置所有 outlets 或 actions。因此，当视图被呈现给用户时，你的视图控制器的 outlets 和 actions 已经被设置并可以使用。这个能力作为你的运行代码和你设计时的资源文件之间的桥梁，这是故事板如此强大的原因之一。

### 创建视图以编程方式

如果你准备以编程方式创建视图，替代故事板的使用，你可以通过重写视图控制器的 [loadView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/loadView) 方法达成目的。你的这个方法的实现过程应该做以下的事情：

1.创建根视图对象。

根视图对象包含所有与你的视图控制器关联的其它视图，通常定义这个视图的框架以视图应用程序窗口的尺寸，它自己应该填充整个屏幕。但是，这个框架是基于你的视图控制器如何显示而调整。见 [Resizing the View Controller‘s Views](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/AdoptingaFull-ScreenLayout/AdoptingaFull-ScreenLayout.html#//apple_ref/doc/uid/TP40007457-CH13-SW2)。

你可以使用通用的 [UIView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) 对象，由你自定义视图，或可以拓展的其它视图填充满屏幕。

2.创建额外的子视图并把它们添加到根视图。

对每个视图，你应该：

a.创建和初始化视图。
b.使用 [addSubview:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instm/UIView/addSubview:) 方法添加视图到父视图。

3.如果你使用自动布局，请为你创建的每个视图分配足够的约束控制视图的位置和尺寸。否则，可以实现 [viewWiiLayoutSubviews](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillLayoutSubviews) 和 [viewDidLayoutSubviews](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidLayoutSubviews) 方法调整视图层次结构中子视图的框架。见 [Resizing the View Controller‘s Views](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/AdoptingaFull-ScreenLayout/AdoptingaFull-ScreenLayout.html#//apple_ref/doc/uid/TP40007457-CH13-SW2)。

4.把根视图分配到你的视图控制器的 [view](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instp/UIViewController/view) 属性。

清单 4-2 展示一个 `loadView` 方法的实现例子。这个方法在视图层次结构中创建了一对自定义视图并把它们分配到视图控制器。

清单 4-2 以编程方式创建视图

```

- (void)loadView
{
    CGRect applicationFrame = [[UIScreen mainScreen] applicationFrame];
    UIView *contentView = [[UIView alloc] initWithFrame:applicationFrame];
    contentView.backgroundColor = [UIColor blackColor];
    self.view = contentView;
 
    levelView = [[LevelView alloc] initWithFrame:applicationFrame viewController:self];
    [self.view addSubview:levelView];
}

```

> 当重写 loadView 方法以编程方式创建你的视图控制器时，你不应该调用 super。因为这样做会启动默认视图加载行为并且通常会浪费 CPU 周期。你的 `loadView` 实现过程应该只做为你的视图控制器创建根视图和子视图所需要的工作。更多关于视图加载的处理信息，见 [A View Controller Instantiates Its View Hierarchy When Its View is Accessed](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html#//apple_ref/doc/uid/TP40007457-CH10-SW2)。

## 高效管理内存

### iOS 6 和之后的版本，视图控制器在需要时卸载属于它的视图

### iOS 5 和之前的版本，当内存过低时系统可能会卸载视图

---

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



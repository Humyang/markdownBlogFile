# 通过其它视图控制器呈现视图控制器

这种呈现视图控制器的能力可以让你中断当前的工作流程并显示新的视图集合。大部分情况下，应用程序呈现视图控制器临时中断工作流程是为了向用户获取重要数据。但是，你也可以使用被呈现的视图控制器使应用程序在特定的时间显示备用界面。

## 视图控制器如何呈现其它视图控制器

被呈现的视图控制器不是 [UIViewController]() 的特殊子类 (如 `UITabBarController` 或 `UINavigationController`)。任意视图控制器都可以通过你的应用程序呈现。例如标签栏和导航控制器，当你想传达之前的视图层次结构和新呈现的视图层次结构之间的关系相关的特定含义时你可以呈现视图控制器。

当你呈现模态视图控制器时，系统会在呈现中的视图控制器和将要被呈现的视图控制器之间创建关系。进一步解释，呈现中的视图控制器会更新它的 [presentedViewController]() 属性指向被呈现的视图控制器。同样，被呈现的视图控制器会更新它的 [presentingViewController]() 属性往回指向呈现它的视图控制器。图 10-1 展示在日历应用程序中管理主屏幕的视图控制器和被用作创建新事件的被呈现视图控制器之间的关系。

**图 10-1** 日历应用程序中被呈现的视图。

![](./connections_made_when_a_controller_is_presented_2x.png)

任何视图控制器对象都可以在同一时间内呈现一个单独的视图控制器，即使视图控制器它们自己就是通过其它视图控制器呈现的也是可以的。换句话说，你可以使被呈现的视图控制器以队列的方式一起出现，根据需要在其它视图控制器的顶部呈现新的视图控制器。图 10-2 展示队列的过程和触发的动作的可视化表现。在这个例子中，当用户点击相机视图中的图标时，应用程序会呈现显示用户照片的视图控制器。点击图像库的工具栏中的动作按钮会提示用户选择相应的动作然后对该动作呈现另一个视图控制器 (人员选取器)。选择联系人后 (或取消人员选择器) 会退出该界面并使用户回到图像库中。点击完成按钮会退出图像库并使用户回到相机界面。

**图 10-2** 创建模态视图控制器的队列
![](./modal_chains.png)

在被呈现的视图控制器的队列中的每个视图控制器都有指向它在队列中周围的其它对象。换句话说，呈现其它视图控制器的被呈现视图控制器的 [presentingViewController]() 和 [presentedViewController]() 属性都拥有有效对象。你可以根据需要使用这些关系追踪通过队列的视图控制器。例如，如果用户取消当前的操作，你可以通过解散第一个被呈现的视图控制器来移除队列中的所有对象。解散视图控制器所解散的不仅仅是视图控制器，也会解散所有由它呈现的视图控制器。

在图 10-2 中，值得提到的一点是被呈现的视图控制器都是导航控制器。你可以呈现 [UINavigationController]() 对象与你将呈现的内容视图控制器使用同一种方式。

当呈现导航控制器时，你需要呈现的总是 `UINavigationController` 对象本身，而不是呈现位于导航堆栈的任意视图控制器。但是，个别位于导航堆栈的视图控制器可能会呈现其它视图控制器，包括导航控制器。图 10-3 展示在前面涉及的例子中更详细的对象。如你所见，人员选择器不是被图像库的导航控制器呈现而是通过导航堆栈 (图像库的导航堆栈) 的其中一个内容视图控制器呈现。

**图 10-3** 模态呈现导航视图控制器

![](./modal_chains_objects_2x.png)

## 模态视图的呈现风格

对于 iPad 应用程序，你可以使用几种不同的风格呈现内容。在 iPhone 应用程序中，被呈现的视图总是会覆盖窗口的可见部分，但当它运行在 iPad 上时，视图控制器会使用它的 [modalPresentationStyle]() 属性中的值决定它们被呈现时的外观。这个属性的不同选项让你呈现的视图控制器可以完全填充屏幕或只填充一部分屏幕。

图 10-4 展示了可用的核心呈现风格。([UIModalPresentationCurrentContext]() 风格使视图控制器遵循它的父辈的风格。)在每个呈现风格中，变暗的区域显示的是底下的内容但不允许点击内容。因此，不像 popover，你所呈现的视图必须拥有一个控件使用户可以退出该视图。

**图 10-4** iPad 的呈现风格

![](./modal_styles_2x.png)

更多使用不同的呈现风格的指导，见 [Modal View]()。

## 呈现视图控制器并选择过渡风格

当使用故事板的 segue 呈现视图控制器时，它会自动实例化并呈现。呈现中的视图控制器可以在目标视图控制器被呈现之前对它进行配置。更多信息，见 [Configuring the Destination Controller When a Segue is Triggered]()。

如果你需要以编程方式呈现视图控制器，你不需做以下的事情：

1.创建你想要呈现的视图控制器。
2.设置视图控制器的 [modalTransitionStyle]() 属性为期望值。
3.对视图控制器分配委托对象。通常，委托是呈现中的视图控制器。这个委托由被呈现的视图控制器使用当它准备消失时通知呈现中的视图控制器。它也可以传达其它信息返回给委托。
4.调用当前视图控制器的 [presentViewController:animated:completion:]() 方法，将你想要呈现的视图控制器传递进去。

`presentViewController:animated:completion:` 方法为指定的视图控制器对象呈现视图并在当前的视图控制器和新的视图控制器之间配置呈现中－被呈现 (presenting-presented) 关系。除了恢复之前的状态，我们一般都想动画新视图控制器的外观。你应该使用的过渡风格取决于你计划如何使用被呈现的视图控制器。表 10-1 列出你可以分配到被呈现的视图控制器的 [modalTransitionStyle]() 属性的过渡风格并且描述了如何使用每一个。

过渡风格  | 用途
------------- | -------------
[UIModalTransitionStyleCoverVertical]()  | 当你想中断当前的工作流程向用户收集信息时使用这个风格。你也可以使用它呈现用户可以或不可以修改的内容。对于这个过渡风格，内容视图控制器应该提供明显的按钮使用户可以退出这个视图控制器。通常这些按钮是取消按钮或完成按钮。如果你没有明确的设置过渡风格，那么这个是默认风格。
[UIModalTransitionStyleFlipHorizontal]()  | 使用这个风格临时更改应用程序的工作模式。这个风格的大部分用途是显示可能频繁更改的设置，例如股票和天气应用程序。这些设置可以应用到整个应用程序或者指定到当前屏幕。对于这个过渡风格，通常会提供一些有序的按钮使用户回到应用程序的正常运行模式。
[UIModalTransitionStyleCrossDissolve]() | 当设备方向更改时使用这个风格呈现备用的界面。在这种情况下，你的应用程序在响应方向更改通知时复杂呈现和解散备用界面。多媒体应用程序也可以使用这个风格淡入屏幕显示多媒体内容。如何实现响应界面方向更改时使用备用界面的例子，见 [Creating an Alternate Landscape Interface](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/RespondingtoDeviceOrientationChanges/RespondingtoDeviceOrientationChanges.html#//apple_ref/doc/uid/TP40007457-CH7-SW14)。

清单 10-1 展示如何以编程方式呈现视图控制器。当用户添加食谱时，应用程序通过呈现一个导航视图控制器提示用户输入关于食谱的基本信息。被选择的是导航视图控制器所以会在标准的位置摆放取消和完成按钮。使用导航控制器也使未来需要拓展新食谱界面时变得轻松。所有你需要做的只是把新的视图控制器推送到导航堆栈中。

清单 10-1 编程方式呈现视图控制器。

```

- (void)add:(id)sender {
   // Create the root view controller for the navigation controller
   // The new view controller configures a Cancel and Done button for the
   // navigation bar.
   RecipeAddViewController *addController = [[RecipeAddViewController alloc]
                       init];
 
   // Configure the RecipeAddViewController. In this case, it reports any
   // changes to a custom delegate object.
   addController.delegate = self;
 
   // Create the navigation controller and present it.
   UINavigationController *navigationController = [[UINavigationController alloc]
                             initWithRootViewController:addController];
   [self presentViewController:navigationController animated:YES completion: nil];
}

```


## Presentation Contenxts 提供被呈现的视图控制器覆盖的区域

## 退出被呈现的视图控制器

## 呈现标准系统视图控制器
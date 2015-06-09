layout: [post]
title: "iOS 笔记 《View Controller Programming Guide for iOS：Responding to Display-Related Notifications》"
date: 2015-06-07 03:48:20
tags: 
- iOS
categories: 
- iOS 开发
- 翻译
id: "VCP5"

---

iOS 视图控制器编程指南：响应与显示相关的通知


<!-- more -->

[原文地址](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007457-CH1-SW1)

记录关于学习过的 iOS 文档

---


# 响应与显示相关的通知

当视图控制器的视图的可见行更改时，视图控制器会调用一些内置的方法通知子类这个更改。你可以重写这些方法重写子类如何应答这些更改。例如，你可以使用这些通知更改状态栏的颜色和方向使它适应将要显示的视图的主题风格。

## 响应视图出现时

图 5-1 展示了一个当视图控制器的视图添加到窗口的视图层次结构时发生的事件顺序。[viewWillAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillAppear:) 和 [viewDidAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidAppear:) 方法给子类机会执行额外的有关视图外观的动作。

**图  5-1** 响应视图的外观

![](./viewappear_process_2x.png)



## 响应视图消失时

图 5-2 展示一个当视图从它的窗口移除时发生的事件顺序。当视图控制器检测到它的视图被隐藏或被删除时，它会调用 viewWillDisappear: 和 viewDidDisappear: 方法给子类机会执行相关的人物。

**图 5-2** 响应视图消失时

![](./viewdissappear_process_2x.png)


## 判断视图的外观为什么更改

有时，知道视图为什么显示和消失是很有用的。例如，你可能想知道视图添加到容器后是否出现或者想知道其它遮挡它的内容移除后它是否会出现。这种特殊的例子通常在使用导航控制器时出现；你的内容控制器的视图可能会因为视图控制器把它放入导航堆栈中而显示或者因为在它之上的控制器从堆栈中弹出而显示。

[UIViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController) 类提供一些方法你的视图控制器可以调用判断为什么外观会发生更改。表 5-1 描述了这些方法和它们的用途。这些方法可以在你所实现的方法 [viewWillAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillAppear:),[viewDidAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidAppear:),[viewWillDisappear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillDisappear:) 和 [viewDidDisappear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidDisappear:) 方法内部被调用。

**表 5-1** 调用方法判断视图的外观为什么更改

方法名称  | 用途
------------- | -------------
[isMovingFromParentViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/isMovingFromParentViewController)  | 在 [viewWillDisappear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillDisappear:) 和 [viewDidDisappear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidDisappear:) 方法内部调用这个方法判断视图控制器的视图的隐藏是否因为视图控制器从它的容器视图控制器移除。
[isMovingToParentViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/isMovingToParentViewController)  | 在 [viewWillAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillAppear:) 和 [viewDidAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidAppear:) 方法内部调用这个方法判断视图控制的视图的显示是否因为视图控制器添加到了容器视图控制器。
[isBeingPresented](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/isBeingPresented)  | 在 [viewWillAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillAppear:) 和 [viewDidAppear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidAppear:) 方法内部调用这个方法判断视图控制器的视图的显示是否因为视图控制器通过其它视图控制器呈现。
[isBeingDismissed](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/isBeingDismissed)  | 在 [viewWillDisappear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewWillDisappear:) 和 [viewDidDisappear:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/viewDidDisappear:) 方法内部调用这个方法判断视图控制器的视图的隐藏是否因为视图控制器已被清退 (dismissed，以上面的被其它视图控制器显示对应，如信息录入完成，返回之前的视图控制器)。


---

[*iOS 笔记 《View Controller Programming Guide for iOS：Introduction》*](../VCP0) 

[*iOS 笔记 《View Controller Programming Guide for iOS：View Controller Basics》*](../VCP1) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Using View Controllers in Your App》*](../VCP2) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Creating Custom Content View Controllers》*](../VCP3)

[*iOS 笔记 《View Controller Programming Guide for iOS：Resource Management in View Controllers》*](../VCP4) 

*iOS 笔记 《View Controller Programming Guide for iOS：Responding to Display-Related Notifications》*

[*iOS 笔记 《View Controller Programming Guide for iOS：Resizing the View Controller's Views》*](../VCP6) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Using View Controllers in the Responder Chain》*](../VCP7) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Supporting Multiple Interface Orientations》*](../VCP8) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Accessibility from the View Controllers's Perspective》*](../VCP9) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Presenting View Controllers from Other View Controllers》*](../VCP10) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Coordinating Efforts Between View Controllers》*](../VCP11) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Enabling Edit Mode in a View Controller》*](../VCP12) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Creating Custom Segues》*](../VCP13) 

[*iOS 笔记 《View Controller Programming Guide for iOS：Creating Custom Container View Controllers》*](../VCP14)



[原文地址](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009503-CH1-SW2)



记录关于学习过的 iOS 文档

[toc]

---

#动画效果
动画效果为你的用户界面不同状态之间提供流体视觉过渡。在 iOS 中，动画效果被广泛的用作重新定位视图，更改视图的尺寸，从视图层次结构移除视图，和隐藏视图。你可以使用动画效果对用户传达反馈或实现有趣的视觉效果。

在 iOS 中，创建复杂的动画效果不需要你写任何绘制代码。这个章节描述的所有动画效果技术是使用 Core Animation 提供的内置支持。你要做的只是触发动画效果并让 Core Animation 处理单个帧的渲染。只需要简单的几行代码就可以创建非常复杂的动画效果。

##什么可以被动画？
UIKit 和 Core Animation 都提供了对动画效果的支持，但每个技术所提供的支持级别都有变化。在 UIKit 中，动画效果是通过使用 [UIView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) 对象完成执行。视图支持的基础动画效果集合覆盖许多常用任务。例如，你可以使用动画更改视图的属性或使用过渡动画效果把一个视图集合替换为另一个。

表 4-1 列出可动画的属性－这些属性有内置的动画效果支持－通过 UIView 类。存在可动画不意味着
动画效果会自动发生。更改这些属性的值正常来说是立刻更新属性 (和视图) 而没有动画效果。需要有动画的更改，你必须从动画块内部更改属性的值。这在 [Animating Property Changes in a View](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW3) 中有描述。

属性 | 你能做的更改
阿萨德 | asd

属性  | 你能做的更改
------------- | -------------
[frame](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/frame)  | 修改这个属性会更改视图的尺寸和相对它的父视图的坐标系统的位置。(如果 transform 属性不包含仿射转换，修改 bounds 或 center 属性作为替代。)
[bounds](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/bounds)  | 修改这个属性更改视图的尺寸。
[center](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/center)  | 修改这个属性更改视图相对于它的父视图的坐标系统的位置。
[transform](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/transform)  | 修改这个属性让视图相对于它的中心点拉伸，旋转，或翻转。使用这个属性的转换效果总是在 2D 空间执行。(要执行 3D 的转换效果，你必须使用 Core Animation 动画视图的层对象。)
[alpha](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/alpha)  | 修改这个属性会渐变更改视图的透明度。
[backgroundColor](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/backgroundColor)  | 修改这个属性更改视图的背景颜色。
[contentStretch](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/contentStretch)  | 修改这个属性变更视图的内容在拉伸填充可用空间时的方式。

以动画过渡视图是让你更改视图层次结构的方式，它在视图层次结构提供的方式之外。尽管你应该使用视图控制器管理简洁的视图层次结构，但许多时候你想替换视图层次结构的所有或一部分。在这种情况下，你可以使用基于视图的过渡效果以动画添加。

在你想执行更复杂的动画效果的地方，或 UIView 类不支持的动画效果，你可以使用 Core Animation 和类的底层的层来创建动画效果。因为视图的层对象是紧密关联在一起的，更改视图的层会影响到视图它自己。使用 Core Animation，你可以为你的视图的层动画以下类型的更改：

- 层的尺寸和位置
- 在执行过渡效果时被用到的中心点
- 为层或它的子层添加 3D 过渡效果
- 从层的层次结构添加或移除层
- 层的相对于兄弟层的 Z 子序。
- 层的影子
- 层的边界 (包含层的边角是否为圆角)
- 调整尺寸操作期间拉伸层的一部分
- 层的不透明度
- 位于层的边界外部的子层的裁剪行为
- 当前的层内容
- 层的光栅化行为

> **说明：** 如果你的视图是自定义层对象－意思是，层对象没有关联的视图－你必须使用 Core Animation 动画它们的更改。

虽然该章节解决少数 Core Animation 行为，它只涉及到从视图的代码中初始化它们。更多完整的关于如何使用 Core Animation 使层动画的信息，见 [*Core Animation Programming Guide*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40004514) 和 *Core Animations Cookbook*。

##在视图中以动画更改属性
为了使用动画改变 [UIView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) 类的属性，你必须在动画效果块内部包裹这些更改。术语动画效果块 (*animation block*) 一般指用于指定动画变化的代码。在 iOS 4 和以后版本，你可以使用块对象创建一个动画效果块。在早期的 iOS 版本，是使用 UIView 类的指定的类方法标记动画效果块的开始和结束。这些技术都支持同样的配置选项和提供同样的控制动画执行的数量。

下面章节的焦点在为了动画更改视图属性而使用的代码。关于如何在视图之间创建动画过渡效果，见 [Creating Animated Transitions Between Views](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW9)。

###使用基于块的方法开始动画效果
在 iOS 4 之后的版本，可以使用基于块的类方法初始化动画效果。这里是几个基于块的方法为动画效果块提供了不同配置的级别。

- [animateWithDuration:animations:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/animateWithDuration:animations:)
- [animateWithDuration:animations:completion:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/animateWithDuration:animations:completion:)
- [animateWithDuration:delay:options:animations:completion:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/animateWithDuration:delay:options:animations:completion:)

因为这些是类方法，这些动画效果块创建后是没绑定到单个视图的。因此，你可以使用这些方法创建涉及多个视图更改的单个动画效果。例如，清单 4-1 的代码用超过一秒的时间淡入一个视图和淡出另一个视图。当这个代码执行时，指定的动画效果立刻在另一个线程开始以避免锁定当前线程或应用程序主线程。

**清单 4-1** 执行简单的基于块的动画效果

```
[UIView animateWithDuration:1.0 animations:^{
        firstView.alpha = 0.0;
        secondView.alpha = 1.0;
}];

```

上面例子中的动画效果只执行一次使用易入易出的动画效果曲线。如果你想更改默认动画效果参数，你必须使用 animateWithDuration:delay:options:animations:completion: 方法执行你的动画效果。这个方法让你自定义下面的动画效果参数：

- 开始动画效果之前的使用延迟
- 动画效果期间使用的定时曲线类型
- 动画效果应该重复的时间数字
- 当动画效果结束后是否应当自动逆转它自己
- 当动画效果正在处理时触摸事件是否应当发出
- 动画效果应当打断这在处理的动画效果或开始之前等待执行完成

其他的  animateWithDuration:animations:completion: 和 animateWithDuration:delay:options:animations:completion: 方法可以指定完后的处理块。你可以使用完成处理为你的应用程序制定动画效果完成后的操作。完成处理也是一种链接单个动画效果为一起的方式。

清单 4-2 显示一个动画效果块的例子它使用完成处理在动画执行完成之后初始化新的动画效果。首先调用 animateWithDuration:delay:options:animations:completion: 设置淡出动画效果并配置一些自定义选项。当动画效果完成时，它的完成处理开始运行并设置第二半动画效果，在延迟之后使视图变淡。

使用完成处理连接多个动画效果的基础方式。

清单 4-2 创建一个动画效果块和自定义选项。

```

- (IBAction)showHideView:(id)sender
{
    // Fade out the view right away
    [UIView animateWithDuration:1.0
        delay: 0.0
        options: UIViewAnimationOptionCurveEaseIn
        animations:^{
             thirdView.alpha = 0.0;
        }
        completion:^(BOOL finished){
            // Wait one second and then fade in the view
            [UIView animateWithDuration:1.0
                 delay: 1.0
                 options:UIViewAnimationOptionCurveEaseOut
                 animations:^{
                    thirdView.alpha = 1.0;
                 }
                 completion:nil];
        }];
}


```

> **重要提示:**修改一个属性的值而属性涉及到的动画效果已经在处理中它不会停止当前的动画效果。反而，当前的动画效果会继续并且会动画成你已分配给属性的新值。


###使用 begin/commit 方法开始动画效果
如果你的应用程序运行在 3.2之前的版本，你必须使用 UIView 的类方法 [beginAnimations:context:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/beginAnimations:context:) 和 [commitAnimations](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/commitAnimations) 定义你的动画效果块。这些方法标记了你的动画效果的开始和结束。在这些方法之间的属性修改会在 commitAnimations 方法调用之后动画成这些新值。在次要的线程执行这些动画效果避免锁定当前线程或应用程序的主线程。

>**说明：**如果你在 iOS 4 或之后的版本，你应该使用基于块的方法动画你的内容。关于如何使用这些方法，见 [Starting Animations Using the Block-based Methods](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW4)。

清单 4-3 显示的代码用作实现与清单 4-1 相同的行为但使用的是 begin/commit 方法。如清单 4-1，这个代码用超过一秒的时间淡出一个视图和淡入一个视图。不管怎样，在这个例子中，你必须使用单独的方法设置动画的长短。

**清单 4-3** 执行一个简单的 begin/commit 动画效果

```
    [UIView beginAnimations:@"ToggleViews" context:nil];
    [UIView setAnimationDuration:1.0];
 
    // Make the animatable changes.
    firstView.alpha = 0.0;
    secondView.alpha = 1.0;
 
    // Commit the changes and perform the animation.
    [UIView commitAnimations];

```

默认情况下，所有可动画的属性在动画效果块内的更改都会被动画。如果你想值动画一些更改其他的不管，使用 [setAnimationsEnabled:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationsEnabled:) 方法暂时禁用动画效果，更改你不想被动画的属性，然后可以调用 setAnimationsEnabled: 在此重新启用动画效果。你可以调用类方法 [areAnimationsEnabled](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/areAnimationsEnabled) 判断动画效果当前是否启用。

> **说明：**更改了一个属性值而属性涉及到的动画效果正在进行处理时不会停止当前的动画效果。相反，动画效果继续执行并动画为你已分配给属性的新值。

####为 Begin/Commit 动画效果配置参数
要为 begin/commit 动画效果块配置动画效果参数，你可以任意使用几个 UIView 的类方法。表 4-2 列出这些方法和描述如何使用它们配置你的动画效果。大多数这些方法只能从 begin/commit 动画效果内部被调用但一些也可以被基于块的动画效果使用。如果你没从动画效果调用这些值方法中的一个，相应的属性会使用默认值。更多关于每个方法关联的默认值，见 [UIView Class Reference](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/doc/uid/TP40006816) 中这些方法的描述。


**表 4-2**配置动画效果块的方法

方法 | 用途 
------------ | ------------- 
[setAnimationStartDate:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationStartDate:) <br /> [setAnimationDelay:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationDelay:) | 使用这些方法中的一个指定执行过程什么时候开始。如果指定的开始时间已经超过 (或延时为 0)，动画效果会尽可能早开始。
[setAnimationDuration:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationDuration:) | 使用这个方法设置执行动画效果的时间周期
[setAnimationCurve:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationCurve:) | 使用这个方法设置动画效果的时间曲线。这个控制动画效果是否执行线性或在某些时候更改速度。
[setAnimationRepeatCount:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationRepeatCount:) <br /> [setAnimationRepeatAutoreverses:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationRepeatAutoreverses:) | 使用这个方法设置动画效果重复时间的数字和每次动画效果的完整周期结束是否反向运行。更多关于使用这些方法的信息，见  [Implementing Animations That Reverse Themselves](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW15)。
[setAnimationDelegate:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationDelegate:) <br />[setAnimationWillStartSelector:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationWillStartSelector:) <br />[setAnimationDidStopSelector:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationDidStopSelector:) | 使用这些方法在动画效果之前或之后执行代码。更多关于使用委托的信息，见 [Configuring an Animation Delegate](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW8)
[setAnimationBeginsFromCurrentState:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationBeginsFromCurrentState:) | 使用这个方法立刻停止当前所有动画效果并从停止的地方开始新的动画效果。如果你传入 NO 到方法中，而不是 YES，新的动画效果不会立刻开始执行直到之前的动画效果停止。

清单 4-4 显示的代码被用作实现与清单 4-2 中的代码的相同的行为但使用 begin/commit 方法。在之前，代码淡出一个视图，等待一秒，然后淡入回来。为了实现动画效果的第二部分，代码设置动画效果委托和和实现停止后的处理方法。这个处理方法然后设置动画效果的第二部分然后运行它。

**清单 4-4** 使用 begin/commit 方法配置动画效果参数

```
// This method begins the first animation.
- (IBAction)showHideView:(id)sender
{
    [UIView beginAnimations:@"ShowHideView" context:nil];
    [UIView setAnimationCurve:UIViewAnimationCurveEaseIn];
    [UIView setAnimationDuration:1.0];
    [UIView setAnimationDelegate:self];
    [UIView setAnimationDidStopSelector:@selector(showHideDidStop:finished:context:)];
 
    // Make the animatable changes.
    thirdView.alpha = 0.0;
 
    // Commit the changes and perform the animation.
    [UIView commitAnimations];
}
 
// Called at the end of the preceding animation.
- (void)showHideDidStop:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
{
    [UIView beginAnimations:@"ShowHideView2" context:nil];
    [UIView setAnimationCurve:UIViewAnimationCurveEaseOut];
    [UIView setAnimationDuration:1.0];
    [UIView setAnimationDelay:1.0];
 
    thirdView.alpha = 1.0;
 
    [UIView commitAnimations];
}



```

####配置动画效果委托
如果你想在动画效果之前或之后立即执行一些代码，你必须关联一个委托对象和为你的 begin/commit 动画效果块设置开始或停止选择器。使用 UIVIew 的类方法 [setAnimationDelegate:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationDelegate:) 设置委托对象和用类方法 [setAnimationWillSelector:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationWillStartSelector:) 和 [setAnimationDidStopSelector:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationDidStopSelector:) 设置开始或停止选择器。在动画效果的期间，动画效果系统在相应的时间调用你的委托对象让你有机会执行你的代码。

你的动画效果委托方法签名需要像下面一样：

```
- (void)animationWillStart:(NSString *)animationID context:(void *)context;
- (void)animationDidStop:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context;

```

两个方法中的参数 *animationID* 和 *context* 都是相同的，是你在动画效果块开始时传递给 beginAnimations:context: 的参数：

- *animationID*－应用程序提供的字符串用来标识一个动画效果。
- *context*－应用程序提供的对象你可以用来传递额外的信息给委托。

setAnimationDidStopSelector: 选择器方法有一个额外的参数－一个布尔值如果是 YES 动画效果会运行完成。如果这个参数的值是 NO，动画效果会过早的被另一个动画效果取消或停止。

>**说明：** 尽管动画效果委托能用在基于块的方法中，但那里通常不需要使用它们。作为代替，把你想在动画效果运行之前的代码放置在块的开始位置和把你想在动画效果运行结束后运行的代码放置在完成处理。


###嵌套动画效果块
你可以通过嵌套额外的动画效果块为动画效果块的一部分分配不同的定时和配置选项。如名称所示，嵌入动画效果块是一个新的动画效果块在已有的动画效果块内部创建。已嵌入的动画效果与它的父动画效果同时开始但与它自己的配置选项运行 (大部分)。默认情况下，已嵌套的动画效果基础它父辈的持续时间和动画曲线但这些选项可以按需要重写。

清单 4-5 的例子说明在整个组中已嵌套的动画效果如何被用来修改定时，持续时间，和一些动画效果的行为。在这个例子中，两个视图开始变淡到完全透明，但 anotherView 对象的透明度在最终隐藏之前会来回一段时间。UiViewAnimationOptionOverrideInheritedCurve 和 UIViewAnimationOptionOverrideInheritedDuration 键在被嵌套的动画效果块中被用作允许来自第一个动画效果的曲线和持续时间值被第二个动画效果修改。如果没有这些键，外部动画效果块的持续时间和曲线值将被用作替代。

**清单 4-5** 拥有不同配置信息的已嵌套动画效果

```
[UIView animateWithDuration:1.0
        delay: 1.0
        options:UIViewAnimationOptionCurveEaseOut
        animations:^{
            aView.alpha = 0.0;
 
            // Create a nested animation that has a different
            // duration, timing curve, and configuration.
            [UIView animateWithDuration:0.2
                 delay:0.0
                 options: UIViewAnimationOptionOverrideInheritedCurve |
                          UIViewAnimationOptionCurveLinear |
                          UIViewAnimationOptionOverrideInheritedDuration |
                          UIViewAnimationOptionRepeat |
                          UIViewAnimationOptionAutoreverse
                 animations:^{
                      [UIView setAnimationRepeatCount:2.5];
                      anotherView.alpha = 0.0;
                 }
                 completion:nil];
 
        }
        completion:nil];


```

如果你使用 begin/commit方法创建动画效果，嵌套的工作与基于块的方法非常相似。每个 beginAnimations:context: 的连续调用内已经打开一个动画效果块创建新的已嵌套动画效果块你可以按需要进行配置。任何你所配置的更改都会在最近打开的动画效果块中应用。所有动画效果块在提交和执行之前闭合和调用 [commitAnimations](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/commitAnimations)。


###实现可逆转的动画效果
当创建与重复记数有关联的可逆动画时，考虑为重复记数指定一个非整数值。对于一个自动逆转的动画效果，每一个动画效果的完整周期涉及到从原始值到新值并再次返回的动画。如果你想动画效果在新值结束，添加 0.5 到重复记数会导致动画效果完成所需求的额外一半的周期使其在新值结束。如果不包含这一半的步骤，你的动画效果将动画到原始值然后迅速紧扣新值，这可能不是你想要的动画效果。


##创建视图之间动画的过渡效果
视图过渡效果帮助你掩盖视图层次结构中突然的变化，关联的有增加，移除，隐藏或显示视图。使用视图过渡效果实现下面类型的变更：

- **更改已存在的视图的子视图可见性。**当你想以相对较小的更改已有的视图时通常选择这个选项。
- **替换视图层次结构中的一个视图为不同的视图。**当你想替换屏幕上所有的或大部分的视图层次结构时通常选择这个选项。


>**重要提示：**视图的过渡效果不能与通过视图控制器启动的过渡效果混淆，例如模态呈现视图控制器或推新的视图控制器到导航堆栈。视图过渡效果只影响到视图层次结构，而视图控制器过渡效果还能更改活跃的视图控制器。因此，对于视图过渡效果，当你启动过渡效果时视图控制器是活跃当过渡效果完成时会保留活跃状态。<br /> 更多关于如何使用视图控制器呈现新的内容，见 [*View Controller Programming Guide for iOS*](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007457)。



###更改视图的子视图

更改视图的子视图使你可以视图适度的修改。例如，你可以添加或移除视图切换父视图的两个不同状态之间。动画效果完成时，显示的是同样的视图但它的内容是不同的。

在 iOS 4 和之后，使用 [transitionWithView:duration:options:animations:completion:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/transitionWithView:duration:options:animations:completion:) 方法开始视图的过渡动画效果。在动画效果块中传递这个方法，只会更改正常的动画与显示，隐藏，添加，或移除子视图相关联的。这个设置的限制动画使视图可以创建动画前和动画后的视图快照版本和在两个图像之间动画，这样更高效。如果你需要动画其他更改，当调用其他方法时你可以包含 [UIVIewAnimationOptionAllowAnimatedContent](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/c/econst/UIViewAnimationOptionAllowAnimatedContent) 选项。包含这个选项防止视图创建快照并直接动画所有更改。

清单 4-6 例子说明如何使用过度动画效果使它看起来像已经添加一个新的文本输入页面。在这个例子中，主视图包含两个嵌入式文本视图。文本视图使用相同的配置，但一个视图可见时其他是总是隐藏。当用户触击按钮创建新页面时，这个方法切换两个视图的可见性，对用户显示一个新的空页面和一个空的文本视图准备接收文本。过渡效果完成之后，视图使用一个私有方法从旧页面保存文本并重置当前隐藏的文本视图使它可以稍后重用。视图然后整理它的指针当用户仍需要其他新页面时它可以做同样的事情。

**清单 4-6**将已存在的文本视图交换为空文本视图

```
- (IBAction)displayNewPage:(id)sender
{
    [UIView transitionWithView:self.view
        duration:1.0
        options:UIViewAnimationOptionTransitionCurlUp
        animations:^{
            currentTextView.hidden = YES;
            swapTextView.hidden = NO;
        }
        completion:^(BOOL finished){
            // Save the old text and then swap the views.
            [self saveNotes:temp];
 
            UIView*    temp = currentTextView;
            currentTextView = swapTextView;
            swapTextView = temp;
        }];
}

```

如果你需要在 iOS 3.2 或更早执行视图过渡效果，你可以使用 [setAnimationTransition:forView:cache:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/setAnimationTransition:forView:cache:) 方法为转换效果指定参数。你传递给这个方法的视图与传递给 transitionWithView:duration:options:animations:completion: 方法的第一个参数是相同一个。清单 4-7 显示你所需要创建的动画效果块的基础结构。注意实现完整的块在清单 4-6 中显示，你将需要配置一个动画效果委托与未停止的处理，在 [Configuring an Animation Delegate](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW8) 中描述。

**清单 4-7**使用 begin/commit 方法更改子视图

```
    [UIView beginAnimations:@"ToggleSiblings" context:nil];
    [UIView setAnimationTransition:UIViewAnimationTransitionCurlUp forView:self.view cache:YES];
    [UIView setAnimationDuration:1.0];
 
    // Make your changes
 
    [UIView commitAnimations];



```


###将视图替换成不同视图
有时候你想界面发生显著变化可以使用替换视图。因为这个技术只交换视图 (不是视图控制器)，你负责适当的设计你的应用程序的控制器对象。这个技术使用一些标准的过渡效果用简单的方式呈现新的视图。

在 iOS 4 和之后，使用 [transitionFromView:toView:duration:options:completion:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/transitionFromView:toView:duration:options:completion:) 方法使两个视图之间过渡。这个方法实际上从你的视图层次结构移除第一个视图然后插入其他的，因此如果你想保持第一个视图你应该确认已有对它的引用。如果你想使用隐藏视图替代从视图层次结构移除它们，传入 [UIViewAnimationOptionShowHideTransitionViews](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/c/econst/UIViewAnimationOptionShowHideTransitionViews) 键作为选项之一。

清单 4-8 的代码用作在两个被单个视图控制器管理的主视图之间交换。在这个例子中视图控制器的根视图总是显示两个子视图中的一个 (primaryView 或 secondaryView)。每个视图都呈现同样的内容但都以不同的方式。视图控制器使用成员变量 displayingPrimary (一个布尔值) 追踪视图在给定时间内是否显示。根据视图是否显示更改翻转方向。

**清单 4-8**在视图控制器中切换两个视图之间

```

- (IBAction)toggleMainViews:(id)sender {
    [UIView transitionFromView:(displayingPrimary ? primaryView : secondaryView)
        toView:(displayingPrimary ? secondaryView : primaryView)
        duration:1.0
        options:(displayingPrimary ? UIViewAnimationOptionTransitionFlipFromRight :
                    UIViewAnimationOptionTransitionFlipFromLeft)
        completion:^(BOOL finished) {
            if (finished) {
                displayingPrimary = !displayingPrimary;
            }
    }];
}

```

>**说明：**除了换出视图外，你的视图控制器代码还需要管理主视图和次视图的读取和卸载。关于如何使用视通控制器加载和卸载视图，见 [*View Controller Programming Guide for iOS*](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007457)。


##连结多个动画效果到一起
UIView 的动画效果接口提供连结单个动画效果块的支持使它们能按顺序执行代替同时执行。连结动画效果块的处理取决于你是使用基于块的动画效果方法或 begin/commit 方法：

- 对使用基于块的动画效果，使用 [animateWithDuration:animations:completion:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/animateWithDuration:animations:completion:) 和[animateWithDuration:delay:options:animations:completion:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/clm/UIView/animateWithDuration:delay:options:animations:completion:) 方法提供的完成处理执行任意后续动画效果。
- 对使用 begin/commit 动画效果，为动画效果 对动画效果分配一个委托对象和一个 did-stop 选择器。关于如何分配委托对象到你的动画效果中，见 [Configuring an Animation Delegate](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW8)。

一个替代连结多个动画效果到一起的方式是使用嵌套动画效果设置不同的延迟系数使得在不同时间开始动画效果。更多关于如何嵌套动画效果的信息，见 [Nesting Animation Blocks](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW11)。

##一起更改视图和层的动画

应用程序可以按需要自由的混合基于视图和基于层的动画效果代码但动画效果参数的配置处理取决于谁拥有该层。更改视图拥有的层等于更改视图它自己，任何你对层的属性应用的动画效果都遵从当前基于视图的动画效果块的动画效果参数。对于你自己创建的层这说法不成立。自定义层对象基于视图的动画效果块参数并使用默认的核心动画效果参数作为替代。

如果你想为你创建的层自定义动画效果参数，你必须直接使用 Core Animation。通常，使用 Core Animation 制作层动画涉及到创建 [CABasicAnimation](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CABasicAnimation_class/index.html#//apple_ref/occ/cl/CABasicAnimation) 对象或 [CAAnimation](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CAAnimation_class/index.html#//apple_ref/occ/cl/CAAnimation) 的具体子类。然后添加动画效果到相应的层。你可以从基于视图的动画效果块的外部或内部应用这个动画效果。

清单 4-9 显示的一个在同一时间修改视图和自定义层的动画效果。这个例子中的视图在它的边界的中心包含一个自定义的 CALayer 对象。这个动画选过以逆时针旋转视图而以顺时针旋转层。因为是以相反方向旋转，层保存相对屏幕的原始方向并且不会出现明显旋转。这个例子主要演示如何混合视图和层的动画效果。这个类型的混合不应该用在需要精确定时的情景下。

**清单 4-9** 混合视图的层动画效果

```

[UIView animateWithDuration:1.0
    delay:0.0
    options: UIViewAnimationOptionCurveLinear
    animations:^{
        // Animate the first half of the view rotation.
        CGAffineTransform  xform = CGAffineTransformMakeRotation(DEGREES_TO_RADIANS(-180));
        backingView.transform = xform;
 
        // Rotate the embedded CALayer in the opposite direction.
        CABasicAnimation*    layerAnimation = [CABasicAnimation animationWithKeyPath:@"transform"];
        layerAnimation.duration = 2.0;
        layerAnimation.beginTime = 0; //CACurrentMediaTime() + 1;
        layerAnimation.valueFunction = [CAValueFunction functionWithName:kCAValueFunctionRotateZ];
        layerAnimation.timingFunction = [CAMediaTimingFunction
                        functionWithName:kCAMediaTimingFunctionLinear];
        layerAnimation.fromValue = [NSNumber numberWithFloat:0.0];
        layerAnimation.toValue = [NSNumber numberWithFloat:DEGREES_TO_RADIANS(360.0)];
        layerAnimation.byValue = [NSNumber numberWithFloat:DEGREES_TO_RADIANS(180.0)];
        [manLayer addAnimation:layerAnimation forKey:@"layerAnimation"];
    }
    completion:^(BOOL finished){
        // Now do the second half of the view rotation.
        [UIView animateWithDuration:1.0
             delay: 0.0
             options: UIViewAnimationOptionCurveLinear
             animations:^{
                 CGAffineTransform  xform = CGAffineTransformMakeRotation(DEGREES_TO_RADIANS(-359));
                 backingView.transform = xform;
             }
             completion:^(BOOL finished){
                 backingView.transform = CGAffineTransformIdentity;
         }];
}];

```

>**说明：**在清单 4-9 中，你也可以在基于视图的动画效果块外部创建和应用 CABasicAnimation 对象实现同样的结果。所有的动画效果最终依靠 Core Animatino 为它们执行。因此，如果它们提交的时间大约相同，它们会一起运行。


---


系列文章

[*iOS 笔记 《View Programming Guide for iOS：Introduction》*](http://blog.csdn.net/yangxionggui/article/details/45178793) 

[*iOS 笔记 《View Programming Guide for iOS：View and Window Architecture》*](http://blog.csdn.net/yangxionggui/article/details/45179765)

[*iOS 笔记 《View Programming Guide for iOS：Windows》*](http://blog.csdn.net/yangxionggui/article/details/45179821)

[*iOS 笔记 《View Programming Guide for iOS：Views》* ](http://blog.csdn.net/yangxionggui/article/details/45179955)

*iOS 笔记 《View Programming Guide for iOS：Animations》*

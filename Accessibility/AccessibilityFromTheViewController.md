# 视图控制器角度的无障碍访问

除了管理视图的行为，视图控制器也协助控制应用程序的无障碍访问。无障碍访问的应用程序让每个人都可以使用，无论使用者是残疾人士或者身体功能障碍，它作为帮助工具保持应用程序的功能和可用性。

要实现无障碍访问，iOS 应用程序必须提供关于它的用户界面元素的信息给 VoiceOver 用户。VoiceOver,是一个屏幕阅读技术被设计来协助阅读障碍人士，可以大声念出在屏幕上显示的文本，图像，和 UI 控件，使有阅读障碍的人士仍然能与这些元素交互。UIKit 对象默认可以无障碍访问，但仍然需要你在视图控制器的角度做一些事情以解决无障碍访问。意味着你需要确保以下的实现：

* 每个可以与用户交互的用户界面元素都是可访问的。包含仅提供信息的元素，例如静态文本，以及执行动作的控件。
* 所有可访问的元素提供准确和有帮助的信息。

除了这些基本方面，还可以通过编程方式设置 VoiceOver 的对焦环，响应特定的 VoiceOver 手势，和观察无障碍通知，提高 VoiceOver 的用户体验。


## 移动 VoiceOver Cursor 到特定元素

当屏幕的布局变更时，VoiceOver 的对焦环，也称为 *VoiceOver cursor*,会重置它的位置到显示在屏幕上从左到右，从上到下的第一个元素。当视图被呈现在屏幕上时你可以决定更改 VoiceOver cursor 所在的第一个元素。

例如，当导航控制器推送一个视图控制器到导航堆栈时，VoiceOver cursor 会落在导航栏的返回按钮上。根据你的应用程序的不同，移动到导航栏的标题可能更有意义，或者其它元素。 

要想这样做，调用 [UIAccessibilityPostNotification]() 使用通知 [UIAccessibilityScreenChangedNotification]() (它告示 VoiceOver 屏幕的内容已发生变更) 和你想聚焦的元素，如在清单 9-1 所示。

**清单 9-1** 投递一个无障碍访问通知更改第一个念出的元素

```

@implementation MyViewController
- (void)viewDidAppear:(BOOL)animated
{
    [super viewDidAppear:animated];
 
    UIAccessibilityPostNotification(UIAccessibilityScreenChangedNotification,
                                    self.myFirstElement);
}
@end


```

如果只是布局的更改而不是屏幕的内容更改，例如从竖屏切换到横屏，使用通知 [UIAccessibilityLayoutChangeNotification]() 替代 `UIAccessibilityScreenChangeNotification`。

> **说明：**设备方向旋转会触发布局更改，这会重置 VoiceOver cursor 的位置。

## 响应特定的 VoiceOver 手势

这里有几个 VoiceOver 的特殊手势用户可以执行触发自定义动作。这些手势是特殊的因为你可以定义它们的行为，不像标准 VoiceOver 手势。你可以通过在视图控制器重写默写方法进行手势检测。

手势会首先为指令检查拥有 VoiceOver cursor 的视图并沿着响应者队列寻找直到找到相应的 VoiceOver 手势方法。如果没找到实现方法，手势会触发系统默认动作。例如，Magic Tap 手势可以在音乐应用的播放列表播放和暂停音乐如果没有在应用程序委托找到当前视图的 Magic Tap 实现。

尽管你可以提供任意你想要的自定义逻辑，但 VoiceOver 用户期望这些特殊动作能够遵循某些方针，你的 VoiceOver 手势的实现应该遵循下面的形式使用户可以直观的与无障碍访问应用程序进行交互。

这里是五个特殊 VoiceOver 手势：

* **Escape。**两个手指以 Z 字形拖动使模态窗口退出，或者在导航层次结构退后一级。
* **Magic Tap。**两个手指双击执行最有意义的动作。
* **Three-Finger Scroll。**三根手指轻扫使垂直或水平内容滚动。
* **Increment and Decrement。**一个手指往上或往下轻扫添加从可调整的元素中增加或减去给定的值。可调整的无障碍访问元素必须实现这些方法。

> **说明：**所有特殊 VoiceOver 手势方法会返回一个布尔值用来决定是否通过响应链传播。要停止传播，返回 YES；否则，返回 NO。

### Escape

如果你呈现的视图覆盖了内容－例如模态窗口或弹出提示，你应该重写 [accessibilityPerformEscape]() 方法使覆盖视图退出。Escape 手势的功能与电脑键盘上 `ESC` 键的功能类似；它可以取消临时对话框或标签使主内容显示。

重写 Escape 手势的另一个使用情况是返回导航层次结构的上一级。`UINavigationController` 默认实现了这个方法。如果你在设计实现自己的导航控制器，你应该设置 Escape 手势使它可以向上遍历你的导航堆栈中的一级，因为这个是 VoiceOver 用户期望的功能。

### Magic Tap

Magic Tap 的目的是快速执行一些常用的或最有意义的动作。例如，在图像应用程序，它可以接听或挂断电话。在时钟应用程序，它可以开始和停止秒表。如果你想动作从一个手势发出不管 VoiceOver cursor 是否位于视图上，你应该在你的视图控制器实现 [accessibilityPerformMagicTap]() 方法。

说明：如果你不喜欢 Magic Tap 手势执行与应用程序内其它位置相同的动作，最适当的方式是在应用程序委托实现 `accessibilityPerformMagicTap` 方法。

### Three-Finger Scroll

当 VoiceOver 用户执行三根手指滚动时会触发 [accessibilityScroll:]() 方法。它会接收 [UIAccessibilityScrollDirection]() 参数你可以用来判断滚动的方向。如果你有自定义的滚动视图，可能更适合在视图自身实现这个方法。

### Increment and Decrement

可调整数值的元素需要实现 [accessibilityIncreament]() 和 [accessibilityDecrement]() 方法并且应该在视图自身中实现。

## 观测无障碍访问通知

你可以监听无障碍访问通知并触发回调函数。某些情况下，UIKit 会发出无障碍访问通知，你的应用程序可以观测这些通知拓展它的访问功能。

例如，如果你监听了 [UIAccessibilityAnnouncementDidFinishNotification]() 通知，你可以触发一个方法在 VoiceOver 的语音结束跟进。Apple 在 iBooks 应用程序实现了这个功能。当 VoiceOver 完成书中一行的阅读时 iBooks 会发出通知触发下一行的阅读。如果是本页的最后一行，回调中的逻辑会告诉 iBooks 让它翻页并继续阅读一旦到达文章最后一行就结束阅读。这种一行接一行程度的粒度使得在导航文本的同时为用户提供了无缝，不间断的阅读体验。

要注册为无障碍访问通知观测者，可以使用默认的通知中心。然后创建一个与你为 `selector` 参数提供的同名方法，如清单 9-2 所示。

**清单 9-2**注册成为无障碍访问通知观测者。

```

@implementation MyViewController
- (void)viewDidLoad
{
    [super viewDidLoad];
 
    [[NSNotificationCenter defaultCenter]
        addObserver:self
           selector:@selector(didFinishAnnouncement:)
               name:UIAccessibilityAnnouncementDidFinishNotification
             object:nil];
}
 
- (void)didFinishAnnouncement:(NSNotification *)dict
{
    NSString *valueSpoken = [[dict userInfo] objectForKey:UIAccessibilityAnnouncementKeyStringValue];
    NSString *wasSuccessful = [[dict userInfo] objectForKey:UIAccessibilityAnnouncementKeyWasSuccessful];
    // ...
}
@end


```

`UIAccessibilityAnnouncementDidFinishNotification` 预计会有一个 [NSNotification]() 字典作为参数你可以判断语音的值并判断是否已经不间断的说完。语音可能会中断，如果 VoiceOver 使用者执行停止语音手势或者在公告完成之前划到另一个元素。

另一个有用的通知订阅是 [UIAccessibilityVoiceOverStatusChanged]()。它可以检测 VoiceOver 的打开或关闭切换。如果 VoiceOver 在应用程序的外部切换，当你的应用程序重新回到前台时会接收到这个通知。因为 `UIAccessibilityVoiceOverStatusChanged` 不期望接收任何参数，所以在 selector 中的方法不需要附加冒号 (`:`)。

需要可观测的完整通知列表，可以阅读 [*UIAccessibility Protocol Reference*]() 中的 "Notifications"。记住你能观测的只有可以被 UIKit 投递的通知，它是 `NSString` 对象，不能通过你的应用程序投递的通知，它们是 `int` 类型。


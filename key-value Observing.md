[原文地址](http://nshipster.com/key-value-observing/)

# Key-Value Observing 

> NSNotification & NSNotificationCenter 可以在应用程序的任意位置通知其它到应用程序的其它位置，只需要知道事件的名称。
> KVO 的主要理念：任何对象可以订阅成任意其它对象状态更改时被通知。

Ask anyone who's been around the NSBlock a few times: Key-Value Observing has the worst API in all of Cocoa. It's awkward, verbose, and confusing. And worst of all, its terrible API belies one of the most compelling features of the framework.

When dealing with complicated, stateful systems, dutiful book-keeping is essential for maintaining sanity. Lest the left hand not know what the right hand doeth, **objects need some way to publish and subscribe to state changes over time**.

In Objective-C and Cocoa,** there are a number of ways that these events are communicated**, each with varying degrees of formality and coupling:

* **NSNotification & NSNotificationCenter** provide a centralized hub through which any part of an application may notify and be notified of changes from any other part of the application. The only requirement is to know **what to look for**, specifically in the **name of the notification. For example**, `UIApplicationDidReceiveMemoryWarningNotification` signals a low memory environment in an application.
* **Key-Value Observing** allows for **ad-hoc**, evented introspection between specific object instances by listening for changes on a particular key path. For example, a UIProgressView might observe the numberOfBytesRead of a network request to derive and update its own progress property.
* **Delegates** are a popular pattern for signaling events over a fixed set of methods to a designated handler. For example, UIScrollView sends scrollViewDidScroll: to its delegate each time its scroll offset changes.
* **Callbacks** of various sorts, whether block properties like NSOperation -completionBlock, which trigger after isFinished == YES, or C function pointers passed as hooks into functions like SCNetworkReachabilitySetCallback(3).

Of all of these methods, Key-Value Observing is arguably the least well-understood. So this week, NSHipster will endeavor to provide some much-needed clarification and notion of best practices to this situation. To the casual observer, this may seem an exercise in futility, but subscribers to this publication know better.

---

<NSKeyValueObserving>, or KVO, is an informal protocol that defines a common mechanism for observing and notifying state changes between objects. As an informal protocol, you won't see classes bragging about their conformance to it (it's just implicitly assumed for all subclasses of NSObject).

The main value proposition of KVO is rather compelling: **any object can subscribe to be notified about state changes in any other object. Most of this is built-in, automatic, and transparent**.

## Subscribing

> `- addObserver:forKeyPath:options:context:`，
> addObserver: 注册 KVO 通知的对象，这个对象必须实现 key-value 观察方法 `observeValueForKeyPath:ofObject:change:context:`
> forKeyPath: 相对于接受者，需要观察的 property。必须不能为 nil。
> options: `NSKeyValueObservingOptions` 值的组合，指定观察通知中要包含什么。NSKeyValueObservingOptions 中有说明
> context: 数据会传入 `observeValueForKeyPath:ofObject:change:context:`
> 
> 

Objects can have observers added for a particular key path, which, as described in the KVC operators article, are dot-separated keys that specify a sequence of properties. Most of the time with KVO, these are just the top-level properties on the object.

The method used to add an observer is –addObserver:forKeyPath:options:context::

```

- (void)addObserver:(NSObject *)observer
         forKeyPath:(NSString *)keyPath
            options:(NSKeyValueObservingOptions)options
            context:(void *)context

```

* **observer:** The object to register for KVO notifications. The observer must implement the key-value observing method `observeValueForKeyPath:ofObject:change:context:`.
* **keyPath:** The key path, relative to the receiver, of the property to observe. This value must not be nil.
* **options:** A combination of the NSKeyValueObservingOptions values that specifies what is included in observation notifications. For possible values, see "NSKeyValueObservingOptions".
* **context:** Arbitrary data that is passed to observer in observeValueForKeyPath:ofObject:change:context:.

Yuck. What makes this API so unsightly is the fact that those last two parameters are almost always 0 and NULL, respectively.

options refers to a bitmask of NSKeyValueObservingOptions. Pay particular attention to NSKeyValueObservingOptionNew & NSKeyValueObservingOptionOld as those are the options you'll most likely use, if any. Feel free to skim over NSKeyValueObservingOptionInitial & NSKeyValueObservingOptionPrior:



### NSKeyValueObservingOptions
## Responding
### Correct Context Declarations
### Better Key Paths
## Unsubscribing
### Safe Unsubscribe with @try / @catch
## Automatic Property Notifications

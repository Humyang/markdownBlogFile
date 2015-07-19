plainRead 源码阅读


# 单例

该代码内很多地方用到了 singleton，所以需要了解一下。

> 资料来源：http://blog.csdn.net/iukey/article/details/7339844。

> 单例模式的思路是一个类能返回对象一个实例（永远是同一个）和一个获得该实例的方法（必须是静态方法，通常使用 getInstance这个名称）；当我们调用这个方法时，如果类持有的实例不为空，就返回这个实例；如果类保持的实例为空，就创建该类的实例，并将实例赋予该类保持的实例，从而限制用户只有通过该类提供的静态方法来得到该类唯一的实例。
> 单例模式在多线程场合下必须小心使用。当唯一的实例未创建时，如果有两个线程同时调用创建方法，那么他们同时没有检测到唯一的实例存在，从而同时各自创建了一个实例，这样就有两个实例被创建出来，从而违反了单例模式中实例唯一的原则。解决这个问题的办法是为标记类是否已经实例化的变量提供一个互斥锁（虽然这样会降低效率）。

一些用到单例的源代码：

```

[[AFNetworkReachabilityManager sharedManager] startMonitoring];

[[CWObjectCache sharedCache] setMaxCacheSize:1024 * 1024 * 256];

 [[UIApplication sharedApplication] setStatusBarHidden:NO withAnimation:UIStatusBarAnimationFade];

```

## 进阶写法

资料来源：http://justsee.iteye.com/blog/1819556

实现单例方法时很容易出现不安全线程，要解决会使用很繁琐的方式，但是使用下面的方式可以简便的创建单例，并且是线程安全的

```

+(SchoolManager *)sharedInstance  
{  
    __strong static SchoolManager *sharedManager;  
      
    static dispatch_once_t onceToken;  
    dispatch_once(&onceToken, ^{  
        sharedManager = [[SchoolManager alloc] init];  
    });  
      
    return sharedManager;  
}  

```

函数void dispatch_once( dispatch_once_t *predicate, dispatch_block_t block);其中第一个参数predicate，该参数是检查后面第二个参数所代表的代码块是否被调用的谓词，第二个参数则是在整个应用程序中只会被调用一次的代码块。dispach_once函数中的代码块只会被执行一次，而且还是线程安全的。

## 用宏实现单例

看到如下一篇文章，用宏实现（https://gist.github.com/lukeredpath/1057420）：

ExampleClass.m:

```
@implementation MySharedThing
 
+ (id)sharedInstance
{
DEFINE_SHARED_INSTANCE_USING_BLOCK(^{
return [[self alloc] init];
});
}
 
@end
```

GCDSingleton.h:

```

#define DEFINE_SHARED_INSTANCE_USING_BLOCK(block) \
static dispatch_once_t pred = 0; \
__strong static id _sharedObject = nil; \
dispatch_once(&pred, ^{ \
_sharedObject = block(); \
}); \
return _sharedObject; \

```


# 离线阅读部分

```
     /*
     离线阅读(NSURLProtocol + NSURLCache + CWObjectCache + SQLite3)
     
     NSURLRequestUseProtocolCachePolicy。一个Request的默认缓存策略。他是最为隐晦的，我们最后说。
     NSURLRequestReturnCacheDataElseLoad。如果在之前的网络请求中，我们获取了Cache的Response，那么本次请求同样的接口，就直接从Cache中去抓。反之，从服务器上去获取，并在本地cache起来。(这里的问题是，如果cache存在，就一直拉不到服务器数据，所以用这个机制，有它的弊端，当然也要看具体的需求情况。可以通过自定义自类化NSURLCache来自定义cache time，但也仅仅适用于Data改变不大的项目中。)
     NSURLRequestReturnCacheDataDontLoad。只从缓存中获取cached response而不从服务器获取。实属离线版本。
     
     设置URL缓存
     调用 sharedURLCache 方法之前需要调用这个方法，设置共享的 NSURLCache 实例到指定的缓存对象
     
     PRURLCache 从 NSURLCache 继承，重写了 cachedResponseForRequest
     
     */
     
    [NSURLCache setSharedURLCache:[[PRURLCache alloc] init]];
    //注册到 NSURLProtocol 协议中，使它可以在 URL loading system 中可见

    /* 2015年7月14日 19:32
     NSURLProtocol is an abstract class that provides the basic structure for performing protocol-specific loading of URL data.
     Concrete subclasses handle the specifics associated with one or more protocols or URL schemes.
     */
    [NSURLProtocol registerClass:[PRHTTPURLProtocol class]];
    [NSURLProtocol registerClass:[PRCustomURLProtocol class]];
    
```

上面的代码在 appdelegate 初始化时调用，不注册了类之后会在哪里起作用，也不知道 NSURLCache 和 NSURLProtocol 之间的关系。

所以先去看看文档中给出的指南 [URL Loading System Programming Guide](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html#//apple_ref/doc/uid/10000165-BCICJDHA)。

## URL Loading System Programming Guide

### URL loading System 的定义

> 一系列与服务器通过 URL 交互和使用标准协议与服务器通信的 Foundation framework class 被称为 URL loading system。
> URL loading system 是一系列类和协议的集合，使你的应用程序可以访问 URL 引用的内容，技术核心是 NSURL，它可以让你的应用程序操作 URL 和它指向的资源。
> Foundation framework 提供了丰富的类集合供你加载 URL 的资源，上传数据到服务器，管理 cookie 存储，控制 response caching，处理凭据和特定的应用程序认证方式，还可以**自定义拓展协议**。
> 它还使用用户的系统偏好设置透明的支持代理服务器和 SOCKS 网关。

This guide describes the Foundation framework classes available for interacting with URLs and communicating with servers using standard Internet protocols. Together these classes are referred to as the *URL loading system*.

The URL loading system is a set of classes and protocols that allow your app to access content referenced by a URL. At the heart of this technology is the **NSURL** class, which lets your app manipulate URLs and the resources they refer to.

To support that class, the Foundation framework provides a rich collection of classes that let you load the contents of a URL, upload data to servers, manage cookie storage, control response caching, handle credential storage and authentication in app-specific ways, and write custom protocol extensions.

The URL loading system provides support for accessing resources using the following protocols:

* File Transfer Protocol (ftp://)
* Hypertext Transfer Protocol (http://)
* Hypertext Transfer Protocol with encryption (https://)
* Local file URLs (file:///)
* Data URLs (data://)

It also transparently supports both proxy servers and SOCKS gateways using the user’s system preferences.

### URL Loading 

可以以多种方式获取 URL 的内容，OS X 和 iOS 所使用的 API 有区别。

* iOS 7 和 OS X v10.9 之后，新代码推荐使用 NSURLSession，它拥有完美的 API。
* 需要支持老版本的 OS X，可以使用 NSURLDownload 下载 URL 的内容到磁盘文件。
* 需要支持老版本的 iOS 或 OS X，可以使用 NSURLConnection 下载 URL 的内容到内存中，你可以根据需要把它们写入磁盘。

#### Fetching Content as Data (In Memory)

在高级别，有两种基本方法获取 URL 数据：

* 对于简单的 request，使用 NSURLSession API 直接从 NSURL 对象获取内容，保存为 NSData 对象或者磁盘文件。
* 更复杂的 request，需要上传数据的请求，例如提供 NSURLRequest 对象 (或则它的 mutable 子类，NSMutableURLRequest)到 NSURLSession 或 NSURLConnection。

不管你选择哪个方法，你的应用程序可以以两种方式获取响应数据：

* 提供 completion handler block，当从服务器完成获取数据时会调用该方法。
* 提供自定义 delegate，URL loading class 定期调用你的 delegate 方法接收从地址获取的数据，你的应用程序负责积累数据，根据需要。

除了数据本身，URL loading class 还提供封装了与请求相关联的元数据的响应对象的 delegate 或 completion handler block，例如 MIME 类型和 content length。

#### Downloading Content as a File

### Helper Classes

URL loading class 使用了两个辅助类提供额外的元数据，一个负责请求 (NSURLRequest) 另一个负责服务器的响应 (NSURLResponse)。

#### URLRequest

NSURLRequest 对象封装了 URL 和任何协议指定的属性，以一种“与协议无关的方式”。

它还指定了使用本地缓存数据的策略，当使用了 `NSURLConnection` 或 `NSURLDownload`，还提供了借口设置超时时间。(对于 NSURLSession，超时时间基于每个 session 设置。)

> 当客户端应用程序使用 NSMutableURLRequest 的实例初始化 connection 或 download 时，request 会发生 deep copy。

一些协议支持协议特定的属性。例如，HTTP 协议添加了方法到 NSURLRequest 使它返回 HTTP request body，headers，和 transfer method。它也添加了方法到 NSMutalbleURLRequest 设置这些值。

#### Response Metadata

服务器对请求的响应可以被看作两部分：描述内容的元数据和内容数据它本身。大部分协议都拥有元数据，它封装在 NSURLResponse 类中，由 MIME type，content length，text encoding，负责响应的 URL 组成。协议具体的子类 `NSURLResponse` 可以提供额外的元数据。例如，NSHTTPURLResponse 保存着由服务器返回的 header 和 status code。

> 重要提示：NSURLResponse 对象只保存着与响应相关的元数据。各个 URL loading class 使用对象的 delegate 或 completion handler block 对你的应用程序提供响应数据。
> NSCacheURLResponse 实例封装了 NSURLResponse 对象，URL 内容数据，和对你的应用程序提供任意额外的信息。见 [Cache Management](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html#//apple_ref/doc/uid/20001834-155585) 更多细节。

#### Redirection and Other Request Changes


#### Authentication and Credentials

一些服务器会限制访问某些内容，需要用户提供一些认证手段：客户端证书，用户名和密码，等等。证书也可以让应用程序使用决定是否信任该服务器。

URL loading system 提供了 model creadentials 和 protected areas 的类以及提供了 secure creadential persistence。你的应用程序可以为单个 request，程序运行期间，指定这些 credentials persist，还可以在钥匙孔永久的使用。

NSURLCredential 类封装了认证信息的凭据(例如用户名和密码)和持久性。NSURLProtectionSpace 类代表需要特定凭据的区域。protection space 可以为一个单个 URL，或服务器，或引用代理。
NSURLCredentialStorage 类的共享实例管理着凭据的保存和提供 NSURLCredential 对象的 mapping 给相应的 NSURLProtectionSpace 对象为需要特定凭据的区域提供认证。

NSURLAuthenticationChallenge 类封装了需要由 NSURLProtocol 实现的认证请求信息：proposed credential，涉及的 protection space，被用作对所需要的认证的内容判断的协议进行响应或错误处理，和已取得的认证尝试数目。NSURLAuthenticationChallenge 实例也指定初始认证的对象。初始对象简称为 `sender`，它必须符合 NSURLAuthenticationChallengeSender 协议。

NSURLAuthenticationChallenge 的实例会被 NSURLProtocol 子类使用通知需要认证的 URL loading system。它们也提供方便定制认证处理的 NSURLConnection 和 NSURLDownload 的 delegate 方法。

#### Cache Management

URL loading system 提供了混合的方式在磁盘和内存缓存内容减轻网络的依赖和提供快速响应已缓存的内容。缓存基于每个应用程序程序保存。缓存由 NSURLConnection 查询，由初始化的 NSURLRequest 对象指定缓存策略。

NSURLCache 类提供方法配置缓存的尺寸和本地磁盘位置。它也提供方法管理包含了已缓存的 responses 的 NSCachedURlResponse 对象的集合。

NSCachedURLResponse 对象封装了 NSURLResponse 对象和 URL content data。NSCachedURLResponse 也提供让你的应用程序缓存任意自定义数据的用户信息字典。

不是所有协议都实现了 response caching 的支持。当前只有 http 和 https 请求可以被缓存。

NSURLConnection 对象可以控制 response 是否可以被缓存和 response 是否应该只缓存在内存中，可以通过实现 connection:willCacheResponse: 委托方法实现。



#### Cookie Storage

#### Protocol Support

URL loading system 原生支持 http，https，file，ftp 和 data 协议。URL loading system 也允许你的应用程序注册你自己的类支持额外的 application-layer networking protocols。

# 界面结构部分

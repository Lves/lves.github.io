![scenedelegate_logo.jpg](https://upload-images.jianshu.io/upload_images/1159872-89194fed91cdda3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


iOS13 项目中的`SceneDelegate`类有什么作用？自从Xcode11发布以来，当你使用新XCode创建一个新的iOS项目时，`SceneDelegate`会被默认创建，它到底有什么用呢。

在本文中，我们将深入探讨iOS 13和Xcode 11的一些变化。我们将重点关注SceneDelegate和AppDelegate，以及它们如何影响SwiftUI、Storyboard和基于XIB的UI项目。

通过阅读本文你将了解到:

- SceneDelegate和AppDelegate的新变化
- 他们是如何合作引导你的app启动的
- 在纯手写App中使用`SceneDelegate`
- 在Storyboards 和 SwiftUI项目中使用`SceneDelegate`

让我们开始吧。

> 本篇文章基于 Xcode 11 和 iOS 13.

### AppDelegate

你可能对AppDelegate已经熟悉，他是iOS app的入口，`application(_:didFinishLaunchingWithOptions:)`是你的app启动后系统调用的第一个函数。

`AppDelegate`类实现了UIKit库中的`UIApplicationDelegate` 协议。而到了iOS13 `AppDelegate`的角色将会发生变化，后面我们会详细讨论。

下面是你在iOS12中一般会在AppDelegate中做的事情：

- 创建app的第一个view controller也就是 rootViewController
- 配置并启动一些像日志记录和云服务之类的组件
- 注册推送通知处理程序，并响应发送到app的推送通知
- 响应应用程序生命周期事件，例如进入后台，恢复应用程序或退出应用程序（终止）

iOS12及以前，使用Storyboards的app，AppDelegate很简单。 像这样：

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool
{
    return true
}
```

一个使用XIB的简单应用看起来像这样：

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool
{   
    let timeline = TimelineViewController()
    let navigation = UINavigationController(rootViewController: timeline)

    let frame = UIScreen.main.bounds
    window = UIWindow(frame: frame)

    window!.rootViewController = navigation
    window!.makeKeyAndVisible()

    return true
}
```

在上面的代码中，我们创建一个ViewController,并将其放在navigation controller中。然后将其分配给UIWindow对象的rootViewController属性。 这个`window`对象是AppDelegate的属性，它是我们的应用的一个窗口。

应用程序的*window*是一个重要的概念。 本质上，窗口就是应用程序，大多数iOS应用程序只有一个窗口。 它包含您应用的用户界面（UI），将事件调度到视图，并提供了一个主要背景层来显示您的应用内容。 从某种意义上说，“ Windows”的概念就是微软定义的窗口，而在iOS上，这个概念没有什么不同。 （谢谢，Xerox！）

好了，下面让我们继续SceneDelegate。

> 如果“窗口”的概念仍然不了解，请查看iPhone上的应用程序切换器。 双击Home键或从iPhone底部向上滑动，然后您会看到当前正在运行的应用程序的窗口。 这就是应用程序切换器。

### The Scene Delegate

在iOS 13（及以后版本）上，SceneDelegate将负责AppDelegate的某些功能。 最重要的是，*window*（窗口）的概念已被*scene*（场景）的概念所代替。 一个应用程序可以具有不止一个场景，而一个场景现在可以作为您应用程序的用户界面和内容的载体（背景）。

尤其是一个具有多场景的App的概念很有趣，因为它使您可以在iOS和iPadOS上构建多窗口应用程序。 例如，文档编辑器App中的每个文本文档都可以有自己的场景。 用户还可以创建场景的副本，同时运行一个应用程序的多个实例（类似多开）。

在Xcode 11中有三个地方可以明显地看到SceneDelegate的身影：

1. 现在，一个新的iOS项目会自动创建一个`SceneDelegate`类，其中包括我们熟悉的生命周期事件，例如active，resign和disconnect。
2. AppDelegate类中多了两个与“scene sessions”相关的新方法：`application(_:configurationForConnecting:options:)` 和 `application(_:didDiscardSceneSessions:)`  
3. Info.plist文件中提供了”Application Scene Manifest“配置项，用于配置App的场景，包括它们的场景配置名，delegate类名和storyboard

让我们一次开看一看。

#### 1. Scene Delegate Class

首先，SceneDelegate类：

![scene-delegate-1.jpg](https://upload-images.jianshu.io/upload_images/1159872-abfdaa44c8a2b006.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


SceneDelegate的最重要的函数是：`scene(_:willConnectTo:options:)`。 在某种程度上，它与iOS 12上的 `application(_:didFinishLaunchingWithOptions:)` 函数的作用最相似。当将场景添加到app中时`scene(_:willConnectTo:options:)`函数会被调用的，因此这里是配置场景的最理想地方。 在上面的代码中，我们手动地设置了视图控制器堆栈，稍后会进行详细介绍。

这里需要特别注意的是，“SceneDelegate”采用了协议模式，并且这个delegate通常会响应任何场景。 您使用一个Delegate来配置App中的所有场景。

 `SceneDelegate` 还具有下面这些函数:

- `sceneDidDisconnect(_:)` 当场景与app断开连接是调用（注意，以后它可能被重新连接）
- `sceneDidBecomeActive(_:)`  当用户开始与场景进行交互（例如从应用切换器中选择场景）时，会调用
- `sceneWillResignActive(_:)`  当用户停止与场景交互（例如通过切换器切换到另一个场景）时调用
- `sceneWillEnterForeground(_:)` 当场景变成活动窗口时调用，即从后台状态变成开始或恢复状态
- `sceneDidEnterBackground(_:)` 当场景进入后台时调用，即该应用已最小化但仍存活在后台中

> 看到函数的对称性了吗？  Active/inactive, background/foreground, 和 “disconnect”. 。 这些是任何应用程序的典型生命周期事件。

#### App Delegate中的Scene Sessions

在iOS13中AppDelegate中有两个管理Senen Session的代理函数。在您的应用创建scene（场景）后，“scene session”对象将跟踪与该场景相关的所有信息。

这两个函数是:

- `application(_:configurationForConnecting:options:)`,  会返回一个创建场景时需要的UISceneConfiguration对象

- `application(_:didDiscardSceneSessions:)`,   当用户通过“应用切换器”关闭一个或多个场景时会被调用

  

目前，SceneSession被用于指定场景，例如“外部显示” 或“ CarPlay” 。 它还可用于还原场景的状态，如果您想使用【状态还原】，SceneSession将非常有用。 状态还原允许您在应用启动之间保留并重新创建UI。 您还可以将用户信息存储到场景会话中，它是一个可以放入任何内容的字典。

`application(_:didDiscardSceneSessions:)`很简单。 当用户通过“应用程序切换器”关闭一个或多个场景时，即会调用该方法。 您可以在该函数中销毁场景所使用的资源，因为不会再需要它们。

了解`application(_:didDiscardSceneSessions:)`与`sceneDidDisconnect（_ :)`的区别很重要，后者仅在场景断开连接时调用，不会被丢弃，它可能会重新连接。而`application（_：didDiscardSceneSessions：）`发生在使用【应用程序切换器】退出场景时。

#### 3. Info.plist 中的Application Scene Manifest

您的应用支持的每个场景都需要在“Application Scene Manifest”（应用场景清单）中声明。 简而言之，清单列出了您的应用支持的每个场景。 大多数应用程序只有一个场景，但是您可以创建更多场景，例如用于响应推送通知或特定操作的特定场景。

Application Scene Manifest清单是Info.plist文件的一项，都知道该文件包含App的配置信息。 Info.plist包含诸如App的名称，版本，支持的设备方向以及现在支持的不同场景等配置。

请务必注意，您声明的是会话的“类型”，而不是会话实例。 您的应用程序可以支持一个场景，然后创建该场景的副本，来实现【多窗口】应用程序。

下面看一下的 `Info.plist`中清单的一些配置:

![scene-manifest-2.jpg](https://upload-images.jianshu.io/upload_images/1159872-b322c57372932219.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在红框内，您会看到Application Scene Manifest 这一条。 在它下面一条是Enable Multiple Windows，需要将其设置为“ YES”以支持多个窗口。 再往下*Application Session Role*的值是一个数组，用于在应用程序中声明场景。 你也可以在数组中添加一条【外部屏幕】的场景声明。

最重要的信息保存在*Application Session Role*数组中。 从中我们可以看到以下内容：

- Configuration的名称，必须是唯一的
- 场景的代理类名，通常为`SceneDelegate`。
- 场景用于创建初始UI的storyboard名称

Storyboard名称这一项可能使您想起*Main Interface*设置，该设置可以在Xcode 12项目的Project Properties配置中找到。 现在，在iOS应用中，你可以在此处设置或更改主Storyboard名称。

AppDelegate中的SceneDelegate、UISceneSession和Application Scene Manifest是如何一起创建多窗口应用的呢？

- 首先，我们看`SceneDelegate`类。 它管理场景的生命周期，处理各种响应，诸如 `sceneDidBecomeActive(_:)` and `sceneDidEnterBackground(_:)`之类的事件。
- 然后，我们再看看`AppDelegate`类中的新函数。 它管理场景会话（scene sessions），提供场景的配置数据，并响应用户丢弃场景的事件。
- 最后，我们看了一下*Application Scene Manifest*。 它列出了您的应用程序支持的场景，并将它们连接到delegate类并初始化storyboard。

Awesome! Now that we’ve got a playing field, let’s find out how *scenes* affects building UIs in Xcode 11.

太棒了！ 现在让我们了解scenes（场景）是如何影响Xcode 11中的用户界面的吧。



### 在SwiftUI中使用Scene Delegate

不久将来，SwiftUI将是创建iOS 13项目最简单的方法。 简言之，SwiftUI应用程序主要依靠SceneDelegate来设置应用程序的初始UI。

首先，SwiftUI项目中“*Application Scene Manifest* ”将长这样：

![scene-manifest-3.jpg](https://upload-images.jianshu.io/upload_images/1159872-f6a1c6cfe5bcc7ee.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

特别注意，配置中没有设置“Storyboard Name”这一项。 请记住，如果要支持多个窗口，则需要将*Enable Multiple Windows*设置为`YES`。

我们将跳过“ AppDelegate”，因为它相当标准。在SwiftUI项目中，只会返回“true”。



接下来是`SceneDelegate`类。 正如我们之前讨论的，SceneDelegate负责设置您应用中的场景，以及设置首个页面。

像下面一样:

```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {

        let contentView = ContentView()

        if let windowScene = scene as? UIWindowScene {
            let window = UIWindow(windowScene: windowScene)
            window.rootViewController = UIHostingController(rootView: contentView)
            self.window = window
            window.makeKeyAndVisible()
        }
    }
}
```

上面的代码中发生了什么？

- 首先，必须明确的是 在将新场景添加到应用中后 会调用`scene(_：willConnectTo：options：)`代理函数。 它提供了一个`scene`对象（和一个session）。 这个“UIWindowScene”对象是由应用创建的，您无需进行其他操作。
- 其次，`window`属性会在这里用到。 App仍然使用“ UIWindow”对象，但现在它们已成为scene（场景）的一部分。 在`if let`代码块中，您可以清楚地看到如何使用*scene*来初始化UIWindow对象的。
- 然后是设置window的rootViewController，将`window`实例分配给了场景的`window`属性，并且设置窗口`makeKeyAndVisible`为true，即将该窗口置于App的前面。
- 接着为SwiftUI项目创建了ContentView实例，并通过使用UIHostingController将其添加为根视图控制器。 该控制器用于将基于SwiftUI的视图显示在屏幕上。
- 最后但并非不重要的一点，值得注意的是，UIScene的实例化对象scene实际上是UIWindowScene类型的对象。 这就是`as?`对可选类型转换的原因。 （到目前为止，已创建的场景通常为“ UIWindowScene”类型，但我猜想将来还会看到更多类型的场景。）

所有这些看起来似乎很复杂，但是从高层次的概述来看，这很简单：

- 当`scene（_：willConnectTo：options：）`被调用时，SceneDelegate会在正确的时间配置场景。
-  AppDelegate和Manifest的默认配置，他们没有涉及storyboard的任何东西。
-  `scene（_：willConnectTo：options :)`函数内，创建一个SwiftUI视图，将其放置在托管控制器中，然后将控制器分配给window属性的根视图控制器，并将该窗口放置在应用程序UI的前面 。

太棒了！ 让我们继续。

>您可以通过选择File（文件）→New（新建）→Project（项目）来建立一个基本的Xcode 11项目。 然后，选择`Single View App`, 在`User Interface`处选择SwiftUI来创建一个SwiftUI项目



### 在Storyboards项目中使用SceneDelegate

Storyboards和XIB是为iOS应用程序构建UI的有效方法。 在iOS 13上也是如此。 在将来，我们将看到更多的SwiftUI应用，但目前，Storyboards更常见。

有趣的是，即使有了SceneDelegate，通过Storyboards创建iOS项目你也不需要做任何额外的事情 只需选择`File → New → Project`。 然后，选择`Single View App`。 最后，为 User Interface 处选择 Storyboard ，就完成了。

设置方法如下：

- 如我们前面提到过的，您可以在Info.plist中的“ Application Scene Manifest”中找到`Main` storyboard的设置地方。
- 默认情况下，AppDelegate将使用默认的UISceneConfiguration。
- SceneDelegate会设置一个“UIWindow”对象，并使用“Main.storyboard”来创建初始UI。

#### 纯代码编写UI

许多开发人员喜欢手写UI，而随着SwiftUI的兴起，使用SwiftUI手写代码将会越来越常见。 如果您不使用storyboards，而使用XIB创建应用程序UI，该怎么办？ 让我们看看`SceneDelegate`如何修改。

首先，AppDelegate和Application Scene Manifest中保持默认值。 我们不使用storyboard，所以需要在SceneDelegate类的`scene(_:willConnectTo:options:)`函数中设置初始视图控制器。

像下面这样：

```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions)
    {
        if let windowScene = scene as? UIWindowScene {

            let window = UIWindow(windowScene: windowScene)
            let timeline = TimelineViewController()

            let navigation = UINavigationController(rootViewController: timeline)
            window.rootViewController = navigation

            self.window = window
            window.makeKeyAndVisible()
        }
    }
}
```

上面代码很简单我们就不详细介绍了。

很简单，对吧？ 使用SceneDelegate的核心是将一些代码从AppDelegate移至到SceneDelegate中，并正确配置 Application Scene Manifest 。

> 想要为现有的iOS项目添加scene(场景)支持？ 可以查看Apple官方文档https://developer.apple.com/documentation/uikit/app_and_environment/scenes/specifying_the_scenes_your_app_supports。

作者：Reinder de Vries
翻译：乐Coding




我们在上一篇文章中[如何拦截iOS所有网络请求](http://mp.weixin.qq.com/s/yZFNommXNZMfUormrFn2vw)介绍了如何使用`NSURLProtocol`拦截所有的网络请求添加统计代码。使用`NSURLProtocol`拦截第三方框架像`Alamofire、AFNetwoking、SDWebImage`等就需要一些代码侵入。今天说说如何不使用`NSURLProtocol`给`Alamofire`的所有请求添加统计的方法,而且不用修改现有代码。

如果你看过`Alamofire`的源码就会发现`Alamofire`在网络请求的各个阶段会发送相应的通知。

```
extension Notification.Name {
    /// Used as a namespace for all `URLSessionTask` related notifications.
    public struct Task {
        /// Posted when a `URLSessionTask` is resumed. The notification `object` contains the resumed `URLSessionTask`.
        public static let DidResume = Notification.Name(rawValue: "org.alamofire.notification.name.task.didResume")

        /// Posted when a `URLSessionTask` is suspended. The notification `object` contains the suspended `URLSessionTask`.
        public static let DidSuspend = Notification.Name(rawValue: "org.alamofire.notification.name.task.didSuspend")

        /// Posted when a `URLSessionTask` is cancelled. The notification `object` contains the cancelled `URLSessionTask`.
        public static let DidCancel = Notification.Name(rawValue: "org.alamofire.notification.name.task.didCancel")

        /// Posted when a `URLSessionTask` is completed. The notification `object` contains the completed `URLSessionTask`.
        public static let DidComplete = Notification.Name(rawValue: "org.alamofire.notification.name.task.didComplete")
    }
}
```

只要我们在自己的单例中注册`Notification.Name.Task.DidResume`和`Notification.Name.Task.DidComplete`这两个通知可以获得网络请求和返回数据。关键代码如下：
```
class LLMarkerAlamofire: NSObject {

    static let sharedMarkerAlamofire = LLMarkerAlamofire()
    private override init() {
        startDates = [URLSessionTask: Date]()
    }
    private var startDates: [URLSessionTask: Date]
    deinit {
        ll_markerStopLogging()
    }
    public func ll_markerStopLogging() {
        NotificationCenter.default.removeObserver(self)
    }
    public func ll_markerStart()  {
        //1.0 添加Alamofire的通知
        let notificationCenter = NotificationCenter.default
        notificationCenter.addObserver(
            self,
            selector: #selector(LLMarkerAlamofire.ll_markerNetworkRequestDidStart(notification:)),
            name: Notification.Name.Task.DidResume,
            object: nil
        )
        notificationCenter.addObserver(
            self,
            selector: #selector(LLMarkerAlamofire.ll_markerNetworkRequestDidComplete(notification:)),
            name: Notification.Name.Task.DidComplete,
            object: nil
        )
    }
 // MARK: - Private  Notifications
    @objc private func ll_markerNetworkRequestDidStart(notification: Notification) {
        guard let userInfo = notification.userInfo,
            let task = userInfo[Notification.Key.Task] as? URLSessionTask
            else {
                return
        }
        startDates[task] = Date()
    }
    
    @objc private func ll_markerNetworkRequestDidComplete(notification: Notification) {
        guard let sessionDelegate = notification.object as? Alamofire.SessionDelegate,
            let userInfo = notification.userInfo,
            let task = userInfo[Notification.Key.Task] as? URLSessionTask,
            let request = task.originalRequest,
            let httpMethod = request.httpMethod,
            let requestURL = request.url
            else {
                return
        }
        
        var elapsedTime: TimeInterval = 0.0
        var startTime:Date?
        if let startDate = startDates[task] {
            elapsedTime = Date().timeIntervalSince(startDate)
            startTime = startDate
            startDates[task] = nil
        }
  }
}

```



我们在`ll_markerNetworkRequestDidComplete`拿到`sessionDelegate` 和`request `就可以拿到需要的信息了。我们采用相同的方式也可以统一控制网络请求过程中`HUD`的显示与隐藏。

注意：Swift2.3这种方式是如法获得请求的返回数据的，Swift3对应的Alamofire框架才可以获得`sessionDelegate`并获得返回数据

```swift
guard let data = sessionDelegate[task]?.delegate.data else { return }
```
-----------------------
最新文章第一时间发布在微信公众号：`乐Coding`。关注请微信搜索：`lecoding`或者`乐Coding`,或者扫描下方二维码关注。
![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

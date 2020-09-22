##### 背景

最近在研究`iOS无埋点`统计技术，我们的`统计SDK`主要分两部分：点击事件和网络请求。统计所有的点击事件是采用`Method Swizzling`实现的，可以做到使用中不需要一行代码实现统计所有事件，具体细节将来我会专门抽几篇文章介绍。

今天主要说说如何统计APP中的所有网络请求。公司网络请求如果不是`静态库`或者`框架`，很容易想到在网络请求发送和返回时添加统计的代码。如何在不修改原来代码（或者修改最少）的基础上拦截所有的请求呢，能不能从系统层面上拦截回调呢？答案是肯定的，苹果有一个黑魔法`NSURLProtocol`。

##### 介绍

`NSURLProtocol`是`iOS URL Loading System`中的一部分，看起来像是一个协议，但其实这是一个类，而且必须使用该类的子类，并且需要被注册。先看看他在`URL Loading System`中的位置：

![001.png](http://upload-images.jianshu.io/upload_images/1159872-21a097826254d74c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 使用场景

不管是`UIWebView`还是`URLSession`还是第三方的`AFNetWorkong`、`Alamofire`或者`SDWebImage`他们都是基于URLSession或者NSURLConnection来实现的，因此可以通过`NSURLProtocol`做自定义操作。

- 重定向网络请求
- 拦截网络加载，采用本地缓存
- 修改Request信息
- 自定义返回结果
- 对请求进行HTTPDNS解析，动态设置Host，解决不同网络下客户端不能访问的情况

##### 实现

首先要继承`NSURLProtocol`创建自定义的类，然后重写`startLoading`、`stopLoading`添加我们的统计代码就可以了：

```objective-c
static NSString * const hasInitKey = @"LLMarkerProtocolKey";
@interface LLMarkerURLProtocol : NSURLProtocol
@end
```

子类实现的`NSURLProtocol`方法:

1.0 `+(BOOL)canInitWithRequest:(NSURLRequest *)request;`子类是否能响应该请求。

```objective-c
+(BOOL)canInitWithRequest:(NSURLRequest *)request{
    if ([NSURLProtocol propertyForKey:hasInitKey inRequest:request]) {
        return NO;
    }
    return YES;
}
```

2.0  `+(NSURLRequest *)canonicalRequestForRequest:(NSURLRequest *)request`;自定义网络请求，如果不需要处理直接返回request。

```objective-c
+(NSURLRequest *)canonicalRequestForRequest:(NSURLRequest *)request{
    return request;
}
```

3.0  `-(void)startLoading` 开始网络请求，需要在该方法中发起一个请求，对于`NSURLConnection`来说，就是创建一个`NSURLConnection`，对于`NSURLSession`，就是发起一个`NSURLSessionTask` 。一般下载前需要设置该请求正在进行下载，防止多次下载的情况发生。

  ```objective-c
  -(void)startLoading{
    NSMutableURLRequest *mutableReqeust = [[self request] mutableCopy];
    //做下标记，防止递归调用
    [NSURLProtocol setProperty:@YES forKey:hasInitKey inRequest:mutableReqeust];
    self.connection = [NSURLConnection connectionWithRequest:mutableReqeust delegate:self];
  }
  ```

4.0  `-(void)stopLoading` 停止相应请求，清空请求`Connection` 或` Task`。


```objective-c
-(void)stopLoading{
    [self.connection cancel];
}
```

5.0  实现`NSURLConnectionDelegate`、`NSURLConnectionDataDelegate`或者`NSURLSessionTaskDelegate`。

```
#pragma mark - NSURLConnectionDelegate

-(void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error{
    [self.client URLProtocol:self didFailWithError:error];
}
#pragma mark - NSURLConnectionDataDelegate
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response
{
    self.responseData = [[NSMutableData alloc] init];
    [self.client URLProtocol:self didReceiveResponse:response cacheStoragePolicy:NSURLCacheStorageNotAllowed];
}

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data {
    [self.responseData appendData:data];
    [self.client URLProtocol:self didLoadData:data];
}

- (void)connectionDidFinishLoading:(NSURLConnection *)connection {
    [self.client URLProtocolDidFinishLoading:self];
}
```

##### 使用

一、在`AppDelegate`中注册:

```
[NSURLProtocol registerClass:[LLMarkerURLProtocol class]];
```

这样能拦截UIWebView和自定义的请求了，如果要拦截AFNetWorking、Alamofire等第三方请求还需要做一些修改。

二、`LLMarkerURLProtocol`中添加自定义`NSURLSessionConfiguration`方法：

```objective-c
+ (NSURLSessionConfiguration *) defaultSessionConfiguration{
    NSURLSessionConfiguration *config = [NSURLSessionConfiguration defaultSessionConfiguration];
    NSMutableArray *array = [[config protocolClasses] mutableCopy];
    [array insertObject:[self class] atIndex:0];
    config.protocolClasses = array;
    return config;
}
```

拦截第三方网络库方法就是让第三方使用我们这个`NSURLSessionConfiguration`。因为我们在自己的`NSURLSessionConfiguration` 中的`protocolClasses`中注册了自己类。

三、 下面以`Alamofire`为例

1.0 继承`Alamofire.SessionManager` 自定义`SessionManager`

```swift
class LLSessionManger: Alamofire.SessionManager{
    public static let sharedManager: SessionManager = {
        let configuration = LLMarkerURLProtocol.defaultSessionConfiguration()
        configuration?.httpAdditionalHeaders = SessionManager.defaultHTTPHeaders
        let manager = Alamofire.SessionManager(configuration: configuration!)
        return manager
    }()
}
```

2.0 使用 `LLSessionManger`进行网络请求

```swift
let manager = LLSessionManger.sharedManager
manager.request("https://httpbin.org/get").responseJSON { (response) in
    if let JSON = response.result.value {
        print("JSON: \(JSON)")
    }
}
```

注意：`AFNetWorking`、`SDWebimage`等第三方库的修改和`Alamofire`类似,找到使用`NSURLSessionConfiguration`的地方，换成`LLMarkerURLProtocol`的`defaultSessionConfiguration`就可以了。

看到这你可能发现，如果使用Alamofire进行网络请求，我们还是修改了原来的代码，下篇文章单独介绍如何不修改原来代码，通过注册Alamofire通知方式，拦截Alamofire的网络请求。

-----------------------
最新文章第一时间发布在微信公众号：`乐Coding`。关注请微信搜索：`lecoding`或者`乐Coding`,或者扫描下方二维码关注。
![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

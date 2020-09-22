### 概念介绍

##### 1.深度链接（Deep Linking）

深度链接即通过手机浏览器或者微信、QQ等第三方`WebView`启动自己原生应用，进而跳转到指定页面或者处理指定逻辑。

##### 2.延迟深度链接(Deferred Deep Linking)

`Deferred Deep Linking`是指用户点击Web跳转到App的时候，手机并没有安装该App。我们希望用户安装完之后可以Deep link到相应内容，或者标记改用户为某个渠道过来的用户。

### 使用场景

- 深度链接的使用场景：

   用户在手机浏览器上浏览了某个商品，点击购买后会跳转的我们自己App的购买页面。


![deep-link.jpg](http://upload-images.jianshu.io/upload_images/1159872-003cb20b1a45e8db.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 延迟深度链接的使用场景：
  - 用户在手机浏览器上浏览推广页面点击了下载，跳转到App Store下载应用，启动应用，记录该用户是从推广页面下载的
  - 用户在Web商品详情页，点击购买。去App Store下载应用，启动应用后跳转到刚浏览的页面。

![deferred-deep-link.jpg](http://upload-images.jianshu.io/upload_images/1159872-365ca46226cc1047.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 解决方案

深度链接iOS中一般使用自定义`URL Scheme`和`Universal links`(iOS 9开始引入)实现。已经有完美解决方案，我们在这里不做介绍。下面主要介绍延迟深度链接解决方案。

#### 延迟深度链接需要解决的问题

1. 点击web页面判断是否安装App,如果安装走`Deep linking`流程，如果未安装去重定向到App Store下载链接。
2. 用户下载第一次启动，用户匹配，如何定位是从Web引流过来的。
3. Deep Linking



#### 实现方案

解决问题的关键是如何在用户不登录的情况下获得用户的唯一标示，因为`iOS`系统限制，`js`无法获得系统的唯一标示，这就需要我们自己来创建。

##### 1. 通过剪切板

- 只支持`iOS10`以上
- 跨越浏览器和宿主app限制
- 复制到剪切板`js`下载链接 https://github.com/zenorocha/clipboard.js

   iOS相关代码

```objective-c
NSString *str = [[UIPasteboard generalPasteboard] string]
```

##### 2. 生成设备不完美唯一标示

通过`js`和app分别采用相同的参数Hash出来一个相同规则的唯一标示，上传到服务器，由服务器模糊匹配在一定时间间隔内是否是该用户操作。

为什么说他是不完美唯一标示呢？

- iOS系统限制拿不到系统的udid、idfa、uuid等信息
- `js`功能有限，拿不到内网`IP`等唯一确定设备的信息
- 及时自己生成时间戳等唯一标示也无法采用Cookie、LocalStorage、SessionStorage共享，因为他们在不同的沙盒下。

我们采用的生成规则是根据屏幕尺寸、操作系统版本和外网IP生成一个并不唯一的唯一标示。这样存在的问题就是相同设备在同一个`wifi`环境下可能存在误伤。同一设备`IP`也可能变化。所以后台要有一个时间限制，比如10分钟后拍配到的就当做无效激活。

存在误伤，随着用户量增加误伤增加。

##### 3. SFSafariViewController Cookie互通方案

iOS9以上可以使用`SFSafariViewController`共享Cookie的方式获取。

优点：精准，不会误伤

缺点：

- iOS10以后必选显示加载SFSafariViewController，把SafariView透明度设置成0或者隐藏不会加载，而且加载的URl不能修改。
- 只能通过safari，不能借助QQ，微信等第三方app的WebView

实现思路：

- 用户点击手机浏览器页面上的按钮
- 把需要保存的信息存入Cookie，重定向到App Store下载链接
- 用户下载并启动应用
- Push或者Present一个SFSafariViewController，公用Safari中的Cookie浏览web页面
- Web页面获取到指定Cookie信息，重定向(URL Scheme方式)应用本身
- AppDelegate接受OpenURL代理回调处理相应逻辑

##### 4. 集成第三方SDK

现有方案有：

1. https://branch.io/
2. https://www.appsflyer.com/
3. 友盟 http://dev.umeng.com/gxb/apptrack#2_2

前两种是国外产品，可能不稳定。友盟采用的方式就是介绍的第2中方案。

**自己实现建议采用1、2两种方式结合的方案，不建议采用3方案，因为限制太多且不稳定**

### 参考文章

[https://stackoverflow.com/questions/25855618/deferred-deep-linking-in-ios](https://stackoverflow.com/questions/25855618/deferred-deep-linking-in-ios)
[http://blogs.innovationm.com/deferred-deep-linking-in-ios-with-universal-link/](http://blogs.innovationm.com/deferred-deep-linking-in-ios-with-universal-link/)
--------
更多iOS、Swift、iOS逆向最新文章请关注微信公众账号：`乐Coding`，或者微信扫描下方二维码关注

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg



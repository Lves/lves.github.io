今天一时兴起给大家介绍一个好用的数据图开源库:`iOS-Charts`,github链接[https://github.com/danielgindi/Charts](https://github.com/danielgindi/Charts),这个开源库是用Swift实现的，作者相当🐂，这个项目有`9435`个Star,Android也有他写的相应开源库：`MPAndroidChart`。 本博客原文地址
[http://lvesli.com/2016/06/06/iOS-Charts/](http://lvesli.com/2016/06/06/iOS-Charts/)


![001.png](http://upload-images.jianshu.io/upload_images/1159872-e7609b44739a90e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![002.png](http://upload-images.jianshu.io/upload_images/1159872-35a5efb7e919db8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![003.png](http://upload-images.jianshu.io/upload_images/1159872-5bdf41e7f35703e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![004.png](http://upload-images.jianshu.io/upload_images/1159872-685adf8ce64dd31e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![005.png](http://upload-images.jianshu.io/upload_images/1159872-aa52914901ba3bbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![006.png](http://upload-images.jianshu.io/upload_images/1159872-f6a4cf9e921beb28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![007.png](http://upload-images.jianshu.io/upload_images/1159872-244598405382c7e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![008.png](http://upload-images.jianshu.io/upload_images/1159872-01aa4c4a52d96401.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![009.png](http://upload-images.jianshu.io/upload_images/1159872-a8da39795a2f0099.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![010.png](http://upload-images.jianshu.io/upload_images/1159872-8306480c20c471cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![011.png](http://upload-images.jianshu.io/upload_images/1159872-2920ef7b8ec435ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![012.png](http://upload-images.jianshu.io/upload_images/1159872-34c8403ccf396c8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![013.png](http://upload-images.jianshu.io/upload_images/1159872-d7e6cd3a27e277db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###进店须知
先上几个美图，给大家个视觉感受，看看是不是你的菜，现在毕竟是看脸的时代。然后我再介绍她能提供哪些“特殊服务”。如果你满意接下来再具体给你介绍如何把“她”抱回家（具体配置和使用）。





###店长介绍
看完`艳压群芳、技压群芳`的姑凉们，如果感觉还不错，接下来听我具体介绍本店的"特殊服务"。本店特色、店长推荐，免除你独自撸码实现的烦恼。

1. 可以实现8各不同类型的数据图
2. 支持图形缩放（使用你勤劳的手指双击或者双指撑开）
3. 支持拖拽和滑动
4. 双轴线
5. 自定义x轴或者y轴
6. 高亮数据（自定义 popup-views）
7. 以PNG、JPEG格式保存到相册
8. 预定义颜色模板
9. 图例（自动生成或者自定义）
10. 动画（支持x轴和y轴动画）
11. 限制线（用于显示添加限制信息或者最大值）
12. 自定义（绘制、类型面板、图例、颜色、背景、手势、虚线）
13. 绘制来自[Realm.io](https://realm.io/)移动数据库的数据


能看到这说明你也是一个痴情郎啊，既然选择就一往情深。下面看看如何带回家享受服务吧。

###缴费办理

####一、 手动配置

1. 把`Charts.xcodeproj`拖拽到你的工程
2. 打开`target's settings`,点击 `Embedded Binaries`下方的加号，选择`Charts.framework`
3. Xcode 6.3.1 有一个bug,你必须先编译然后引入头文件
4. @import Charts
5. 在Objc项目中需要添加桥接文件

####一、 使用Pods
1.在`Podfile`文件中添加

```
pod 'Charts/Realm'
```

执行 `pod install`命令


### 售后服务
`iOS-Charts`已经能够实现大部分功能，当然由于业务需求，她不能完全满足你的需求，就需要你改源码了。比如我司业务还需要她满足一下功能：

1. 数据点只支持圆点不支持正方形。
2. 多条折线时无法同时高亮相同X轴的点
3. 柱状图动画不支持数值变换

下一篇文章我会讲解[如何在GitHub上Fork iOS-Charts的代码、修改源码](http://lvesli.com/2016/07/25/Fork-in-Github-and-change/)满足自己的业务需求。

看到这，你才是“本店”的忠实粉丝啊，看在我们“优质的服务和合理的价格”，欢迎分享到朋友圈，未关注用户点击右上角关注哟。

--- 
更多精彩文章请浏览我的博客：[http://lvesli.com](http://lvesli.com)，本博客发布也会在 *** lecoding *** 微信公众号中同步更新，欢迎大家订阅，有什么问题可以在此一起交流。公众号搜索：*** 乐Coding *** 或者 *** lecoding *** 或者微信扫描下方二维码：


![icon.jpg](http://upload-images.jianshu.io/upload_images/1159872-926fac546865fcd5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

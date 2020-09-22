今天有空看了下iOS9 Storyboard的新特性，Xcode7给storyboard带来了以下特性：

#####1. 分解一个单一的storyboard成多个storyboard,并通过 storyboard references  连接他们。
#####2. 通过 scene dock  给ViewController附加view。
#####3. 在storyboard上给 navigation bar添加多个button .

初始项目，在 `Main.storyboard `中有一个 `TabBarViewController `,它包含两个`HomeViewController`和`UserViewController`。
`HomeViewController`是一个tableviewcontroller,点击cell进入`DetailViewController`。UserViewcontroller是一个简单的ViewController。
Main.storyboard和项目目录如下图：
![1.png](http://upload-images.jianshu.io/upload_images/1159872-c9a42cd1b1558919.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![2.png](http://upload-images.jianshu.io/upload_images/1159872-6a5d4de929c30aa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  项目中还有一个未使用的DiscoverStoryboard.storyboard ，在这个storyboard中只有一个简单的Controller，稍后我们会用到。
![3.png](http://upload-images.jianshu.io/upload_images/1159872-d494671ddaba5ea1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
运行项目效果如下：
![4.png](http://upload-images.jianshu.io/upload_images/1159872-92eb910fb213523a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
今天我们先看一下它的第一个特性，`storyboard`的分解和引用 （`storyboard references`）。
用`storyboard`做过开发的猿都知道，`storyboard`会慢慢的变得越来越臃肿；
还有一个”致命“的弱点就是使用`Git`进行多人开发的时候经常造成冲突，让我们猿们无法忍受只想摔键盘啊！
即使我们可以使用多个`storyboard`来解决但也无法实现使用`segue`从一个`storyboard`中的`viewcontroller` push到另一个`storyboard`中的`controller`。
在Xcode7中这些问题通过`storyboard references`都得到了很好的解决 。
###一、创建一个Storyboard引用
 
打开Main.stroyboard选中HomeController对应的三个controller如下图，然后点击菜单栏【Editor】选项中的【Refactor to storyboard…】按钮
 ![5.png](http://upload-images.jianshu.io/upload_images/1159872-2dc50bd0c9c14f69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)     
 ![6.png](http://upload-images.jianshu.io/upload_images/1159872-96606561bbaa6d80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在弹出框中给新的storyboard起个新的名字Home.storyboard：
![7.png](http://upload-images.jianshu.io/upload_images/1159872-599056d623f5ab67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
保存后，你会发现目录中多了一个Home.storybaord。打开原来的Main.storyboard你会发现原理的Homecontroller变成了一个storyboard references。选中这个storyboard references  在右侧Attributes Inspector中显示如下图：
![8.png](http://upload-images.jianshu.io/upload_images/1159872-3f1e7a70639a939c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打开原来的的`Main.storyboard`如下图，到此分解单个`storyboard`称多个`storyboard`就完成了，就这么简单。
![9.png](http://upload-images.jianshu.io/upload_images/1159872-393b764e057b79c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###二、在团队开发中使用Storyboard
`storyboard references` 还有另外一种使用情况，当我们开发多个功能模块时可能会出现，不同的人在自己的storyboard中开发然后通过storyboard references  把不同的功能模块连接起来。在上边说过我们有一个DiscoverStoryboard.storyboard 还没有用到，如何快速的把第三个功能模块合进来？
首先打开`Main.storyboard`从 `Object Library`中托出一个`storyboard references` 到`User Interface`中，
![10.png](http://upload-images.jianshu.io/upload_images/1159872-46d9171d2bd60c4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后按住键盘`control`键，点击鼠标左键从`TabBarController`拖拽到 `storyboard reference`，在弹出框中选择 `view controllers`如下图：
![11.png](http://upload-images.jianshu.io/upload_images/1159872-8f378ffb21b16e66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
选中 `storyboard reference` 打开右侧`Attributes Inspector` 设置`storyboard` 为 `DiscoverStoryboard.storyboard` 。好了到此添加一个已存在的storyboard成功。
![12.png](http://upload-images.jianshu.io/upload_images/1159872-4a228f7138cf126f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
运行项目如下图所示：
![13.png](http://upload-images.jianshu.io/upload_images/1159872-3928bbd8017930e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*** 原文链接：[http://lvesli.com/2016/05/24/iOS9-Storyboard%E6%96%B0%E7%89%B9%E6%80%A7%E4%B9%8BStoryboard-References/](http://lvesli.com/2016/05/24/iOS9-Storyboard%E6%96%B0%E7%89%B9%E6%80%A7%E4%B9%8BStoryboard-References/) ***

>本博客也会在 *** lecoding *** 微信公众号中同步更新，欢迎大家订阅，有什么问题可以在此一起交流。公众号搜索：*** 乐Coding *** 或者 *** lecoding *** 或者微信扫描下方二维码：

![http://www.lvesli.com/wp-content/uploads/2014/10/qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-8126ca6ffcdbda50.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# iPhoneX适配

**觉得不错请点击下方【喜欢】**，为了微博认证也是无赖!还差1660个🤣

### 屏幕尺寸相关变化

1. 高度增加了145pt，变成812pt.
2. 状态栏高度由20pt变成44pt，留意这个距离就能避开“刘海”的尴尬，相应的导航栏以上变化64->88。
3. 底部工具栏需要为home indicator留出34pt边距。
4. 物理分辨率为1125px * 2436px.

### 启动图的适配

一、 如果在iPhoneX上启动App并没有占满整个屏幕,而是在中心保持原有高度，需要修改启动图。

未修改前张这个鬼样：

![001.png](http://upload-images.jianshu.io/upload_images/1159872-53c8d70c7b674e59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1. 通过`LaunchScreen.storyboard`方式启动

2. 如果启动图原来使用的Assets中的LaunchImage，如下图：

 
![002.png](http://upload-images.jianshu.io/upload_images/1159872-fc5e3b44d7e9decf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




- 给Brand Assets添加一张1125*2436大小的图片

  - 打开Assets.xcassets文件夹，找到Brand Assets
  - 右键Show in Finder
  - 添加一张1125*2436大小的图片

- 修改Contents.json文件,添加如下内容

  ```json
  {
  "extent" : "full-screen",
  "idiom" : "iphone",
  "subtype" : "2436h",
  "filename" : "1125_2436.png",
  "minimum-system-version" : "11.0",
  "orientation" : "portrait",
  "scale" : "3x"
  }
  ```

再次启动App就可以看到全屏显示了

![003.png](http://upload-images.jianshu.io/upload_images/1159872-c38cef3362920fe1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### App内部样式适配



在适配之前先介绍下`viewSafeAreaInsetsDidChange`的调用顺序：

- `viewDidLoad`

- `viewWillAppear`

- `viewSafeAreaInsetsDidChange`

  ```
  ▿ UIEdgeInsets
    - top : 44.0
    - left : 0.0
    - bottom : 34.0
    - right : 0.0
  ```

- `viewWillLayoutSubviews`

- `viewDidAppear`

只有在调用`viewSafeAreaInsetsDidChange`及以后的方法才能获得view和Controller的UIEdgeInsets。所以在viewDidLoad中根据Safe Area设置界面会有问题。

#### 自定义导航栏的情况

先来看看隐藏系统导航栏，自定义NavigationBar不适配时iPhoneX下是什么样子。下图是不适配时效果图和约束。


![004.png](http://upload-images.jianshu.io/upload_images/1159872-b930b6b321959f68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 ![005.png](http://upload-images.jianshu.io/upload_images/1159872-e355cf8c4a919143.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 ![006.png](http://upload-images.jianshu.io/upload_images/1159872-4cb7c5ea3fbabb5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


会发现原来设置64高度的自定义Bar离刘海很近。原因就是iPhoneX下状态栏高度由20变成了44。

解决方法有三种：

1. 在Storyboard中使用Safe Area LayoutGuides,这样有个弊端是在Storyboard使用Safe Area最低只支持iOS9，iOS8的用户就要放弃了，这样老板肯定不同意。

   解决思路就是，去除自定义Bar的64像素高度约束，设置TableView的顶部距离Safe Area的垂直距离为44。

   - 选择viewcontroller所在的storyboard，在File inspector中勾选Use Safe Area Layout Guides.


![007.png](http://upload-images.jianshu.io/upload_images/1159872-b769407718996977.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![008.png](http://upload-images.jianshu.io/upload_images/1159872-95a15f908bd56f4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



   - 从选中TableView，按住control键,按住鼠标左键拉到Safe Area上，选择Vertical Spacing。

![009.png](http://upload-images.jianshu.io/upload_images/1159872-510dfcca26fc4157.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


   - 选中刚添加的约束，在Size Inspector中修改Second Item为Safe Area.Top ,Constant = 44



![010.png](http://upload-images.jianshu.io/upload_images/1159872-d93f664a20464f31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![011.png](http://upload-images.jianshu.io/upload_images/1159872-1c9073949317d7e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



2. 用代码来布局，弊端是原来用Storyboard布局的改成纯代码，累死程序员不偿命。方法就是在`viewSafeAreaInsetsDidChange`设置自定义bar的高度。

   ```swift
    @available(iOS 11.0, *)
     override func viewSafeAreaInsetsDidChange() {
         navigationBarH = view.safeAreaInsets.top + 44
     }
   ```

3. 当然还有第三种方式了，既能使用Storyboard又能适配iPhoneX。

思路：在Storyboard中按照非iPhoneX设置自定义导航栏的高度为64，然后把高度约束作为属性在源码中修改为view.safeAreaInsets.top + 44。

   - 把自定义导航栏的高度拖到Controller作为属性

    
![012.png](http://upload-images.jianshu.io/upload_images/1159872-212fca2159912374.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


   - 在viewSafeAreaInsetsDidChange中修改导航栏的高度

     ```swift
       @available(iOS 11.0, *)
       override func viewSafeAreaInsetsDidChange() {
           customNaviBarHightConstraint.constant = view.safeAreaInsets.top + 44
       }
     ```

#### TableView、WebView、CollectionView等继承ScrollView的适配

原来的老项目中包含TableView、CollettionView的页面如果是使用Storyboard设置的约束，在iPhoneX中可能会有34像素的安全区域，scrollview划不到底部，如下图：


![013.png](http://upload-images.jianshu.io/upload_images/1159872-c3faf8dc8503c860.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

出现这个区域的原因是：原来设置底部约束是tableview底部到Bottom Layout Guide.Top的距离。


![014.png](http://upload-images.jianshu.io/upload_images/1159872-2eb1a032ac4bfde0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解决办法：
修改约束的Bottom Layout Guide.Top为Superview.Bottom。

![修改前](http://upload-images.jianshu.io/upload_images/1159872-d010431f64bf3dc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![修改后](http://upload-images.jianshu.io/upload_images/1159872-231bcc76ab29ecb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


修改完后TableView就可以滚到到底部了。

![016.png](http://upload-images.jianshu.io/upload_images/1159872-72ee77f808418c91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**以下两个是iOS11造成的和iPhoneX没关系**

#### UIBarButtonItem的适配
自定义`rightBarButtonItem`时`Item`会有偏移：
![1004.png](http://upload-images.jianshu.io/upload_images/1159872-fda6dec9c6408f55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解决办法，添加一个偏移。原来代码如下：
```
func setNavigationRightTextButton(_ title: String,textColor:UIColor? = ColorTheme.NormalNavigationBarTitle, action: @escaping () -> Void) {
        let button = NavigationBackButton(type: .custom)
        button.setTitle(title, for: UIControlState())
        button.setTitleColor(textColor, for: UIControlState())
        let font = UIFont.systemFont(ofSize: 15)
        button.titleLabel?.font = font
        let attributes = [NSFontAttributeName: font];
        let size = title.toNSString.size(attributes: attributes)
        button.frame = CGRect(x: 0, y: 0, w: size.width + 15, h: 44)
        button.reactive.controlEvents(.touchUpInside).observeValues { button in
            action()
        }
        let buttonItem = UIBarButtonItem(customView: button)
        self.navigationItem.rightBarButtonItem = buttonItem
    }
```
添加以下代码

```
 if #available(iOS 11.0, *) {
      button.frame = CGRect(x: 0, y: 0, w: size.width + 30, h: 44)
      button.contentEdgeInsets = UIEdgeInsetsMake(10,0 , -10, 0)
 }
```





以上只是我们自己的项目在适配iPhoneX时遇到的几个小问题，可能还会有其他问题，欢迎在讨论区讨论。

-------
更多iOS、Swift、iOS逆向最新文章请关注微信公众账号：`乐Coding`，或者微信扫描下方二维码关注

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

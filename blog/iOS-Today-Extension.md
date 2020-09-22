我们项目中想要添加iPhone `Today Extension`功能，之前没有自己接触过Extension的新功能今天抽了点时间看了看。

`Extension`是`iOS8`新添加的特性，主要是为了改善iOS沙河机制对应用间通信限制。iOS8新添加了6个扩展，iOS9又新添加了4个，分别是：

iOS8:

* Today
* Share
* Action
* Photo Editing
* Storage Provider(Document Provider)
* Custom Keyboard

iOS9新添加四个：

* Audio Unit
* Content Blocker
* Shared Links
* Spotlight Index


今天我们主要看一下`Today Extension`的实现。Today Extension(也叫`Widget`)究竟是个什么鬼呢，如下图：

![IMG_1455.PNG](http://upload-images.jianshu.io/upload_images/1159872-0c0f6cf94030868f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###Today Extension创建步骤
开始之前先要创建一个iOS项目，因为`Extension`不能脱离`containing app`而存在。本项目实例名为，`TodayExtensionDemo`,项目创建完后
具体步骤如下：

1. File -> New -> Target  选择Today Extension,点击继续。

![屏幕快照 2016-03-21 下午6.31.40.png](http://upload-images.jianshu.io/upload_images/1159872-e674cfebd09052c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 给Extension起个响亮的名字后点击创建。

![屏幕快照 2016-03-21 下午6.36.10.png](http://upload-images.jianshu.io/upload_images/1159872-19f1e9897ee7a2c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 创建完后项目中会多三个文件：

	* TodayViewController.h/.m
	*  MainInterface.storyboard
	*  info.plist

4. 运行，设置Active Scheme为刚创建的Extension，点击运行，点击运行后会出现一个选择框，选择Today就可以了，如下图：

![屏幕快照 2016-03-21 下午6.42.50.png](http://upload-images.jianshu.io/upload_images/1159872-a2d0ef3596c47469.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![屏幕快照 2016-03-21 下午6.45.34.png](http://upload-images.jianshu.io/upload_images/1159872-294bce6aa3635e89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这时你就可以看到一个Hello World出现了。

接下来的工作就是自定义这个展示页面了，如果你习惯使用Storyboard直接在`MainInterface.storyboard`上修改即可，如果你习惯自己Coding，你需要先修改一下info.plist
修改方法：

删除 


	<key>NSExtensionMainStoryboard</key>
	<string>MainInterface</string>



添加：

	
	<key>NSExtensionPrincipalClass</key>
	<string>TodayViewController</string>




###部分代码

1.调整Widget的高度


	-(void)awakeFromNib {
	    [super awakeFromNib];
	    [self setPreferredContentSize:CGSizeMake(0, 240)];
	}



2.如果你要访问http站点链接，iOS9之后因为苹果App Transport Security (ATS)新特性，无法直接访问http数据，你也需要在Extension的plist中添加如下代码：

	<key>NSAppTransportSecurity</key>
	<dict>
	    <key>NSAllowsArbitraryLoads</key>
	    <true/>
	</dict>

3.因为Extension和containing app无法进行数据和文件共享，所以你还需要在Extenison中再添加一遍需要的文件。

4.在widget想要点击页面打开containing app。需要采用Open URL的方式打开containing app。

首先，在containing app的info.plist添加如下代码:



	<key>CFBundleURLTypes</key>
	<array>
		<dict>
			<key>CFBundleURLName</key>
			<!--这个一定要唯一-->
			<string>com.wildcat.TodayExtensionDemo</string>  
			<key>CFBundleURLSchemes</key>
			<array>
				<!--调转URL的host,例如：TodayDemo:// --->
				<string>TodayDemo</string>                    
			</array>
		</dict>
	</array>


在today extension中实现：



	-(void)openURLContainingAPP
	{
	    [self.extensionContext openURL:[NSURL URLWithString:@"lecoding://action=GotoHomePage"]
	                 completionHandler:^(BOOL success) {
	                     NSLog(@"open url result:%d",success);
	                 }];
	
	}



在 containing app appdelegate中添加如代码，接收跳转：




	-(BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options{
	    NSString* prefix = @"TodayDemo://";
	    if ([[url absoluteString] rangeOfString:prefix].location != NSNotFound) {
	        NSString* action = [[url absoluteString] substringFromIndex:prefix.length];
	        if ([action isEqualToString:@"GotoHomePage"]) {
	
	        }
	
	        else if([action isEqualToString:@"GotoOtherPage"]) {
	
	         }
	    }
	    return YES;
	}





###如何使用containing app中的图片

虽然Extention无法和containing app 公用库文件，但是可以公用图片，方法就是：

1. 左侧选中`containing app`的`Assets.xcassets`，在右侧`File Inspector`中的`Target Membership`勾选`Extension项目名`。如下图：


![屏幕快照 2016-03-25 上午9.57.25.png](http://upload-images.jianshu.io/upload_images/1159872-0710dd64eae8ac9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 更多iOS、Android开发精彩文章请关注微信公众账号：`lecoding`,你也可以扫描下方二维码关注我们。

![qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-fc0ea2c48064eb49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

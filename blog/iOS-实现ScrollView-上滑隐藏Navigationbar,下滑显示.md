转载请注明原文链接：[http://www.jianshu.com/p/b43113256ce1](http://www.jianshu.com/p/b43113256ce1)

我司产品🐶突然喜欢上了`知乎`和`简书`的的那种上滑加载更多时隐藏`NavigationBar`,下拉时显示的那种效果。那些阅读类APP需要`沉浸式体验`隐藏导航栏无可厚非，我就纳闷一个P2P类软件你隐藏个毛线！废话少说，技术很好实现几行代码的事。

在包含`TableView`或者`ScrollView`的.m文件中加入以下代码:

在页面即将消失时显示NavigationBar,让下一个页面显示时`NavigationBar`显示状态:


	//滑动隐藏导航栏 LiXingLe
	-(void)viewWillDisappear:(BOOL)animated{
	    [super viewWillDisappear:animated];
	
	    self.navigationController.navigationBarHidden = NO;
	
	}


实现`ScrollView`的代理：


	#pragma mark 滑动隐藏导航栏
	//滑动隐藏导航栏 LiXingLe
	
	-(void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset{
	    [self.navigationController setNavigationBarHidden:velocity.y>0 animated:YES];
	}

现在运行一下如果上滑的时候顶部没有变黑就OK了，如果NavigationBar 隐藏后顶部变黑，加上下面的代码，
在`viewDidLoad`中添加：

	//滑动隐藏导航栏 LiXingLe
	
	if ([self respondsToSelector:@selector(edgesForExtendedLayout)])
	
	      self.edgesForExtendedLayout = UIRectEdgeNone;



**觉得不错，下方记得点个“喜欢”哟!**

---
更多iOS、Swift、逆向等开发精彩文章可以关注我的微信公众号`乐Coding`。
你也可以扫描下方二维码关注我们。


![qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-fc0ea2c48064eb49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

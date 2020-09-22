# OS X 10.11 安装Cocoapods失败：Operation not permitted
   系统升级到 *** OS X 10.11 ***  (EI Capitan)的时候，当安装 ** CocoaPods ** 的时候提示如下的错误：*** Operation not permitted ***



#### 一共有两种解决方案：
> 1.官方解决方案；
>
>2.github解决方案，安装在其他目录（我采用的方案）；

##### 1.官方给出的解决方式是：自定义 GEM_HOME
 
    $ mkdir -p $HOME/Software/ruby
    $ export GEM_HOME=$HOME/Software/ruby
	$ gem install cocoapods
	[...]
	1 gem installed
	$ export PATH=$PATH:$HOME/Software/ruby/bin
	$ pod --version
	0.37.2  
##### Github 解决方案

	$ sudo gem install -n /usr/local/bin cocoapods
	$ pod setup

建议开通VPN安装会快点，如果你没有vpn也可以参照一下*** 唐巧 ***的一篇博客：[链接](http://blog.devtang.com/blog/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency)

*** 原文链接：[http://www.lvesli.com](http://lvesli.com/2016/05/23/OS-X-10-11-%E5%AE%89%E8%A3%85Cocoapods%E5%A4%B1%E8%B4%A5%EF%BC%9AOperation-not-permitted/) ***

>本博客也会在 *** lecoding *** 微信公众号中同步更新，欢迎大家订阅，有什么问题可以在此一起交流。公众号搜索：*** 乐Coding *** 或者 *** lecoding *** 或者微信扫描下方二维码：

![http://www.lvesli.com/wp-content/uploads/2014/10/qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-8126ca6ffcdbda50.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

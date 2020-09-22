#### 背景

最近公司项目集成了`Bugtags`,发现奔溃概率达到了2%，怪不得`AppStore`评论有人在【骂娘】。主要问题是有一个`Signal 10 was raised. SIGBUS (_mh_execute_header + 795252)`的bug，但在奔溃堆栈中查不到有用信息。从统计数据中发现，奔溃大多出现在`iOS9.0`-`iOS9.3`之间的版本。

#### 找手机一波三折

发现崩溃大多出现在iOS9.0-9.3，iPhone6、6 Plus、6s、6s Plus手机上，因为测试人员没有`iOS9.3以下`版本的手机，所以就暂时搁置没解决。

第二天早晨一看奔溃率增加，已经到了非解决不可的地步。于是我们先在公司群里悬赏吆喝谁有`iOS9.3`以下的手机，公司400多人没一个😭。

实在没办法只能借助网友的力量了，先后经过了发微博、技术交流群里寻找帮助，最终找到了2个有该系统手机的网友，然后让他们帮忙下载测试，果然必崩。本来想如果他们在北京，立刻过去找他们现场测试，请客吃顿饭就解决了，可他们都不在北京，呜呼哀哉。

有一个同事提议可以上网租一个测试机，我们就找到了“租测云”租用了一台`iOS9.1`版本的测试机，”租测云“还挺给力，中午就把手机送来了。

#### 柳暗花明又一村

拿到测试机后，先从`AppStore`下载线上版本，运行一会就随机在任何页面崩溃了。

我先在`Xcode`上下载`iOS9.2`的`模拟器`运行正常。

接着用真机测试也不会崩溃。

我又打包上传到[蒲公英]，下载后也不会崩溃。

然后各种测试接连经历了尝试-怀疑-不解-纳闷-困惑-痛苦-发狂-崩溃-迷茫等心理路程都没找到原因，也没遇到崩溃情况。

后来想到既然只有从`AppStore`下载才出现问题，怀疑是打包出了问题，然后就重新打包上传到`iTunes Connection`，用`TestFlight`功能进行内测。果然从`TestFlight`下的版本崩溃了。然后就上网各种查找`Xcode8`打包`iOS9`线上奔溃问题的资料，最终锁定到了P3资源文件的问题上。

`Stackoverflow`有一个相同的问题[https://stackoverflow.com/questions/42050549/app-downloaded-from-appstore-crash-in-9-3-lower-version-devices](https://stackoverflow.com/questions/42050549/app-downloaded-from-appstore-crash-in-9-3-lower-version-devices) 我按照他的查找方式，果然发现我们项目中有一张`P3`格式资源图片，这张图片只有在测试环境下才会用到，线上怎么会崩溃呢！？抱着试试的态度我买了一个疗程（删除了这张图片），然后重新打包上传`TestFlight`下载一切运行正常，看来真是一张图片造成的惨案。

#### 解决方式

检查自己项目中是否包含`16位`或者`P3`格式图片的方式如下：

```
1. Create an Inspectable .ipa file.  In the Xcode Organizer (Xcode->Window->Organizer), select an archive to inspect, click “Export...", and choose "Export for Enterprise or Ad-Hoc Deployment". This will create a local copy of the .ipa file for your app.
2. Locate that .ipa file and change its the extension to .zip.
3. Expand the .zip file. This will produce a Payload folder containing your .app bundle.
4. Open a terminal and change the working directory to the top level of your .app bundle
cd path/to/Payload/your.app
 
5. Use the find tool to locate Assets.car files in your .app bundle as shown below:
find . -name 'Assets.car'
 
6. Use the assetutil tool to find any 16-bit or P3 assets, in each Assets.car your application has as shown below. :
sudo xcrun --sdk iphoneos assetutil --info /path/to/a/Assets.car > /tmp/Assets.json
 
7.  Examine the resulting /tmp/Assets.json and look for any contents containing “DisplayGamut": “P3” and its associated “Name".  This will be the name of your imageset containing one or more 16-bit or P3 assets.
 
8.  Replace those assets with 8-bit / sRGB assets, then rebuild your app.
```

后来在网上找到一个中文版：
![asses01.png](http://upload-images.jianshu.io/upload_images/1159872-7984c726748019e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
-----------------------
最新文章第一时间发布在微信公众号：`乐Coding`。关注请微信搜索公众号：`lecoding`或者`乐Coding`,或者扫描下方二维码关注。
![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

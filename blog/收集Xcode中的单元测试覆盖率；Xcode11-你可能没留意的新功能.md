> 文中第二部分推荐阅读

## 收集Xcode中的单元测试覆盖率

通过项目的单元测试覆盖程度，可以看出我们写的哪些代码未被测试。 尽管覆盖率数据不能告诉我们测试质量如何，但是它仍然是一个非常有用的工具，尤其是在使用测试对代码库进行改造时，或者当我们不确定某个条件或状态是否真的会执行到时 。



默认情况下，Xcode不会收集测试范围，但我们可以在设置中很容易打开。 首先同时按住`⌥+⌘+U`键，调出当前Target的测试设置。 然后打开`Options`选项卡，并选中`Code Covetage`复选框，如下所示：


![image](https://upload-images.jianshu.io/upload_images/1159872-49e4ffa0b3061ad2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



 这样就设置好了。以后我们每次运行测试后都会生成单元测试覆盖率数据。 一种方法是打开*Report Navigator*(`⌘+9`)查看，现在将显示每个测试会话的覆盖率报告：



![image](https://upload-images.jianshu.io/upload_images/1159872-2f7c3211dfcc9f8a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们还可以在编码窗口显示覆盖率数据。 要启用此功能，点击右上角的 `Adjust Editor Options`，然后点击`Code Coverage`。平时如果感觉影响开发也可以关闭。开启后你能看到你写的代码哪些被单元测试覆盖到了：




![image](https://upload-images.jianshu.io/upload_images/1159872-6aa56d201701bebe?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



如上图所示，所有尚未测试到的代码右边都被红色突出显示，并且所有代码迭代次数也将显示出来。 

## Xcode11 你可能没留意的新功能

`Adjust Editor Options`选项卡中的其他几个功能我们也可以一起来看看。

### Canvas

`Canvas`选项卡只有在SwiftUI项目中才有用，开启后，SwiftUI效果可以实时看到，如下图：


![image](https://upload-images.jianshu.io/upload_images/1159872-3e7261c18f7d6fe1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Layout

Layout选项可以调整Canvas页面的位置，在右边还是在下面显示。下面图一是默认在右边，图二是点击后的样子：


![image](https://upload-images.jianshu.io/upload_images/1159872-137edeee68dadae0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](https://upload-images.jianshu.io/upload_images/1159872-fc596cf985c0498c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### Assistant

`Assistant`选项卡只有在`Storyboard`或者`Xib`页面才有用。在Storyboard编辑窗口时，首先选中某个页面，然后点击`Assistant`可以调出当前页面对应的代码文件。


![image](https://upload-images.jianshu.io/upload_images/1159872-d9465e67fb0be6c6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



这样你就可以脱脱拽拽了。



![image](https://upload-images.jianshu.io/upload_images/1159872-1790157b7ee43d27?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Minimap

Minimap选项可以看到代码缩略图，缩略图有啥有我还没发现，欢迎留言告诉我😜。


![image](https://upload-images.jianshu.io/upload_images/1159872-cbdaf65751541530?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](https://upload-images.jianshu.io/upload_images/1159872-e665865865bcddbe?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### Authors

如果项目使用了Git进行多人版本管理，Authors选项可以显示每行代码谁、什么时间修改的。定位作者，找到真凶。


![image](https://upload-images.jianshu.io/upload_images/1159872-9f27627923614695?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




![image](https://upload-images.jianshu.io/upload_images/1159872-2228af2c47f684d6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### Show editor only

最后一个就是Show Editor Only了，顾名思义关闭所有右边窗口，只保留编辑窗口。


![image](https://upload-images.jianshu.io/upload_images/1159872-35a344d3d9200f2e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


------
更多iOS小技巧请关注我的微信公众账号：`乐Coding`


![image](https://upload-images.jianshu.io/upload_images/1159872-c9e4c96f2074ade6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

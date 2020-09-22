逆向工程绕不过的一部分就是汇编指令的分析。我们iPhone里面用到的是ARM汇编,但是不同的设备也有差异,因CPU的架构不同。

| 架构     | 设备                                       |
| ------ | ---------------------------------------- |
| armv6  | iPhone, iPhone2, iPhone3G, 第一代、第二代 iPod Touch |
| armv7  | iPhone3GS, iPhone4, iPhone4S,iPad, iPad2, iPad3(The New iPad), iPad mini, iPod Touch 3G, iPod Touch4 |
| armv7s | iPhone5, iPhone5C, iPad4(iPad with Retina Display) |
| arm64  | iPhone5S 及以后版本                           |

从iPhone5s之后的苹果手机都是ARM64位操作系统了，所以我们直接从ARM64汇编指令开始。

###本篇我们分3个部分介绍
寄存器、ARM64 汇编指令（包括栈）、实例

........
文章详情以后暂时转移到我的微信公众号：`乐Coding` 独家首发。请扫描下方二维码查看更多iOS、Swift和逆向相关文章。

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg


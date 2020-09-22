
#### 为什么要学习逆向开发

为什么要学习逆向开发，这是个问题。如果你上网查一定会搜到这样的解释：

1. 了解整个iOS系统和架构，能够站在更高的维度看问题


2. 学习其他App优秀的设计和实现方法
3. 提高开发效率
4. 更好的保障自己App的安全

这些当然是我们投入精力和时间理由，而我学习越狱开发的理由很简单、纯粹——装逼。

#### 逆向学习前的准备

1. 学习逆向开发首先你是一个iOS正向开发工程师，了解iOS AppStore开发。

2. 熟悉iOS设备的硬件结构和系统原理（我也不太了解，但我开始学了）。

3. 了解基本的汇编指令。

4. 拥有一台越狱过的iOS设备。

   ​

   > iOS越狱设备最好是ARM64位系统的，因为苹果从5s开始都是64位指令集，再研究32位的没比必要。
   >
   > 手机最好是iOS8、iOS9系统的，因为iOS10可能不能完美越狱。iPhone的话最好是5s及以上，因为5s以下设备是32位指令集。
   >
   > 越狱的话最好不要用自己平常用的手机，不要在越狱机上登录金融类产品账号，因为越狱后不安全，去买个二手的能用就行了。
   >
   > 越狱有风险，如果你担心变成砖可以在某宝上买一台已经越狱好的。

这里有一个我在闲鱼上买二手手机差点上当的小插曲，提醒大家不要上当。（如果你想在闲鱼或者转转上买台二手手机做测试机，要提前问好手机型号和系统，是不是合约机等。在北京的童鞋，在闲鱼上联系的如果发现对方说是自己的店铺在中关村一带，尤其是科贸电子城（中关村城c1口出我记得很清楚）的千万不要去了，那里是骗子聚点。我到了科贸三楼一个挂着电信的代理商铺之后，价格谈好了iPhone6 1000元，付完钱才告诉我是合约机，要身份证转电信户。他们不提前说好是合约机，每个月要280的电信套餐，交2年左右。发现上当我要求退款，他们坚决不给退。我和一哥们一起去的，我们和他们经过一顿撕扯差点大打出手，发现他们人多势众而且个个膘肥体壮，我俩心想好汉不吃眼前亏，我们说要报警这才妥协把钱给退了。）

#### 逆向的基本思想

- 正向工程(Forward Engineering)
   抽象的逻辑设计  =>  具体的物理实现  	  

   设计概念和算法  =>  编写源代码  =>  编译成二进制机器码   

  **将想法和设计理念变成具体实现的过程** 

- 逆向工程(Reverse Engineering)
   具体的物理实现     =>   抽象的逻辑设计
   反编译机器码        =>   汇编代码(类似的高级语言代码)  =>  理解其算法和设计概念

   **从二进制码中提取设计概念和算法** 

- 程序的编译和反编译

  高级语言（C/C++/Oc/Java/Python/C#）	 -> 中间语言（如：汇编等） -> 目标代码(exe/lib/dll/sys/dylib等二进制文件)

- 逆向必须是有目的的、有针对性的。

- 先熟悉你要逆向的目标程序，从正向的思路去猜测他可能的实现方法（使用的框架、调用的系统API等）

- 定位关键代码

**逆向是一个不断的试错过程，需要进行不停的猜测、查找和验证**

#### 为什么要越狱

越狱就是为了突破iOS沙盒机制的限制。沙盒是一种安全机制，为运行中的程序提供隔离环境。沙盒在启动的时候可以设置运行的程序是否可以访问网络、文件、目录等。

#### 越狱后的准备工作

手机越狱后发现手机上多了一款Cydia的应用。Cydia是越狱iOS的软件管理平台(Cydia 之父 - Jay Freeman(杰 弗里曼))

**越狱后iPhone上安装以下工具 **

- OpenSSH 

  在Cydia上搜索OpenSHH并安装，安装OpenSSH后手机就开启了SSH服务。安装成功后电脑就可以通过`ssh`连接上手机了。

  - OpenSSH默认密码是`alpine`,登录成功后记得改密码。链接之前要确保Mac和iPhone在同一个wifi下，然后在手机设置中查看iPhone的ip地址。

    ```shell
    ssh root@xx.xx.xx.xx 
    ```

- apt-get

  apt-get是一款软件包管理工具。命令介绍：

  ```shell
  apt-get update                    【更新所有的源】

  apt-get upgrade                   【更新所有通过apt-get安装的程序】

  apt-get install  packagename      【安装软件包】

  apt-get remove  packagename       【删除软件包，不删除依赖包，不删除配置文件】

  apt-get remove --purge packagename    【删除该软件包，不删除依赖包，删除配置文件】

  apt-get autoremove packagename        【删除该软件包，删除依赖包，不删除配置文件】

  apt-get autoremove --purge packagname 【可以删除所有依赖包+配置文件】

  apt-cache search string             【搜索含有该string字段的软件包】

  apt-cache show packagename          【详细显示该软件包的信息】

  apt-get clean                       【清除apt-get安装的软件包备份，可以释放储存空间，不影响软件正常使用】
  ```

- 通过apt-get安装 ping、ps、find、tcpdump、top、vim、network-cmds必要工具

  ```shell
  apt-get install  ping

  apt-get install  ps

  apt-get install  find

  apt-get install tcpdump

  apt-get install top

  apt-get install vim

  apt-get install  network-cmds 
  ```

#### 接下来学什么

接下来我会用几篇文章介绍逆向开发的基本工具和知识，请关注微信公众号：乐Coding，或者扫描下方二维码获得更多最新精彩文章。

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

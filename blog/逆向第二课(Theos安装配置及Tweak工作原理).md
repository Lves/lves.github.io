### Theos安装与配置
Theos是一个越狱开发工具包，使用它可以创建Tweak项目，动态Hook第三方程序。GitHub链接：[https://github.com/theos/theos](https://github.com/theos/theos) ,官网安装教程可以参考：[https://github.com/theos/theos/wiki/Installation](https://github.com/theos/theos/wiki/Installation)

#####安装

1. 安装依赖库

   安装Theos之前先安装三个依赖库，dpkg、fakeroot和ldid

   ```shell
   $ brew install ldid fakeroot
   $ brew install --from-bottle https://raw.githubusercontent.com/Homebrew/homebrew-core/7a4dabfc1a2acd9f01a1670fde4f0094c4fb6ffa/Formula/dpkg.rb
   $ brew pin dpkg
   ```

   - ldid（作者：saurik ）
     维基百科：http://iphonedevwiki.net/index.php/Ldid

     越狱iPhone下的签名工具（更改授权entitlements），可以为theos开发的程序进程签名（支持在OS X和iOS上运行）。

   - dpkg使用很简单

     ```shell
     $ dpkg -i/-r  deb包安装/卸载
     $ dpkg -s com.iosre.myiosreproject 查看安装包信息
     ```

     ​


2. 检查Mac电脑是否存在 /opt目录,没有自己创建一个。

```shell
$ cd /opt
```

3. 在新建的/opt目录下clone项目源码

```shell
$ git clone --recursive https://github.com/theos/theos.git
```

4. 下载完成后执行以下命令，修改theos权限

```shell
$ sudo chown -R $(id -u):$(id -g) theos 
```

5. 修改环境变量

   打开 ~/.bash_profile文件，添加以下两行

   ```shell
   export THEOS=/opt/theos
   export PATH=/opt/theos/bin/:$PATH
   ```

   配置好后，命令行查看下是否成功。

   ```shell
   ➜  theos git:(master) ✗ echo $THEOS
   /opt/theos
   ```

   >如果你Mac安装了omyzsh，可能上面配置后并不会成功，解决办法是：在～/.zshrc中添加下面一行。
   >
   >source ～/.bash_profile

   ​

### 第一个逆向工程

下面创建我们的第一个逆向程序，以创建一个SpringBoard的动态库为例。

#####创建项目

- 在你想创建项目的任一目录下执行命令

  ```shell
  $ nic.pl
  ```

- 接着会出现一个列表让你选择创建项目的类型, 我们输入tweak前面的数字11。

- Project Name (required): 提示输入项目名称，我们可以叫SpringBoardTest。

- Package Name [com.yourcompany.springboardtest]: 这里是让输入项目的包名，根据喜好随便输入。

- Author/Maintainer Name [xxx]:这里是让输入作者名字，将来会在Cydia中显示。

- [iphone/tweak] MobileSubstrate Bundle filter [com.apple.springboard]:这里是输入你将要Hook程序的Bundle Identifier，我们以SpringBoard为例，所以这里输入com.apple.springboard。

- iphone/tweak] List of applications to terminate upon installation (space-separated, '-' for none) [SpringBoard]:最后一步是输入要Hook项目Mach-o文件名，这里输入SpringBoard。

![class2-001.png](http://upload-images.jianshu.io/upload_images/1159872-75c0b03c6b3ddbce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####项目目录介绍

项目创建完成后，可以看到工程目录有四个文件：Makefile、SpringBoardTest.plist、Tweak.xm 、control

- control文件

  control文件记录了deb包管理系统所需的基本信息，会被打包进deb包里。我们可以对他修改,添加一行自己的博客地址`Homepage: http://www.jianshu.com/u/6fa5c59c9f2a`

- SpringBoardTest.plist

  包含我们要Hook项目的Bundle Identifier

- Makefile 

  工程的配置信息，介绍可看如下注释

  ```
  //工程包含的通用头文件
  include $(THEOS)/makefiles/common.mk
  //创建工程时指定的“Project Name，指定好之后一般不要再更改
  TWEAK_NAME = SpringBoardTest
  //tweak包含的源文件，指定多个文件时用空格隔开
  SpringBoardTest_FILES = Tweak.xm
  //tweak工程的头文件，一般有application.mk、tweak.mk和tool.mk几类
  include $(THEOS_MAKE_PATH)/tweak.mk
  //指定tweak安装之后，需要做的事情，这里是杀掉SpringBoard进程 
  after-install::
      install.exec "killall -9 SpringBoard"    
  ```

  补充：

  ```
  //编译debug或者release
  DEBUG = 0
  //越狱iPhone的ip地址
  THEOS_DEVICE_IP = 192.168.1.113
  //指定支持的处理器架构
  ARCHS = armv7 arm64 
  //指定需要的SDK版本iphone:Base SDK:Deployment Target
  TARGET = iphone:latest:8.0  //最新的SDK，程序发布在iOS8.0以上
  //导入框架，多个框架时用空格隔开
  SpringBoardTest_FRAMEWORKS = UIKit 
  SpringBoardTest_PRIVATE_FRAMEWORKS = AppSupport
  //链接libsqlite3.0.dylib、libz.dylib和dylib1.o
  SpringBoardTest_LDFLAGS = -lz –lsqlite3.0 –dylib1.o
  //make clean
  clean::
      rm -rf ./packages/* 
  ```

- Tweak.xm

  文件后缀：“xm”中的“x”代表这个文件支持Logos语法，如果后缀名是单独一个“x”，说明源文件支持Logos和C语法；如果后缀名是“xm”，说明源文件支持Logos和C/C++语法。

  - %hook 指定需要hook的class，必须以%end结尾

  - %log 该指令在%hook内部使用，将函数的类名、参数等信息写入syslog 

    > Cydia内搜索安装syslogd

  - %orig该指令在%hook内部使用，执行被钩住（hook）的函数的原始代码。

##### 编译工程

我们实现一个在手机中每次点击Home键弹框的功能。

- 修改Tweak.xm文件如下

  ```objective-c
  %hook SpringBoard 
  - (void)_menuButtonDown:(id)down  
  {  
      UIAlertView *alert = [[UIAlertView alloc]  
      initWithTitle:@"Hello，lecoding!" 
      message:nil 
      delegate:self cancelButtonTitle:@"OK"
      otherButtonTitles:nil]; 
      [alert show]; 
      %orig; // call the original _menuButtonDown:
  }
  %end
  ```

- 修改Makefile文件

  THEOS_DEVICE_IP = 109.168.1.2 这里的IP要修改为你自己越狱手机的内网IP地址。也可以不写这行，在命令行中指定。

  ```shell
  DEBUG = 0
  THEOS_DEVICE_IP = 109.168.1.2 
  ARCHS = armv7 arm64 
  TARGET = iphone:latest:8.0  
  include $(THEOS)/makefiles/common.mk

  TWEAK_NAME = MyFirstReProject
  MyFirstReProject_FILES = Tweak.xm
  MyFirstReProject_FRAMEWORKS = UIKit 
  include $(THEOS_MAKE_PATH)/tweak.mk

  after-install::
      install.exec "killall -9 SpringBoard"
  clean::
      rm -rf ./packages/* 
  ```

- 编译命令

  在项目目录依次执行以下命令

  ```
  make  //编译
  make package  //打包
  make install  //安装
  ```

  执行过程如下：


![class2_002.png](http://upload-images.jianshu.io/upload_images/1159872-4ec32b9e7c441820.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



  执行make install 时会让输入两次手机sshd的密码。安装成功后点击手机Home键就会弹框。

![class2_0021.jpg](http://upload-images.jianshu.io/upload_images/1159872-88d9875a51ee8262.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


  > 手机中卸载Tweak方法
  >
  > - 在Cydia中找到我们的项目，点击卸载
  > - ssh链接到手机，使用`dpkg -r bundlID`  ，bundlID是创建Tweak项目时输入的包名。


### Deb包介绍

执行完make package这一步时，在项目目录就会多了几个目录。

- packages目录存放着最终的deb包。

  deb包本质是一个压缩包文件。里面包含一些特定的目录和文件。安装过程就是dpkg程序按照指定的规则去拷贝文件和执行脚本。

  执行以下命令可以查看Deb包的内部目录结构：

  ```shell
  ➜  packages dpkg -c com.wildcat.sbtest_0.0.1-1_iphoneos-arm.deb
  drwxr-xr-x lixingle/staff    0 2017-09-04 16:41 ./
  drwxr-xr-x lixingle/staff    0 2017-09-04 16:41 ./Library/
  drwxr-xr-x lixingle/staff    0 2017-09-04 16:41 ./Library/MobileSubstrate/
  drwxr-xr-x lixingle/staff    0 2017-09-04 16:41 ./Library/MobileSubstrate/DynamicLibraries/
  -rwxr-xr-x lixingle/staff 131984 2017-09-04 16:41 ./Library/MobileSubstrate/DynamicLibraries/SpringBoardTest.dylib
  -rw-r--r-- lixingle/staff     57 2017-09-04 16:41 ./Library/MobileSubstrate/DynamicLibraries/SpringBoardTest.plist
  ```

- .theos/_/DEBIAN : 该目录主要存放control文件、及安装和卸载时需要执行的脚本等

- .theos/_/Library  : 目录下是将要拷贝到手机相应目录的动态库和配置信息

- 脚本文件

  ```
  preinst
  在Deb包文件解包之前，将会运行该脚本。许多“preinst”脚本的任务是停止作用于待升级软件包的服务，直到软件包安装或升级完成。

  postinst
  该脚本的主要任务是完成安装包时的配置工作。许多“postinst”脚本负责执行有关命令为新安装或升级的软件重启服务。

  prerm
  该脚本负责停止与软件包相关联的daemon服务。它在删除软件包关联文件之前执行。

  postrm
  该脚本负责修改软件包链接或文件关联，或删除由它创建的文件。
  ```

- dpkg打包时会复制当前目录下Layout目录下的所有文件和目录，这些文件和目录会镜像到目标设备上（Layout相对于设备的根目录）

### Logos语法

关于Logos语法可以看wiki学习。维基百科：http://iphonedevwiki.net/index.php/Logos

### Tweak工作原理

Cydia Substrate 原名为 Mobile Substrate 已经正式更名为 Cydia Substrate。它是越狱后cydia插件/软件(主要指theos开发的tweak)运行的一个基础依赖包。提供软件运行的公共库，可以用来动态替换内存中的代码、数据等所以iOS系统越狱环境下安装绝大部分插件，必须首先安装Cydia Substrate。

Cydia Substrate主要由3部分组成：MobileHooker，MobileLoader 和 safe mode

#####MobileHooker

MobileHooker用于替换覆盖系统的方法，这个过程被称为Hooking(挂钩)
它主要包含两个函数：

```c
void MSHookMessageEx(Class class, SEL selector, IMP replacement, IMP *result);
void MSHookFunction(voidfunction,void replacement,void** p_original);
```

MSHookMessageEx 主要作用于Objective-C函数

MSHookFunction 主要作用于C和C++函数

Logos语法%hook就是对此函数做了一层封装，让编写hook代码变的更直观。

#####MobileLoader

MobileLoader 将tweak插件注入到第三方应用程序中。

启动时MobileLoader会根据/Library/MobileSubstrate/DynamicLibraries/目录中plist文件指定的作用范围，
有选择的在第三方进程空间里通过dlopen函数加载同名的dylib。

每一个.dylib文件都会有一个同名的.plist文件。

.plist文件的作用就是用来指定tweak插件的作用对象。

/Library/MobileSubstrate/DynamicLibraries/目录中文件如下,会发现有一个我们刚创建的SpringBoardTest.dylib和SpringBoardTest.plist。


![class2_003.png](http://upload-images.jianshu.io/upload_images/1159872-d9bd90b1b7a44396.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### safe mode

- 因为APP程序质量参差不齐崩溃再所难免，tweak本质是dylib，寄生在别人进程里，如果注入Springboard等。系统进程一旦出错，可能导致整个进程崩溃,崩溃后就会造成iOS瘫痪。
- 所以CydiaSubstrate引入了安全模式,在安全模式下所有基于CydiaSubstratede 的三方dylib都会被禁用，便于查错与修复。

------
更多iOS、Swift、iOS逆向最新文章请关注微信公众账号：`乐Coding`，或者微信扫描下方二维码关注

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg


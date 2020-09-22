在以前的文章中已经介绍过砸壳、Tweak创建动态库等逆向开放相关知识，今天主要介绍怎么把一个dylib的动态库注入到第三方项目中、重签名打包实现多开功能。

介绍这部分是为了接下来微信自动抢红包做准备，敬请期待...


我们今天实现一个简单的小功能，在进入微信的登录页面，谈一个框，效果如下：
![001.jpg](http://upload-images.jianshu.io/upload_images/1159872-1b45363fa2730b36.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 创建Tweak项目

  这里我们以前已经讲过，在这不做详细介绍，可以到我微信公众号: `乐Coding`记录中查看。

- 修改Tweak.xm文件，然后`make package`编译。

  ```objective-c
  %hook WCAccountMainLoginViewController
  - (void)viewDidLoad {
      %orig;
      UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"测试"
                  message:@"你成功了"
                  delegate:self
                  cancelButtonTitle:@"Cancel"
                  otherButtonTitles:@"OK",nil];
      [alert show];
  }
  %end
  ```

- 编译成功后在工程的`.theos/obj/debug`(.theos是个隐藏目录)目录下会找到我们需要的动态库`.dylib`结尾的文件


- 修改动态库依赖

  - 查看动态库依赖项

  ```shell
  otool -L WXRedPackage.dylib
  ```

  ​	结果信息如下：	
    ![002.png](http://upload-images.jianshu.io/upload_images/1159872-73c2d279937da0c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


  ​       结果中有一段越狱手机中才会用到的CyduaSubstrate库。

  我们需要用 libsubstrate.dylib替换这个库。

  - 先查看Theos安装目录/opt/theos/lib中是否有 libsubstrate.dylib文件，如果没有可以到https://github.com/kokoabim/iOSOpenDev/blob/master/lib/libsubstrate.dylib 下载。

  - 使用install_name_tool修改动态库的路径，指向 app 二进制文件的同级目录

    ```shell
    install_name_tool -change /Library/Frameworks/CydiaSubstrate.framework/CydiaSubstrate @loader_path/libsubstrate.dylib WXRedPackage.dylib
    ```

  - 修改后再次查看动态了依赖项

    ```shell
    otool -L WXRedPackage.dylib
    ```

    执行结果如下,如果原来库文件路径变成了新的相对路径说明成功。

  ![003.png](http://upload-images.jianshu.io/upload_images/1159872-d65c7366d01c5990.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 将动态链接库注入二进制文件中

  如果电脑中没有optool可以从[https://github.com/alexzielenski/optool](https://github.com/alexzielenski/optool)下载，因为opool添加了submodule,所以建议使用一下命令clone

  ```shell
  git clone --recursive https://github.com/alexzielenski/optool.git
  ```

  然后用Xcode打开编译后，把Product目录下的optool二进制文件拷贝到`/usr/local/bin`目录下。

  - 拷贝libsubstrate.dylib和我们自己编写并修改过的WXRedPackage.dylib拷贝到WeChat.app目录下

  - 修改微信二进制文件加载Load Commands段

    ```shell
    optool install -c load -p "@executable_path/WXRedPackage.dylib" -t WeChat.app/WeChat
    ```

    执行结果如下:
![004.png](http://upload-images.jianshu.io/upload_images/1159872-f833668bab5fa425.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




- 重签名打包

  在开始打包之前，请先将 WeChat.app 里面的 Watch 目录删除，这个目录是跟 Watch 有关的，如果不删除的话，在越狱手机上可以，但是我的iPhone6上安装不成功。

  ​

  签名可以使用`codesign -f -s 证书名字 目标文件`命令，打包可以使用`xcrun -sdk iphoneos PackageApplication -v WeChat.app -o $(pwd)/WeChat2.ipa`命令。下面我们采用一个可视化的工具。

  ​

  使用iOS App Signer 他是开源的，大家可以在http://dantheman827.github.io/ios-app-signer/ 找到安装包和github源码链接。

  ​

  下载打开软件后如下图:

![005.png](http://upload-images.jianshu.io/upload_images/1159872-6258a3378847ddcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


   -  Input File： 我们刚刚修改的微信.app文件 ，下面两个是证书信息。
   -  点击Start打包。

- 下面可以用iTools或者PP助手安装
![006.png](http://upload-images.jianshu.io/upload_images/1159872-4de3392315ce5845.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**觉得不错请点击下方【喜欢】**，为了微博认证也是无奈!还差1651个呢🤣

-------
更多iOS、Swift、iOS逆向最新文章请关注微信公众账号：`乐Coding`，或者微信扫描下方二维码关注

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

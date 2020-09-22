我的越狱手机是iPhone5s，系统iOS8.1.3，Mac系统 10.12.5。

### OpenSSH

####安装

在越狱手机上打开Cydia,搜索OpenSSH后，点击右上角安装。

#### 使用

#####链接及密码修改

- 确保手机和Mac电脑在用一个wifi环境下（也可以使用共享热点的方式），在设置中查看手机的IP地址。

- Mac上打开`终端`，输入一下命令链接到手机

  ```shell
  ssh root@xxx.xxx.xxx.xxx
  ```

- 稍等片刻，下面会询问是否链接，输入'yes',然后会让输入密码，输入默认密码：`alpine `

![openssh_01.png](http://upload-images.jianshu.io/upload_images/1159872-389cfd704358fafb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 链接成功后，需要修改默认密码，否则不安全。命令行输入：`passwd`

- 然后输入新的密码和确认密码就可以了。


![openssh_02.png](http://upload-images.jianshu.io/upload_images/1159872-1550fa9d6354cd2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####查看电脑sshd服务状态

在Mac和越狱手机间通过ssh拷贝文件，还有个条件就是Mac的sshd服务要开启。

1. 查看Mac的sshd服务状态,终端输入以下命令：

   ```shell
   sudo launchctl list | grep ssh
   ```

   输入密码后，如果显示`0 com.openssh.sshd`提示为开启状态，什么都没有为关闭状态。

 
![openssh_04.png](http://upload-images.jianshu.io/upload_images/1159872-9d1b097f33eaa155.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2. 开启sshd服务命令：

   ```shell
   sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
   ```

3. 关闭sshd服务命令：

   ```shell
   sudo launchctl unload -w /System/Library/LaunchDaemons/ssh.plist
   ```

 ##### 文件拷贝

下面以相册中照片拷贝到电脑为例：

- ssh链接到越狱手机后，cd到相册所在目录`/private/var/mobile/Media/DCIM`

- DCIM可能会有子目录，查找到文件后执行以下命令拷贝:

  ```shell
  scp IMG_0010.PNG lecoding@xxx.xxx.xxx.xxx:/Users/lecoding/Desktop/test
  ```

  ​

  >介绍：
  >
  >IMG_0010.PNG： 图片名，
  >
  > lecoding@xxx.xxx.xxx.xxx：Mac用户名@Mac的IP
  >
  >/Users/lecoding/Desktop/test: 拷贝到电脑的目标路径

***拷贝常用命令***

1. Copy本地文件到服务器的命令如下：

   ```shell
   scp <local file> <remote user>@<remote machine>:<remote path>
   ```

2. Copy远程文件到本地:

   ```shell
   scp <remote user>@<remote machine>:<remote path> <local file>
   ```

3. 拷贝目录加上`-r`循环限制

   ```shell
   scp -r local_folder remote_username@remote_ip:remote_folder
   ```


### 砸壳

砸壳我们采用dumpdecrypted,首先需要在电脑上下载源码、编译我们需要的工具，然后把工具拷贝到手机需要砸壳的App的Documents目录下。具体步骤如下：

#### 下载及编译dumpdecrypted工具

- 从 https://github.com/stefanesser/dumpdecrypted 官网下载源码，下载完目录结构如下：


![dumpdecrypted_001.png](http://upload-images.jianshu.io/upload_images/1159872-36c0fd81d3396e8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 打开Mac终端，进入如上解压目录

- 执行make命令就可以编译。

  ```shell
  make
  ```

  如果遇到如下和我相同的情况，说明，你的Xcode可能不叫这个名字，我的Xcode是/Applications/Xcode 8.3。

  ```shell
  /bin/sh: /Applications/Xcode: No such file or directory
  make: *** [dumpdecrypted.o] Error 127
  ```

  解决方式就是把Xcode 8.3重命名为Xcode。

  再次运行依然错误，错误信息如下：

  ```shell
  xcrun: error: active developer path ("/Applications/Xcode 8.3.app/Contents/Developer") does not exist
  Use `sudo xcode-select --switch path/to/Xcode.app` to specify the Xcode that you wish to use for command line developer tools, or use `xcode-select --install` to install the standalone command line developer tools.
  See `man xcode-select` for more details.
  ```

  执行以下命令重新选择以下：

  ```shell
  sudo xcode-select --reset
  ```

  然后重新执行make命令,如果没用error，就会发现目录中多了像2个文件。

- 成功后目录：

![dumpdecrypted_002.png](http://upload-images.jianshu.io/upload_images/1159872-43ae683d914d949d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 开始砸壳

下面以微信砸壳为例：

1. 把dumpdecrypted.dylib拷贝到手机 /var/tmp目录下（如果没有该目录，自己创建一个或者任一目录下）。拷贝方法上边已介绍。

2. Mac终端ssh连接到手机 

3. 获得微信的Documents目录，这步前提是手机微信打开状态。依次执行如下命令

   ```shell
   LvesiPhone:/var/tmp root# ps -e | grep WeChat
    2866 ??         0:09.67 /var/mobile/Containers/Bundle/Application/6D810938-903E-4DAD-A6B9-4234D9921315/WeChat.app/WeChat
    2937 ttys001    0:00.01 grep WeChat
   LvesiPhone:/var/tmp root# cycript -p WeChat
   cy# dir = [[NSFileManager defaultManager]URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0]
   #"file:///var/mobile/Containers/Data/Application/1E15288D-238F-4884-A472-F9FC7603529F/Documents/"
   ```

   /var/mobile/Containers/Data/Application/1E15288D-238F-4884-A472-F9FC7603529F/Documents/就是我手机上微信的Documents目录，不同手机目录不同。

4. 把dumpdecrypted.dylib拷贝到微信的Documents目录，然后cd到Documents目录

   ```shell
   LvesiPhone:/var/tmp root# cp dumpdecrypted.dylib /var/mobile/Containers/Data/Application/1E15288D-238F-4884-A472-F9FC7603529F/Documents
   LvesiPhone:/var/tmp root# cd /var/mobile/Containers/Data/Application/1E15288D-238F-4884-A472-F9FC7603529F/Documents
   ```

5. 执行`DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Containers/Bundle/Application/6D810938-903E-4DAD-A6B9-4234D9921315/WeChat.app/WeChat`命令进行砸壳，成功后就可以在Documents目录找到WeChat.decrypted 文件，就是砸壳后的微信了。

   终端执行如下：

   ```shell
   LvesiPhone:/var/mobile/Containers/Data/Application/1E15288D-238F-4884-A472-F9FC7603529F/Documents root# DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Containers/Bundle/Application/6D810938-903E-4DAD-A6B9-4234D9921315/WeChat.app/WeChat
   mach-o decryption dumper

   DISCLAIMER: This tool is only meant for security research purposes, not for application crackers.

   [+] detected 64bit ARM binary in memory.
   [+] offset to cryptid found: @0x1000c0ca8(from 0x1000c0000) = ca8
   [+] Found encrypted data at address 00004000 of length 51200000 bytes - type 1.
   [+] Opening /private/var/mobile/Containers/Bundle/Application/6D810938-903E-4DAD-A6B9-4234D9921315/WeChat.app/WeChat for reading.
   [+] Reading header
   [+] Detecting header type
   [+] Executable is a FAT image - searching for right architecture
   [+] Correct arch is at offset 56360960 in the file
   [+] Opening WeChat.decrypted for writing.
   [+] Copying the not encrypted start of the file
   [+] Dumping the decrypted data into the file
   [+] Copying the not encrypted remainder of the file
   [+] Setting the LC_ENCRYPTION_INFO->cryptid to 0 at offset 35c0ca8
   [+] Closing original file
   [+] Closing dump file

   LvesiPhone:/var/mobile/Containers/Data/Application/1E15288D-238F-4884-A472-F9FC7603529F/Documents root# ls
   00000000000000000000000000000000  MMResourceMgr  MemoryStat    WeChat.decrypted     heavy_user_id_mapping.dat
   LocalInfo.lst			  MMappedKV	 SafeMode.dat  dumpdecrypted.dylib
   ```

6. 把WeChat.decrypted 拷贝到电脑就可以进行接下来的研究了。

### class-dump

可以将Mach-O文件中的Objective-C头文件导出。class-dump只能导出未经加密的App的头文件,所以我们要先砸壳。如果是swift与OC混编或者纯swift项目class-dump就无能为力了。

classdump是对"otool -ov" 信息的翻译，以一种我们熟悉的易读的方式呈现。

#### 下载

Mac访问官网http://stevenygard.com/projects/class-dump/下载最新版

#### 使用

使用也很简单，使用以下命令进行头文件导出

```shell
 class-dump -s -S -H WeChat.decrypted -o /Users/lecoding/Desktop/test/WeChatDemo/Headers
```

>WeChat.decrypted ： 砸壳后的Mach-o文件路径
>
>/Users/lecoding/Desktop/test/WeChatDemo/Headers： 存储头文件目录
>
>-s             sort classes and categories by name
>
>-S             sort methods by name
>
>-H            generate header files in current directory, or directory specified with -o
>
>-o            output directory used for -H

--------
更多iOS、Swift、iOS逆向最新文章请关注微信公众账号：`乐Coding`，或者微信扫描下方二维码关注

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

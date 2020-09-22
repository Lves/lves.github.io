今天开始逆向的第三篇，了解下越狱手机上如何查看KeyChain和数据库中的内容。从中可以学到：

1. 数据直接存储在手机钥匙串中是不安全的，需要加密存储。
2. 通过微信的聊天记录的存储，了解IM数据表是怎么建的。

## 使用Keychain-Dumper导出keychain数据

- 从[https://github.com/ptoomey3/Keychain-Dumper](https://github.com/ptoomey3/Keychain-Dumper) 下载Keychain-Dumper

- 解压后目录结构中有一个keychain_dumper的可执行文件


![1001.png](http://upload-images.jianshu.io/upload_images/1159872-096517e592d61516.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 把keychain_dumper拷贝到越狱手机的/bin 目录下：

  ```shell
  scp /Users/wildcat/Downloads/Keychain-Dumper-master/keychain_dumper  root@192.168.212.173:/bin
  ```

- 越狱iPhone上进入KeyChains目录

  ```shell
  LvesiPhone:~ root# cd /private/var/Keychains/
  LvesiPhone:/private/var/Keychains root# ls
  TrustStore.sqlite3     caissuercache.sqlite3-journal  keychain-2.db-shm  ocspcache.sqlite3      ocspcache.sqlite3-wal
  caissuercache.sqlite3  keychain-2.db                  keychain-2.db-wal  ocspcache.sqlite3-shm
  ```

- 执行以下命令把KeyChain导出到txt文件中

  ```shell
  LvesiPhone:/private/var/Keychains root# /bin/keychain_dumper > keychain-export.txt
  ```

- 把txt文件拷贝到电脑查看就可以了。

- 下面看看demo中存储的数据Keychain Data：036就是存储的一个手势解锁密码。虽然key值苹果给我们加密码但是数据还是暴露出来了，所以需要我们自己加密

  ```json
  Generic Password
  ----------------
  Service: com.test.merak
  Account: <47657374 75726550 61737377 6f7264>
  Entitlement Group: UUR7J35L27.com.test.merak
  Label: (null)
  Generic Field: <47657374 75726550 61737377 6f7264>
  Keychain Data: 036
  ```

  ​

## 使用iFunBox和SQLPro for SQLite查看微信聊天记录

- 从[iFunBox官网](http://www.i-funbox.com/zh-cn_index.html)下载iFunBox，并安装。

- 越狱手机链接到Mac电脑，在左侧`User Applications`中找到微信


![2001.png](http://upload-images.jianshu.io/upload_images/1159872-7af6a3decb2e5526.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 依次进入一下目录`Documents/一个32位的加密串/DB/`，选中.sqlite结尾的文件，拖到电脑中。


![2002.png](http://upload-images.jianshu.io/upload_images/1159872-74611b24a2349d17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





- 其中有一个MM.sqlite和WCDB_Contact.sqlite。在Mac中用SQLPro for SQLite查看数据库内容。

- MM.sqlite数据库中主要存储着聊天记录相关的数据表

  - 其中每个聊天室会创建一个聊天记录表(公众号消息记录也在这个数据库中)。

  - 表的名字是"Chat_"+聊天室id进行MD5加密的串。其中的聊天室id如果是单聊等于对方的微信号；如果是群聊就是群id。

  
![2003.png](http://upload-images.jianshu.io/upload_images/1159872-e847a74c4d5532af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


  - 聊天记录表结构如下图


![2004.png](http://upload-images.jianshu.io/upload_images/1159872-46ffe2e0cc9852b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


  ​       其中字段可以猜测到意思的有：

  ​	TableVer: 默认全是1

  ​	MesLocalID：自增的主键

  ​        CreateTime: 时间戳

  ​	Message: 发的消息内容

  ​	Type: 消息类型（1:文本消息；49:红包消息）

- WCDB_Contact.sqlite数据库中主要存储通讯录表

  - Friend表存储着通讯录信息

 
![2005.png](http://upload-images.jianshu.io/upload_images/1159872-a48eb664c9c9f072.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


  ​

   - 表结构如下：

![2006.png](http://upload-images.jianshu.io/upload_images/1159872-d140baa54a9fba98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


  - 可以直观看出来的字段信息

    userName: 好友微信ID

    type：好友类型

![2007.png](http://upload-images.jianshu.io/upload_images/1159872-adf53b4b048c18e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**觉得不错请点击下方【喜欢】**，为了微博认证也是无赖!还差1670个🤣

-------
更多iOS、Swift、iOS逆向最新文章请关注微信公众账号：`乐Coding`，或者微信扫描下方二维码关注

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

iOS开发中大多使用`CocoaPods`进行第三方框架的管理，关于如何使用CocoaPods我们就不多说了，今天主要介绍如何把自己写的库文件支持`CocoaPods`让更多的开发者发现和使用。

下面以我自己用`Swift`实现下拉刷新，上滑加载更多的框架[https://github.com/Lves/LLRefresh](https://github.com/Lves/LLRefresh)为例，介绍如何使它支持`CocoaPods`。

#### 一、 先创建一个iOS项目

先创建一个`Single View Application`项目,命名`LLRefreshDemo`,主要用于测试我们的框架。

![cocoapods01.png](http://upload-images.jianshu.io/upload_images/1159872-50f552f297bf01df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 二、 创建Target

我们接下来要给项目创建一个`target`，选择`Cocoa Touch Framework`,我们自己的框架代码全部放在新建的`target`中。

- 选中项目，File ->new -> Target 

![cocoapods02.png](http://upload-images.jianshu.io/upload_images/1159872-c3eaa972b287582b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 选择`Framework&Library`中的`Cocoa Touch Framework`然后给自己框架起个名字（注意：起名字前可以到Cocoapods上搜索有没有，和别人的框架重名就不好了），这里我们命名为`LLRefresh`。

![cocoapods03.png](http://upload-images.jianshu.io/upload_images/1159872-5b3ad4f27c99b622.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 创建完成后就会，点击项目在`target`中就可以看到类似如下结构，现在就可以在`LLRefreshDemo`中引入`Module`了

  ```swift
  import LLRefresh
  ```


![cocoapods04.png](http://upload-images.jianshu.io/upload_images/1159872-3b1038ad72bc64df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 创建完就可以在新建的`target`中进行`Swift`开发了,有几个注意点

  - 如何想让外部使用、继承的`类`或者`Struct`、`Enum`要加上`open` 或者 `public`关键字，否则外部调用将找不到。

  - 外部公开的函数也要添加 `open`或`public`关键字

  - 库写好后，先运行新建的`LLRefresh`Target,然后在demo编写测试，demo运行通过后就可以准备发布到`Cocoapods`了
  
![cocoapods05.png](http://upload-images.jianshu.io/upload_images/1159872-0c71fc6a2409c72d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 三、创建.podspec描述文件

在项目根目录创建`LXLRefresh.podspec`文件，这里的文件名就是你要发布到cocoapods上的名字，供他人搜索、安装(因为`LLRefresh`有人使用，所以我们的库采用`LXLRefresh`)。创建命令如下：

##### 1. 创建Podspec描述文件

```
pod spec create LXLRefresh
```

创建完成后，项目目录结构如下：
![cocoapods06.png](http://upload-images.jianshu.io/upload_images/1159872-f708a002841762f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 2. 修改描述文件

我的描述文件如下，仅供参考。你也可以到`Github`上搜索著名的框架，看看他们怎么写的。

![cocoapods07.png](http://upload-images.jianshu.io/upload_images/1159872-86b229bf7b0d4e7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- s.name	是我们库的名称
- s.module_name  是用户使用时引用的module名，如果库名和module名相同可省略。
- s.version  是库原代码版本号，这里的version要和github上的tag名相同，如何打tag我们第四部介绍
- s.summary  是对我们库的一个简单的介绍
- s.homepage  声明库的主页
- s.license   是所采用的授权版本
- s.author   是库的作者
- s.platform  是我们库所支持的软件平台，这在我们最后提交进行编译 时有用
- s.source  声明原代码的地址。我这里是托管在github上,所以这里将地址copy过来就行了。
- s.source_file 是库包含的源文件目录
- s.resource 是资源文件，例如图片，音视频等



#### 四、 添加tag

我们在上一步填写的`s.version`就是`git`的`tag`版本号。给库添加tag只需要两部，在命令行一次执行。

```
git tag '0.0.1'
git push --tag
```

#### 五、 发布到coacoapods

终于到了最后一步了，如果你没有注册过`Trunk`账号，先注册一个账号才能发布,如何注册过跳过第一步。

- 打开命令行，进入项目根目录。

```
pod trunk register '邮箱' '用户名' --description='描述'
```

- 验证有效性

```
pod spec lint PodName.podspec
```

- 发布到`pod trunk`

```
pod trunk push PodName.podspec --allow-warnings
```

命令行看到类似如下输出，说明已经成功了。现在可以去建个demo，使用cocoapods安装自己的库测试一下了。
![cocoapods08.png](http://upload-images.jianshu.io/upload_images/1159872-3726e6586b511b04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


恭喜你，到此说明你真的读完了。欢迎大家[https://github.com/Lves/LLRefresh](https://github.com/Lves/LLRefresh)帮忙给`star`一下🙏🙏🙏。

-----------------------
最新文章第一时间发布在微信公众号：`乐Coding`。关注请微信搜索公众号：`lecoding`或者`乐Coding`,或者扫描下方二维码关注。
![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

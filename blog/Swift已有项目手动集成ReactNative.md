![000](https://upload-images.jianshu.io/upload_images/1159872-799b1c505d691a39.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###背景

记得2017年初写过一篇公司放弃RN血泪史的经历，当时之所以放弃时因为前期投入过多人力物力研究，以至于第一版本耗时太多未见成效，所有被老板叫停。真是"常在河边走哪有不湿鞋"最近因为苹果审核的问题，我们又在找寻及时更新的方案。之所以选择`ReactNative`是看好了它能与`CodePush`结合实现热更新的功能。

最近大前端的趋势越来越火，跨平台方案也层出不穷从`ReactNative`到`Weex`再到这两天风靡一世的`Flutter`，可真是想感叹一声“md,RN我还没学会，Flutter又来了!”。

大家也不要杞人忧天，选择了这个行业就不要害怕学习，RN也就是学学`NodeJS`、`CSS`、`JavaScript`、`JSX`、`React`、`ReactNative`、`Native`等那么多。

### 正文

如果单纯创建一个ReactNative项目很简单，大家按照官网的教程来就行了，今天主要讲讲在已有的Swift项目中集成ReactNative步骤，我一开始是使用CocoaPods集成的，发现各种坑，不是`yoga`找不到、`glog`找不到，就是各种版本不一致的错误。经过上网查阅各种资料后我选择了放弃，改成手动集成。

前提已经拥有了一个Swift项目，我们假设叫`RNSwiftDemo`,并搭建好了ReactNative开发环境。

#### 1. 创建一个空目录

首先创建一个空目录，我们这里叫RNDemo,用于存放React Native项目.

#### 2. 创建`package.json`文件

#####2.1 添加package.json

在项目根目录下创建一个名为`package.json`的空文件，然后填入以下内容：

```json
{
  "name": "RNSwiftDemo",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
  "dependencies": {
    "react": "16.3.0-alpha.1",
    "react-native": "^0.54.2",
  }
}
```

在根目录执行以下命令`npm install`,执行完后会发现项目目录中多了一个node_modules文件夹，然后执行npm start命令。

![1001.png](https://upload-images.jianshu.io/upload_images/1159872-547af99014d34cbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 2.2 添加index.js

修改index.js文件如下：

```react
import { 
  AppRegistry,
  Text,
  View,
} from 'react-native';
import React, { Component } from 'react';
class RNHomeView extends Component{
  render() {
    return (
      <View style={{backgroundColor:'white' }}>
        <Text style={{fontSize:20, marginTop:200}}>Hello World!</Text>
      </View>
    );
  }
}
class MyApp extends Component {
  render() {
      return <RNHomeView />; 
  }
}
AppRegistry.registerComponent('RNSwiftDemo', () => MyApp);
```



#### 3. 配置Swift项目

**3.1 **把Rn项目中的node_modules文件夹拷贝到Swift项目根目录，如下：

![1002.png](https://upload-images.jianshu.io/upload_images/1159872-ccf8ceddf5e3c6bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




**3.2**在XCode中打开Swift项目,在RNSwiftDemo目录中创建一个RnLibs的Group

**3.3** RnLibs中添加依赖的React Native项目。(不同的项目所添加的依赖库不同，需要自己甄别)

React Native依赖项目主要在node_modules/react-native/Libraries目录中和node_modules/react-native/React中的React.xcodeproj

添加依赖后的效果如下：


![1003.png](https://upload-images.jianshu.io/upload_images/1159872-069966dc3a26063e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![1004.png](https://upload-images.jianshu.io/upload_images/1159872-8f0d135adf9e254b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3.4**添加依赖库

选择项目—Targes—RNSwiftDemo—BuildPhases—Link Binary With Libraries，把依赖项目生成的依赖库添加进去（注意**不要**选择from缀带‘-tvOs’的）


![1006.png](https://upload-images.jianshu.io/upload_images/1159872-d663cb9c125a4668.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![1007.png](https://upload-images.jianshu.io/upload_images/1159872-61fb688a81f3cdc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**3.5** 添加Header Search Path

![1008.png](https://upload-images.jianshu.io/upload_images/1159872-2ca632c2cc4e1500.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**3.6** 设置Other Linker Flag

在Build Settings中搜索【other link flags】，添加`-ObjC`和 `-lc++`。

![1009.png](https://upload-images.jianshu.io/upload_images/1159872-a77dd4f8db59a482.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 4. Swift代码

**4.1** 创建Swift和OC混编的桥接头文件 

如果原项目中有`RNSwiftDemo-Bridging-Header.h`类似这个文件可以跳过4.1。如果没有在项目中随便创建一个OC的文件，XCode会提示是否创建桥接头文件，点击创建就可以了，最后记得把无用的OC文件删掉。

![1010.png](https://upload-images.jianshu.io/upload_images/1159872-6ac94fad1c4455c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**4.2** 引用RN头文件

把一下代码粘贴到桥接头文件中

```objective-c
#import <React/RCTRootView.h>
#import <React/RCTBundleURLProvider.h>
#import "UIView+React.h"
```

**4.3** 添加RN页面

接下来我们准备在首页添加一个按钮，点击按钮跳转到RN页面。RN页面的Controller我们这里叫做“RnViewController”。下面在RnViewController添加如下代码

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    let url = URL(string: "http://localhost:8081/index.bundle?platform=ios")!
    let rootView = RCTRootView(
        bundleURL: url,
        moduleName: "RNSwiftDemo",//这里的名字要和index.js中相同
        initialProperties: nil,
        launchOptions: nil
    )
    view = rootView
}
```

在Info.plist中添加如下配置，

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

#### 5. 运行

如果npm未启动，先到RN项目中执行`npm start`。然后运行iOS项目就可以了,运行效果如下：

![1011.png](https://upload-images.jianshu.io/upload_images/1159872-83ccf8b29106641f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 遇到问题

####1.  找不到React头文件

如果运行出现找不到React头文件（例如：`React/RCTBridgeModule.h file not found`）的情况，可以先选择React外部依赖项目build编译，然后再运行Swift项目。也可以按照下边介绍配置编译依赖：

1.1 Disable the parallel builds:

- xCode menu -> Product -> Scheme -> Manage Shemes...
- Double click on your application
- Build tab -> clear the tick on Pallelize Build

1.2 Add react as a project dependecy

- xCode Project Navigator -> drag React.xcodeproj from Libraries to root tree
- Build Phases Tab -> Target Dependencies -> + -> add **React**

####2.  Undefined symbols for architecture x86_64: “std::terminate()”, referenced from

运行项目时Xcode报错。

解决办法:add -lc++ in Other Linker Flags in your xcode project build settings.

####3.  Invariant Violation:Application 项目名 has not been registered.

error1: Invariant Violation:Application 项目名 has not been registered.

这个错误的原因是index.ios.js 中的注册名，和代码中的引用名不同；
index.js 中

```js
AppRegistry.registerComponent('RNSwiftDemo', () => MyApp);
```

Swift中：

```swift
let rootView = RCTRootView(
    bundleURL: url,
    moduleName: "RNSwiftDemo",
    initialProperties: nil,
    launchOptions: nil
 )
```

###何去何从

作者也是ReactNative刚开始学习，熟悉后会分享下React、ReactNative开发相关知识，更多iOS、iOS逆向、ReactNative相关文章请关注微博或者微信公众账号:乐Coding。

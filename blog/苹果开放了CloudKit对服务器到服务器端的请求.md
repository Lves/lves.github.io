苹果最近[宣布](https://developer.apple.com/news/?id=02042016a)可以连接苹果云平台的`OS X`和`iOS`版`CloudKit`框架，现在已经对`服务器到服务器`的网络请求进行了开放。它消除了以前只有`iOS和MAC`应用才能连接到`CloudKit`公共数据库的限制，使服务器直达苹果web站点。

`CloudKit`服务器到服务器的请求在原来已有特性的基础上新添加了允许开发者提供网络接口，使用户能访问自己的iCloud数据。服务器到服务器的请求根本目的是提供访问`iCloud`公共数据的权限，也为`Parse`关闭`DBaaS`在一定程度上提供了另外一种选择。

为了从服务器端程序或者脚本读取或写入数据到`CloudKit`数据库中，程序员首先需要使用`OpenSLL`生成钥匙串：

    openssl ecparam -name prime256v1 -genkey -noout -out eckey.pem

然后，开发者应该到[CloudKit故事版](https://icloud.developer.apple.com/dashboard)拿着公钥换取访问服务器的私钥。一旦输入公钥，作为网络服务请求子路径的`KeyID`就会立刻生成。苹果提供了一些开发者如何使用新的方式认证`CloudKit`的[JavaScript简单代码](https://developer.apple.com/library/ios/samplecode/CloudAtlas/Listings/Node_node_client_s2s_README_md.html#//apple_ref/doc/uid/TP40014599-Node_node_client_s2s_README_md-DontLinkElementID_200)，如下：

    [Current date]:[Request body]:[Web Service URL]

苹果还提供了使用`curl`来请求的简单代码，如下：

	curl -X POST -H "content-type: text/plain" -H "X-Apple-CloudKit-Request-KeyID: [keyID]” -H "X-Apple-CloudKit-Request-ISO8601Date: [date]" -H "X-Apple-CloudKit-Request-SignatureV1: [signature]" -d '{"users":[{"emailAddress":"[user email]"}]}' https://api.apple-cloudkit.com/database/1/[container ID]/development/public/users/lookup/email

[Stack Overflow上的一些用户](http://stackoverflow.com/questions/35247436/cloudkit-server-to-server-authentication/35254094)也提供了`JavaScript，PHP和Python`语言的替代实现。

`CloudKit`给开发者提供了包括身份认证，私有和公共数据库，结构化的一些服务，例如基于plist，资产存储。

原文翻译自[InfoQ](http://www.infoq.com/news/2016/02/cloudkit-server-to-server)

> 更多`iOS`、`Android`精彩文章请关注微信公众账号：`lecoding`,你也可以扫描下方二维码关注我们。

![qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-fc0ea2c48064eb49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

iOS实现指定长度字符串占满整个TextField，支持支付宝和微信密码输入样式。



###实例
![show_demo](https://upload-images.jianshu.io/upload_images/1159872-88f3803d901fadec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###实现思路
采用两个TextField,一个只负责显示，重写它的drawText方法；
另一个只负责输入，设置它的字体颜色透明。

采用两个TextField实现的原因是iOS中重写`func drawText(in rect: CGRect)`函数，光标并不会相应的改变位置。所以我把UITextField自己的光标设置成透明，用UIView自己实现了一个光标。

### Requirements
Swift 4.2+
Xcode 10.1+
### Installation
Cocoapods is developing, you can drag the `LLimitTextField` folder into your project.


### Usage（代码实例）


```
//if auto insert space
limitTextField.isAutoInsertSpace = true
//auto insert space step
limitTextField.insertSpaceStep = 4
//Color of text
limitTextField.textColor = UIColor.black
limitTextField.font = UIFont.systemFont(ofSize: 25)
//the color of cursor
limitTextField.cursorColor = UIColor.black
limitTextField.keyboardType = .numberPad
//LLimitTextFieldDelegate
limitTextField.delegate = self
//under line type
limitTextField.underlineType = .one
//under line color
limitTextField.underlineColor = UIColor.cyan
//Max limit
limitTextField.limitLength = 16
```

### UnderLine/SeperatorLine Type(分割线类型)

```
enum TextFieldUnderlineType{
    //no underline 
    case none
    //one line
    case one
    //Underline each character below
    case spaced
    //Gridlines
    case grid
}
```
欢迎Star，源码地址： [Github]([https://github.com/Lves/LLimitTextField](https://github.com/Lves/LLimitTextField)
)

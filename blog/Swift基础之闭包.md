##闭包

Swift对闭包进行了简化：

* 利用上下文推断参数和返回值类型
* 隐式返回单表达式闭包，即单表达式闭包可以省略`return`关键字
* 参数名称缩写
* 尾随（Trailing）闭包语法



先来看一个排序的例子，数组的降序排列

```
var usernames = ["Lves", "Wildcat", "Cc", "Lecoding"]
func backWards(s1: String, s2: String) -> Bool
{
    return s1 > s2
}

var resultName1 = usernames.sorted(by: backWards)

//resultName1: ["Wildcat", "Lves", "Lecoding", "Cc"]
```



###1. 闭包表达式语法



swift中闭包的一版形式如下:

```
{ (parameters) -> returnType in
      statements
}

```

下面我们用`内联闭包`实现数组的升序排列,

函数的最后一个参数是闭包时 `()` 可以省略;

注意：此时闭包的`参数和返回值类型`要和`函数声明`相同,`(Self.Generator.Element, Self.Generator.Element) -> Bool`



```
var resultName2 = usernames.sorted { (s1: String, s2: String) -> Bool in
    return s1 < s2
}
//resultName2 : ["Cc", "Lecoding", "Lves", "Wildcat"]

```



###1.2 根据上下文推断类型（Inferring Type From Context）

上面的内联闭包书写还可以更加精简，因为`sorted(_:)`函数需要的函数参数类型已知即：`(String, String) -> Bool`，

因为所有的类型都可以被正确推断，返回箭头`（->）`和围绕在参数周围的`括号`也可以被省略：

```
var resultName3 = usernames.sorted { s1, s2 in
    return s1 > s2
}

//["Wildcat", "Lves", "Lecoding", "Cc"]

```



###1.3 单表达式闭包隐式返回

单行表达式闭包可以通过省略`return`关键字来隐式返回单行表达式的结果

`var resultName4 = usernames.sorted { s1, s2 in  s1 > s2 }`

###1.4 参数名称缩写

如果你觉得还不够精简，当然还有更加精简的：

内联闭包可以省略参数名直接用参数顺序`$0，$1，$2`调用.



```
var resultName5 = usernames.sorted { $0 > $1 }

```

###1.5 函数式闭包
```
var resultName6 = usernames.sorted(by: >)
resultName6 //["Wildcat", "Lves", "Lecoding", "Cc"]
```

###2.  尾随闭包（Trailing Closures））

尾随闭包是一个书写在函数括号之后的闭包表达式，函数支持将其作为最后一个参数调用：

下面先定义一个计算函数，参数为：两个整数和一个函数类型参数

```
func caculateTwoNumbers(num1: Int, num2: Int, CaluFunction: (Int, Int) -> Int) -> Int{
    return CaluFunction(num1, num2)
}

//内联闭包形式，不使用尾随闭包，求和
var numReult1 = caculateTwoNumbers(num1: 2, num2: 3,CaluFunction: {(num1: Int, num2: Int) -> Int in
    return num1 + num2
})
//5
//使用尾随闭包,求乘机
var numReult2 = caculateTwoNumbers(num1: 3, num2: 4) {  $0 * $1 }
numReult2 //12
```

###3. 捕获值（Capturing Values）

闭包可以在其被定义的上下文中捕获常量或变量。即使定义这些常量和变量的原作用域已经不存在，闭包仍然可以在闭包函数体内引用和修改这些值。

###4.闭包是引用类型（Closures Are Reference Types）

和类一样，必要也是引用类型

###5. 非逃逸闭包(Nonescaping Closures)

一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行，我们称该闭包从函数中逃逸。可以在参数名之前标注`@noescape`，用来指明这个闭包是不允许“逃逸”出这个函数的。将闭包标注`@noescape`能使编译器知道这个闭包的生命周期.

像刚才的数组的`sort(_:)`函数中的参数就定义成了非逃逸闭包，

`public func sort(@noescape isOrderedBefore: (Self.Generator.Element, Self.Generator.Element) -> Bool) -> [Self.Generator.Element]`

你可能会问什么时候会出现逃逸闭包呢？举个例子：很多启动异步操作的函数接受一个闭包参数作为 `completion handler`。这类函数会在异步操作开始之后立刻返回，但是闭包直到异步操作结束后才会被调用。在这种情况下，闭包需要“逃逸”出函数，因为闭包需要在函数返回之后被调用。

非逃逸闭包和逃逸闭包讲的不是执行先后顺序吧,非逃逸是指你的闭包不能在函数外单独调用,只能在函数内部调用,函数调用完成后,那个闭包也就结束了。

下面举个逃逸闭包的例子：

```
//声明一个存放函数的数组
var functionArray: [() -> Void] = []
//定义一个接收闭包参数的函数，如果定义非逃逸函数 func doSomething(@noescape paramClosure:() -> Void) 就会编译错误
func doSomething(paramClosure:@escaping () -> Void){
    //把参数放入数组中，用于逃逸调用
    functionArray.append(paramClosure)
    
}
//调用函数

doSomething(paramClosure: {print("Hello world")})

doSomething(paramClosure: {print("Hello LvesLi")})

//逃逸调用闭包
for closurePrama in functionArray {
    
    print("\(closurePrama)")
    
}

```
更多iOS、Swift开发精彩文章请访问[http://lvesli.com](http://lvesli.com)

微信公众账号：`lecoding`同步更新,你也可以扫描下方二维码关注我们。


![qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-fc0ea2c48064eb49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

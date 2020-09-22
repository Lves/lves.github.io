`Swift1.0`的时候一时兴起学习了一下`swift`的基本语法，现在`Swift`已经更新到 `2.1`了,自己吧学到的也忘得差不多了，接下来重新复习一下。 

今天先记录一下学习`swift初见`相关知识点。
微信公众账号`lecoding`同步更新

###1. `if` 条件语句 

```
//---------------------- 1.1  if 联系 ------------------------/

var optionalString: String?
if let name = optionalString {
    greeting="Hello, \(name)"
}
greeting // Hello

```
###1.2 使用 `??` 取代默认值 

```
let wildcat="lecoding"
print("欢迎关注微信公众账号：\(optionalString ?? wildcat)")
```
//打印结果：欢迎关注微信公众账号：`lecoding`


### 1.3 switch

```
/*
* 1. Switch 语句必须有Default选项
* 2. 不用加 break, 找到第一个匹配的分支自动结
*
**/
let animal = "wildcat"
switch  animal {
case "dog":
    print("It's a Dog!")
case "cat", "monkey":
    print("its cat or monkey")
case let x where x.hasPrefix("wild"):
    print("It's wild")
case "wildcat":
    print("It is wildcat")
default:
    print("I don't know")
}
```
//打印结果：  It's wild



### 1.4 for-in 

```
//1. 遍历
let numbersDictionary = [
    "first": [12,23,1,23,34,43],
    "second": [2,23,123,4,43],
    "third": [82,-23,1,23,84,3]
]
var min=Int.max
for (key, numberlist) in numbersDictionary {
    for number in numberlist{
        if number < min{
            min=number
        }
    }
}
print("\(min)")
```
//打印结果： -23

```
//2. `..<` 表示范围 , `..< `不包括上界， `...` 包括上界
var count=0
for i in 0..<4 {
    count++
}
print("\(count)")
//打印结果：4
count=0
for i in 0...4 {
    count++
}
print("\(count)")
```
//打印结果：5



##2. 函数


### 2.1 定义函数
```
func sayHello (name: String, words: String) -> String {
    return "\(name) say:\" \(words)\"。"
}
sayHello("Lves", words: "欢迎关注公众号：lecoding")
```
//打印结果： Lves say:" 欢迎关注公众号：lecoding"。

### 2.2 函数返回多个值 

```
func caculateStatistics(numbers: [Int]) -> (min: Int, max: Int, sum: Int){
    var min = numbers[0]
    var max = numbers[0]
    var sum = 0
    for num in numbers{
        if num > max{
            max = num
        }else if num < min {
            min = num
        }
        sum+=num
    }
    return (min, max, sum)
}

let result = caculateStatistics([23,34,45,4562,-23,0])
print(result.max)  //打印结果：4562
print(result.min)  //打印结果：-23
print(result.2)  //打印结果：4641

```

 在`swift`中，函数支持嵌套，函数既可以当做另一个函数的`参数`，也可以当做另一函数的`返回值`；
函数也是一种`数据类型`


### 2.3 函数嵌套

```
func functionInFunc() ->Int {
    var num1 = 0
    func add() {
        num1++
    }
    add()
    return num1
}
let result1 = functionInFunc()  // 1

```
### 2.4 函数作为返回值

```
func getSumFunction()->((Int,Int)->Int) {
    func getSum(num1: Int,num2: Int)->Int{
        return num1 + num2
    }
    return getSum  //返回函数名
}
var getSumFun=getSumFunction()  //获得求和函数
let result2=getSumFun(10, 20)
result2   //30
```
### 2.5 函数作为参数
```
//第三个参数是一个函数类型的参数
func compareFunc(num1: Int ,num2 : Int ,paramFunc:(Int,Int)->Int)->Int {
    return paramFunc(num1,num2)
}
//把求和函数传入进行求和
let compareResult=compareFunc(1, num2: 12, paramFunc: getSumFun)
// compareResult = 13
```
## 3. 闭包 

函数是一种特殊的带有名字的`闭包`。
匿名闭包使用`in`把 `参数和返回值类型` 与 `闭包体` 分离。

### 3.1 闭包定义 
```
var numArray1 = [10, 21, 2, 0, 65, 26]
//对数组中的每个元素取反后返回，原数组不会变
let numArray2 = numArray1.map({
    (number:Int) -> Int in
    return -number
})
//注释：数组.map() 对当前数组运用闭包内的规则然后返回一个新的数组
print(numArray1, numArray2)
```
打印结果： [10, 21, 2, 0, 65, 26] [-10, -21, -2, 0, -65, -26]

### 3.2 闭包简写

如果一个闭包的类型已知，比如作为一个`回调函数`，你可以忽略参数的`类型`和`返回值`。单个语句闭包会把它语句的值当做结果返回。
上边的闭包你也可以写成下面这样：

```
let numArray3 = numArray1.map({number in
    return -number
})

print(numArray1, numArray3)
```
打印结果： [10, 21, 2, 0, 65, 26] [-10, -21, -2, 0, -65, -26]

你可以通过 `参数位置` 而不是参数名字来引用参数——这个方法在非常短的闭包中非常有用。
当一个闭包作为最后一个参数传给一个函数的时候，它可以直接跟在括号后面。当一个闭包是传给函数的唯一参数，你可以完全忽略括号。

```
//数组排序
let sortedNumbers = numArray1.sort { $0 > $1 }
print(sortedNumbers)
```
打印结果： [65, 26, 21, 10, 2, 0]

## 4. 对象和类 
####1.定义Person类

```

class Person {
    // MARK: - Properties
    ///姓名
    var name: String
    ///年龄
    var age:Int?
    let hasParants:Bool = true
    
    // MARK: - Lifecycle
    //构造函数
    init(name: String){
        self.name = name
    } 
    // MARK: - Private
    func sayHello() ->String{
        return "Hello, My Name is \(name),欢迎关注微信公众账号：lecoding。iOS开发文章实时更新！"
    }
}

var per1 = Person(name: "Lves")
per1.age=24
var sayHi = per1.sayHello()
//"Hello, My Name is Lves,欢迎关注微信公众账号：lecoding。iOS开发文章实时更新！"

```



####2. 定义Person的子类

```

class Student: Person {
    var className: String
    init(name: String, className: String) {
        self.className=className
        super.init(name: name)
        age = 20
    }
    
    override func sayHello() -> String {
        return "I am a Student From class \(className)"
    }
}

var stu = Student(name: "Lves", className: "一年级")
stu.age        //20
stu.sayHello() //"I am a Student From class 一年级"


class Square {
    var width : Float = 0.0
    
    var perimeter: Float{
        get {
            return 4*width
        }
        
        set {
            width = newValue/4.0
        }
    }
    
}
```

## 5. 枚举和结构体
###5.1 枚举

```
//1. 在swift中枚举可以包含方法
enum WeekType {
    case Mon
    case Tue, Wed, Thu, Fri
    case Sat, Sun
    
    func myDescription () -> String{
        switch self {
        case .Mon:
            return "Monday"
        case .Tue:
            return "Tuesday"
        case .Wed:
            return "Wednesday"
        case .Thu:
            return "Thursday"
        case .Fri:
            return "Friday"
        case .Sat:
            return "Saturday"
        case .Sun:
            return "Sunday"
        }
    }
}
let weekday = WeekType.Thu              // Thu
let desc = weekday.myDescription()      // "Tuesday"

//枚举定义了类型之后，可以使用 .rawValue 查看原值
enum FromControllerType: String {
    case First = "FirstController"
    case Second = "SecondController"
}
let from = FromControllerType.First     // First
print(from.rawValue)
//打印结果："FirstController"
```

###5.2. 结构体

使用`struct`来创建一个结构体。结构体和类有很多相同的地方，比如方法和构造器。它们之间最大的一个区别就是结构体是`传值`，类是`传引用`。


```
struct Week {
    var weekday : WeekType
    func myDescription() ->String {
        return "My Weekday is \(weekday.myDescription())"
    }
    
}

let structVar = Week(weekday: .Fri)
structVar.myDescription()     // "My Weekday is Friday"

```

## 6. 协议和扩展 

###6.1. 协议

```
protocol DescriptionProtocal {

    var simpleDescrition: String { get }
    mutating func adjust()
}
```
类、枚举、结构体都可以实现协议
类实现协议

```
class DescripClass: DescriptionProtocal {
    var simpleDescrition:String {
        return "I'm a Class"
    }
    func adjust() {
    
    }
}
```
结构体实现协议

`注意:`声明SimpleStructure时候mutating关键字用来标记一个会修改结构体的方法。SimpleClass的声明不需要标记任何方法，因为类中的方法通常可以修改类属性（类的性质）。

```
struct DescripStruct: DescriptionProtocal {
    var simpleDescrition: String = "I'm a Struct"
    mutating func adjust() {
        simpleDescrition += "123"
    }
}
var structConformProtocal = DescripStruct()
structConformProtocal.adjust()
structConformProtocal.simpleDescrition // "I'm a Struct123"
```

枚举实现协议

```
enum DescripEnum: DescriptionProtocal {
    case Success, Error
    
    var simpleDescrition: String {
        get {
            switch self {
            case .Success:
                return "You are Success"
            case .Error:
                return "You are Failure"
            }
        }
    }
    func adjust() {
    }
}
var enumConformProtocal = DescripEnum.Success
enumConformProtocal.simpleDescrition  //"You are Success"

protocol AbsoluteProtocal {
    mutating func absolute() -> Double
}
```

###6.2 扩展


使用`extension`来为现有的类型添加功能，比如新的方法和计算属性。你可以使用扩展在别处修改定义，甚至是从外部库或者框架引入的一个类型，使得这个类型遵循某个协议。

```
//拓展 Double 类型同时实现 DescriptionProtocal、AbsoluteProtocal 两个协议

extension Double:DescriptionProtocal,AbsoluteProtocal {
    var test:String{
        return "demo"
    }
    var simpleDescrition:String {
        return "The double value is: \(self)"
    }
    mutating func adjust() {
        self = -self
    }
    mutating func absolute() ->Double {
        return abs(self)
    }
}

var doubleNumber:Double = 12.5
doubleNumber.simpleDescrition       //"The double value is: 12.5"
doubleNumber.adjust()               // -12.5

var oriNumber:Double = -100
var resultDouble=oriNumber.absolute()  //取反
resultDouble                // 100

oriNumber.test              // "demo"

```

##7. 泛型 

```
enum optionalValue<Type> {
    case none
    case other(Type)
}

var enumOfInt:optionalValue<Int> = .none
enumOfInt = .other(12)
print(enumOfInt)

```
打印结果：other(12)


```
func anyCommonElements <T: SequenceType, U: SequenceType where T.Generator.Element: Equatable, T.Generator.Element == U.Generator.Element> (lhs: T, _ rhs: U) -> Bool {
    for lhsItem in lhs {
        for rhsItem in rhs {
            if lhsItem == rhsItem {
                return true
            }
        }
    }
    return false
}
anyCommonElements([1, 2, 3], [3])
```

转载请注明原文链接：[http://lvesli.com/2016/05/25/A-Swift-Tour/](http://lvesli.com/2016/05/25/A-Swift-Tour/)
微信公众账号同步更新：`lecoding`,你也可以扫描下方二维码：


![qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-fc0ea2c48064eb49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

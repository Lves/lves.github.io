XCode9新特性
    1. 支持远程调试
    2. Xcode绑定Github账号
    3. 支持Swift类重命名
    4. Swift低版本兼容
    5. Main Thread Checker
    6. 模拟器支持多开
    7. 标示功能
    8. 协议自动补全
    9. 代码段自动抽出函数
    10. 自定义颜色名称
    11. Debug View Hierarchy可以显示ViewController了
    12.支持Markdown，可以在xcode中查阅、编辑markdown文件

Swift4更新
    1. Extension可以访问private的属性
    2. 类型和协议的组合类型
    3. 新的Key Paths 语法
    4. 下标支持泛型
    5. 字符串
        5.1 可以直接获取字符串长度了
        5.2 字符串获取子串语法糖… One-sided Slicing
        5.3 String增加了Collection的一些特性
        5.4 多行字符串
    6. 序列化的简化
    7. Dictionary and Set增强
        7.1 通过Sequence初始化
        7.2 字典过滤后类型不变
        7.3 新增mapValues函数
        7.4 字典支持默认值
    8. MutableCollection新增swapAt(::) 用来交换两个位置的值



上篇文章我们介绍了[iOS11适配iPhoneX总结](http://www.jianshu.com/p/d8073367c274)，这次分享下Xcode9和Swift4新特性。欢迎关注微信公众号：`乐Coding`获得最新文章。

# XCode9新特性

新特性每次都是：***更快、更高、更强***

### 1. 支持远程调试

从来没有接过的iOS设备要先插入一次，然后在设备窗口选中 **Connect via Network**选项，之后就可以断开设备进行网络调试。

如果是已经插入过的手机，插入Mac后选中Xcode的 Window -> Devices and Simulators -> Devices -> 勾选Connect via network

> 这里有一个前提：iPhone必须升级到iOS11，电脑和iPhone在同一个局域网下。

![1005.png](http://upload-images.jianshu.io/upload_images/1159872-56f23b150c594a04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


勾选过一次，以后调试就不用数据线了，爽不爽！手机右边有个网络小星球说明是远程设备。

![10051.png](http://upload-images.jianshu.io/upload_images/1159872-059848c44e368cad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 2. Xcode绑定Github账号

点击右上角Xcode -> Preferences ->


![1006.png](http://upload-images.jianshu.io/upload_images/1159872-a6900526df57777c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![1007.png](http://upload-images.jianshu.io/upload_images/1159872-5b76344fd35a6684.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### 3. 支持Swift类重命名

原来的版本一直不支持Swift语言的重命名操作，终于啊终于...此时应该高歌一曲

> 等了好久终于等到今天
> 梦了好久终于 把梦实现
> 前途漫漫任我闯幸亏还有你在身旁
> 盼了好久终于 盼到今天
> 梦了好久终于 把梦实现

### 4. Swift低版本兼容

原来每次升级Xcode都要convert Swift进行升级，现在Xcode9兼容Swift3.2

![1008.png](http://upload-images.jianshu.io/upload_images/1159872-d5b6c357a4ec39ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 5. Main Thread Checker

Xcode9以前不会自动检测是否在非主线程更新UI,这样会有安全隐患。现在Xcode9自动附带了Main Thread Checker功能。一旦在非主线程操作UI就会在命令行警告：类似如下：

```
Main Thread Checker: UI API called on a background thread: -[UIView subviews]
PID: 28353, TID: 267810, Thread name: (none), Queue name: com.apple.root.user-initiated-qos, QoS: 25
```

现在读秒项目中第三方的小米推送和Bugtags、诸葛IO都有这个问题，需要更新到最新版。小米推送一直没更新，我上周给他们发邮件反馈周四才更新。

### 6. 模拟器支持多开

可以同时打开多个型号模拟器

### 7. 标示功能

- 范围标示

  将游标移到 { } 、( ) 或是 class、func、if、for 等关键字时，按住 command 键，Xcode 将聪明地标示对应的 class、function、if、for 区块。

![10087.png](http://upload-images.jianshu.io/upload_images/1159872-bdf1f664046726a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 提示功能

  跟刚刚一样，将游标移到关键字上，按住 command 键，然后点击，即可出现贴心的提示选单。

  比方如图所示，游标停在 if 上，按住 command 键点击后提示选单出现 Add “else” Statement 和 Add “else if” Statement，Extract Method 等选项。选择 add “else” Statement 后，自动完成 else { } 的输入。


### 8. 协议自动补全

Swift的protocol中必须实现的函数当没有实现时会报错，有时我们需要点击协议定义的地方查看必须实现那些函数。现在Xcode9支持自动补全必须实现的函数

例如UIViewController遵循UITableViewDataSource协议，当未实现函数时会有红色错误提示，点击Fix就会自动补全。

![补全前](http://upload-images.jianshu.io/upload_images/1159872-c6a0cb12a6258b67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![补全后](http://upload-images.jianshu.io/upload_images/1159872-67f61d4abe05deb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 9. 代码段自动抽出函数

选中一段代码，右键选择Refactor -> Extract Method 就会把选中的代码段单独抽出一个函数。这个功能在代码重构中很有用。

![1011.png](http://upload-images.jianshu.io/upload_images/1159872-c6bad6886c4c32ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 10. 自定义颜色名称

可以在 Assets.xcasset 里添加颜色，取个名称，然后在代码或者 Storyboard 中引用这个颜色了。

- 在 Assets.xcassets 页面，右键选择New Color Set

![1012.png](http://upload-images.jianshu.io/upload_images/1159872-3fc6700ffa27421f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 然后设置颜色和名字

![1013.png](http://upload-images.jianshu.io/upload_images/1159872-e216b14d33cf62bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 在xib或者sb中使用的时候可以直接在Named Colors中选择

![1014.png](http://upload-images.jianshu.io/upload_images/1159872-71bcfde7f5493647.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 代码中使用，现在代码中只能iOS11以上可以用

  ```swift
  if #available(iOS 11.0, *) {
      self.view.backgroundColor = UIColor(named:"MainBlue")
  }
  ```

### 11. Debug View Hierarchy可以显示ViewController了

 Debug View Hierarchy是一个很好的页面调试工具，方便研究页面上的控件，原来是无法看到当前Controller的，现在Xcode9中可以看到了。

![1015.png](http://upload-images.jianshu.io/upload_images/1159872-dc0a380ed5c6389f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###12.支持Markdown，可以在xcode中查阅、编辑markdown文件 

# Swift4更新

### 1. Extension可以访问private的属性

在Swift3中类、结构体和枚举的扩展中不能访问private属性。

下面这种写法会报错：**'strName' is inaccessible due to 'private' protection level**

```swift
class ViewController: UIViewController {
    private var strName:String = "test"
}
extension ViewController{
    func testFunc()  {
        print("\(self.strName)") //'strName' is inaccessible due to 'private' protection level
    }
}
```

必须把`private`换成`fileprivate`才可以，但是权限又被扩大了。到了Swift4中extension也可以访问private中的属性了。

### 2. 类型和协议的组合类型

```swift
//2.1 定义类
class Person {
     var name:String?
}
class Student: Person {
    var sId:Int = 0
}
class Teacher: Person {
}
//2.2 协议
protocol Runnable {
    func run()
}
protocol Singing {
    func sing()
}
//拓展子类
extension Student :Runnable, Singing{
    func run() {
        print("\(self.name ?? "")会跑步")
    }
    func sing() {
         print("\(self.name ?? "")会唱歌")
    }
}
extension Teacher :Runnable{
    func run() {
        print("\(self.name ?? "")会跑步")
    }
}

extension ViewController {
    //man既能唱又能跑，还有名字
    func test2(human:Person&Singing&Runnable)  {
        if (human.name?.count ?? 0) > 0 {
            human.run()
            human.sing()
        }
    }
}

```

如果在Swift3中只能像下面这样写

```swift
func test2(man:Person)  {
    if (man.name?.characters.count ?? 0) > 0 {
        if let mm = man as? Runnable{
            mm.run()
        }
        if let mm = man as? Singing{
            mm.sing()
        }
    }
}
```

### 3. 新的Key Paths 语法 

Swift4可以使用`\+点语法`的形式创建KeyPath

`let path:KeyPath = \Apple.color`

例子：

```
//MARK: 3.0 新的Key Path语法
class Apple {
    var color:UIColor
    init(color:UIColor) {
        self.color = color
    }
}
extension ViewController {
    func test3(){
        let apple = Apple(color: UIColor.red)
        let c = apple[keyPath:\Apple.color]
        print("\(c)")
    }
}
```

Swift4的KeyPath具有以下优势：

- 可以再 class、struct上使用
- 定义类型时无需加上 @objcMembers、dynamic 等关键字，使用更简洁
- 性能更好
- 类型安全和类型推断，例如 `apple.valueForKeyPath(color)` 返回的类型是 Any，`apple[keyPath: \Apple.color]` 直接返回 UIColor 类型
- 可以在所有值类型上使用

###4. 下标支持泛型 

在Swift4之前，下标属性只能返回Any类型，现在可以添加泛型约束

```swift
struct School<Key:Hashable , Value> {
    var studens:[Key: Value]
    init(studens:[Key: Value]) {
        self.studens = studens
    }
    subscript<Value>(key: Key) -> Value? {
        return studens[key] as? Value
    }
}
extension ViewController {
    func test4(){
        let tfboys = School(studens: ["001":"王俊凯","002":"易烊千玺","003":"王源"])
        let name:String? = tfboys["001"]
        print("\(name ?? "")") //王俊凯
    }
}
```

### 5. 字符串

#### 5.1 可以直接获取字符串长度了

Swift3中想要获取字符串的长度必须： `str.characters.count`  ,现在可以`str.count`直接获得了。

Swift 3 中的 String 需要通过 characters 去调用的一些属性方法，在 Swift 4 中可以通过 String 对象本身直接调用。

#### 5.2 字符串获取子串语法糖`…` One-sided Slicing

 Swift3中获取子串

```swift
let values = "欢迎关注微信公众号：乐Coding"
let startIndex = values.index(values.startIndex, offsetBy: 3)
let subvalues = values[startIndex..<values.endIndex]
```

Swift4中可以使用`...`语法

```swift
extension ViewController{
    func test5() {
        let str = "欢迎关注微信公众号：乐Coding"
        let start = str.index(str.startIndex, offsetBy: 2)
        let subStr = str[start...]
        let subStr2 = str[...start]
        print("\(subStr)  : \(subStr2)") //关注微信公众号：乐Coding  : 欢迎关
    }
}
```

#### 5.3  String增加了Collection的一些特性

在Swift4中字符串做了一些Collection类似功能函数的扩展（并不是实现Collection协议，而是使用起来更像Collection，更友好）。

```swift
func test6()  {
      //1.0 字符串翻转
      let str = "Backwards"
      let str2 = str.reversed() //ReversedCollection<String>
      let reversedWord = String(str2)
      print(reversedWord) //sdrawkcaB
      //2. 0 遍历字符串
      for char in str {
          print(char, terminator: "") //Backwards
      }
      //3.0 Map
      print("\n---------- Map --------")
      _ = str.map {
          print($0.description+"...")
      }
      /* 打印
       B...
       a...
       c...
       k...
       w...
       a...
       r...
       d...
       s...
       */
      //4.0 Filter
      print("\n---------- Filter --------")
      let filtered = str.filter {
          $0 == "c"
      }
      print("\(filtered)") //c
      //5.0 Reduce
      let letters = "abracadabra"
      let letterCount = letters.reduce(into: [:]) { counts, letter in
            counts[letter, default: 0] += 1
       }
      print("\(letterCount)") //["b": 2, "a": 5, "r": 2, "d": 1, "c": 1]

  }
```

#### 5.4 多行字符串

swift3中写多行字符串只能在字符串中添加`\n`,易读性不好。Swift4中新增加了多行字符串语法```""" ```，注意```"""```要单独占一行。

```swift
func test7(){
    let mutableLineStr =
    """
    小明：
        你妈叫你回家吃饭。
                    小红。
    """
    print(mutableLineStr)
}
```

打印结果：

![1016.png](http://upload-images.jianshu.io/upload_images/1159872-5173e428e7d17276.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###6. 序列化的简化 

原来对象序列化，需要对象遵守`NSCoding`协议并实现下面三个方法，比较麻烦。

```objective-c
- (id)initWithCoder:(NSCoder *)coder;
- (void)encodeWithCoder:(NSCoder *)coder;
- (id)copyWithZone:(NSZone *)zone;
```

Swift4中只需要对象实现Codable协议。然后选择把对象转成`Json(JSONEncoder)`还是`Plist(PropertyListEncoder)`存储。

```swift
struct Animal:Codable {
    var name: String
    var age: Int
}

extension ViewController {
    func test8(){
        let cat = Animal(name: "wildcat", age: 2)
        //编码，存储
        if let encoded = try? JSONEncoder().encode(cat) {
            UserDefaults.standard.set(encoded, forKey: "MyCat")
            UserDefaults.standard.synchronize()
        }
        //获取，解码
        let decode = UserDefaults.standard.object(forKey: "MyCat") as? Data
        if let cat2 = try? JSONDecoder().decode(Animal.self, from: decode!){
            print("\(cat.name)") //wildcat
        }
    }
}
```

###7.  Dictionary and Set增强

#### 7.1 通过Sequence初始化

字典通过Sequence创建的函数声明：

```swift
public init<S>(grouping values: S, by keyForValue: (S.Element) throws -> Key) rethrows where Value == [S.Element], S : Sequence
```

下面以数组转字典为例，字典的key是元素在数组中下标

```swift
let students = ["Kofi", "Abena", "Efua", "Kweku", "Akosua"]
let dic  = Dictionary(grouping: students) { (element) -> Int in
    return students.index(of: element) ?? 0
}
print("dic: \(dic)")
//dic: [4: ["Akosua"], 2: ["Efua"], 0: ["Kofi"], 1: ["Abena"], 3: ["Kweku"]]
```

#### 7.2 字典过滤后类型不变

在Swift3中Dictionary调用`filter`后会返回一个数组，但我们肯定希望还是返回一个过滤后的Dictionary，在Swift4中按照我们希望的做了修改。

```swift
let cities = ["Shanghai": 24_256_800, "Karachi": 23_500_000, "Beijing": 21_516_000, "Seoul": 9_995_000];
let massiveCities = cities.filter { $0.value > 10_000_000 }
print("massiveCities: \(massiveCities)")
//massiveCities: ["Shanghai": 24256800, "Karachi": 23500000, "Beijing": 21516000]

//在Swift3中massiveCities返回的是一个数组，要访问massiveCities中的元素必须massiveCities[0].value。Swift4中变成我们期望的返回一个字典，可以massiveCities["Shanghai"]直接取
```

#### 7.3 新增mapValues函数

先看下面例子：

```swift
let populations = cities.map { $0.value * 2 }
print("\(populations)")
//[48513600, 47000000, 43032000, 19990000]
```

 我们原本希望populations返回的还是一个字典，只是值变成2倍，可是map在Swift4中并没有这么改，而是添加了一个`mapValues()`函数

```swift
let roundedCities = cities.mapValues { "\($0 / 1_000_000) million people" }
print("\(roundedCities)")
//["Shanghai": "24 million people", "Karachi": "23 million people", "Beijing": "21 million people", "Seoul": "9 million people"]
```

#### 7.4 字典支持默认值

下面的`let name =person["name", default: "Anonymous"]`默认值语法，类似于`let name = person["name"] ?? "Anonymous"`

```swift
let person = ["name": "Taylor", "city": "Nashville"]
let name = person["name", default: "Anonymous"] //等价于 let name = person["name"] ?? "Anonymous"
print("\(name)") // Taylor
let create = person["create", default: "1984-01-01"]
print("\(create)") // 1984-01-01
```

还可以看一个统计数组中元素出现次数的例子：

```swift
let favoriteTVShows = ["Red Dwarf", "Blackadder", "Fawlty Towers", "Red Dwarf"]
var favoriteCounts = [String: Int]()
for show in favoriteTVShows {
    favoriteCounts[show, default: 0] += 1
}
print("favoriteCounts: \(favoriteCounts)")
```

### 8. MutableCollection新增swapAt(::) 用来交换两个位置的值

```swift
func test10() {
  var array = ["王俊凯","易烊千玺","王源"]
  array.swapAt(0, 1)
  print("\(array)")
  //["易烊千玺", "王俊凯", "王源"]
}
```
**觉得不错请点击下方【喜欢】**，为了微博认证也是拼了!还差1660个🤣

-------
更多iOS、Swift、iOS逆向最新文章请关注微信公众账号：`乐Coding`，或者微信扫描下方二维码关注

![lecoding](http://upload-images.jianshu.io/upload_images/1159872-1bb43add674a10d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)icon.jpg

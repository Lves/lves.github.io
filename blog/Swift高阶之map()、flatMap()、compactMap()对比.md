### map() vs flatMap() vs compactMap()



Swift提供给我们的`map()`, `compactMap()` 和 `flatMap()`，虽然他们听起来相似，但是却做着不同的事。这篇文章我们将看看`map()`, `compactMap()` 和 `flatMap()`每个函数是做什么的以及什么时候使用。

这三个函数都含有的"map"的原意是：把一个东西转换成另外一个东西。所以下面的代码是帮我们把数组中的每个数字*2:

```swift
let numbers = [1, 2, 3, 4, 5]
let doubled = numbers.map { $0 * 2 }
```

上面代码从数组中的依次拿到每个值，并通过我们的闭包运行它，其中`$0`指的是问题中的每个数字。从容器中取出一个值，使用您指定的代码对其进行转换 ，然后将结果放回容器中 。这里表示从数组中取出一个数字，将其加倍，然后将结果放回新的数组中，因此它将是1 * 2、2 * 2、3 * 2，以此类推。



`map()`可以返回和原来数组中数据类型不同的类型，所以我们可以把一个Int数组转成字符串数组。

```swift
let wizards = ["Harry", "Hermione", "Ron"]
let uppercased = wizards.map { $0.uppercased() }
```

`map()`可以返回和原来数组中数据类型不同的类型，所以我们我们可以把一个Int数组转成字符串数组。

```swift
let numbers = [1, 2, 3, 4, 5]
let strings = numbers.map { String($0) }
// strings：["1", "2", "3", "4", "5"]
```

如果我们执行反向操作，事情就会变得有些棘手。例如我们尝试将这些字符串转换回整数。字符串可以包含任何值：“ 1”，“ 5”和“ 500”都是可以安全地转换为整数的字符串，而“ Fish”则不能。所以将字符串转换为整数将返回**optional类型的**整数。



看下面的操作，代码使用map把String数组转成`optional Int`数组。

```swift
let maybeNumbers = strings.map { Int($0) }
// [Optional(1), Optional(2), Optional(3), Optional(4), Optional(5)]
```

### compactMap(): 转换并解包

操作可选类型会很烦人，但是`compactMap（）`函数可以使事情变得更简单：它先执行转换（名称中的“map”部分），随后丢弃结果为`nil`的值。

因此，以下代码是将上面的字符串转换为整数，但是会返回一个整数数组而不是一个可选整数数组：

```swift
let definitelyNumbers = strings.compactMap { Int($0) }
//[1, 2, 3, 4, 5]
```

Swift中有很多返回可选内容的地方，包括“ try？”，“ as？”以及任何可失败的初始化程序，例如根据字符串创建整数。这些都是选择compactMap（）的理想地方。

例如，如果您有一个“UIView”，并且想读出所有它的子视图，可以像下面这样写：

```swift
let imageViews = view.subviews.compactMap { $0 as? UIImageView }
```

或者您有一个字符串数组，并且想知道哪些是有效的URL，则可以编写以下代码：

```swift
let urls = urlStrings.compactMap { URL(string: $0) }
```

> 小结：map（）将从容器中取出一个值，使用您指定的代码对其进行转换，然后将其放回容器中。 `compactMap（）`做同样的事情，但是如果您的转换返回一个可选值，它将被解包并丢弃所有`nil`值。

### Optional类型执行 map(): 只转换非nil的值



如果您想一下会发现，可选对象类似于数组：它们也是一个包含某些内容的容器。当我们查看可选容器内部时（解开可选容器时），我们要么找到一个值，要么找到`nil`。

这意味着`map（）`方法也适用于可选对象：将可选值从其容器中取出，用我们提供的闭包对其进行转换，然后将其放回容器中（另一个可选）。如果可选参数为空，则map() 会自动执行操作并返回nil。

为了演示这一点，假设我们有一个`getUser（）`方法，该方法接受一个整数并返回具有该ID的用户名（如果存在）。如果不存在，它将发送回“nil”，因此此方法将返回一个可选字符串。

我们可以使用map() 对其进行转换：

```swift
let name: String? = getUser(id: 97)
let greeting = name.map { “Hi, \($0)” }
print(greeting ?? “Unknown user”)
```

因此，如果`name`包含字符串，则`map（）`会将其拼接一个“Hi, ’‘+ name的形式放存到greeting`数组中。

将值放回可选值中，以便以后使用时可以确定它：“也许它有一个值，也许没有”。在这种情况下，`print（）`函数将打印问候或打印`Unknown user，`而不是我们更早地强制“Unknown user”。



### flatMap(): 转换并展平

我们已经看到`map()`将整数数组转换为加倍的整数数组，将整数数组转换为字符串数组，并将字符串数组转换回整数数组， 最后一个转换返回了可选整数。下面我们将研究`compactMap（）`会如何执行相同的转换，然后解开可选的变量并丢弃`nil`值的。

我们看到了map（）在处理包含可选对象的数组时：如果它是非空的将依次执行解包、转换和重新包装，但是如果它为'nil'，则保持'nil'值不变。

现在思考以下代码：

```swift
let number: String? = getUser(id: 97)
let result = number.map { Int($0) }
```

逐步执行–运行后会产生什么结果？

- number是可选字符串。
- map() 将从可选值中取出该值并将其转换。
- 在这种情况下，Int（$ 0）会将字符串转换为可选整数，因为字符串可能是非数字形式，例如“ Fish”。
- 然后，map() 将该可选值放回另一个可选值中。

当该代码运行完，结果将不是 `Int` 也不是`Int?` –这将是一个`Int ??`，这是一个可选的可选整数。 一般来说，出现可选的可选内容的地方都应该重新考虑。

明确地说，可选的可选意味着：

1. 外部可选项可能存在，内部可选项可能存在。

2. 外部可选项可能存在，但内部可选项可能为nil。

3. 外部可选项可能为nil，这意味着没有内部可选项。

   

Optional Optionals的使用非常令人困惑，这就是`flatMap（）`出现的原因：它不仅执行转换，随后将返回的内容展平，从而使“ Optional optional”类型 变为“可选类型”。

就是说，要么整个返回结果存在，要么什么都不存在。它将双倍可选类型简化为单倍可选类型。最终，我们不在乎外部可选还是内部可选，仅在乎结果是否存在值，这就是为什么flatMap（）如此有用的原因。

此代码执行结果会将`result`设置为`Int?`而不是`Int??`：

```swift
let number: String? = getUser(id: 97)
let result = number.flatMap { Int($0) }
```

>译者注：关于flatmap( ) 还有一个更直观的例子

```swift
let numbersCompound = [[1,2,3],[4,5,6]];
var res = numbersCompound.map { $0.map{ $0 + 2 } }
// [[3, 4, 5], [6, 7, 8]]
var flatRes = numbersCompound.flatMap { $0.map{ $0 + 2 } }
// [3, 4, 5, 6, 7, 8]
```

对于二维数组， map 和 flatMap 的结果就不同。



### Mapping and flat mapping: universal friends

' map() '和' flatMap() '不仅仅可以处理数组和可选类型，对允许的类型我们有一个通用名称:functors（函子）和monads（单子）。

名字听起来很大，但只需几分钟即可了解：

- [https://www.hackingwithswift.com/example-code/language/what-is-a-functor](https://www.hackingwithswift.com/example-code/language/what-is-a-functor)
- [https://www.hackingwithswift.com/example-code/language/what-is-a-monad](https://www.hackingwithswift.com/example-code/language/what-is-a-monad)

这些文章概述了函子和单子的基本规则，很有可能您会说“这些规则很明显！”是的，它们*很明显*，但这并没有使它们变得不那么重要。我们可以添加 一个名为“ map（）”的方法应用于我们想要的任何东西，但这并不能自动使其成为函子。

>译者注：关于函子和单子大家可以上网查询。

翻译自：https://www.hackingwithswift.com/articles/205/whats-the-difference-between-map-flatmap-and-compactmap

---------

建了个【Swift开发交流】微信群，欢迎加入。

![31572961609_.pic_hd.jpg](https://upload-images.jianshu.io/upload_images/1159872-ddd9cd06088de01f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




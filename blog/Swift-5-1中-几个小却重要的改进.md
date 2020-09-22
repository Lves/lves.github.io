`Swift 5.1`已正式发布一段时间了，尽管它只是一个次要小版本，却包含了大量的更改和改进。本周，让我们来看看其中的四个特性，以及它们在哪些情况下可能有用。

### 具有默认值的成员初始化器

在Swift中，`结构体`如此吸引人的许多因素之一是他们拥有自动生成的`成员变量构造器`。成员变量构造函数，使我们能够简单地通过传递与其每个属性对应的值来初始化结构体(不包含私有存储的属性)，如下所示：

```swift
struct Message {
    var subject: String
    var body: String
}

let message = Message(subject: "Hello", body: "From Swift")
```

自动生成的构造函数在Swift 5.1中得到了显著改进，因为它们现在考虑了默认属性值，并自动将这些值转换为默认的构造函数参数。

假设我们扩展上面的Message结构体，添加一个`attachments`和`body`属性，`attachments`有一个空数组的默认值，body属性有一个`""`空字符串默认值：

```swift
struct Message {
    var subject: String
    var body = ""
    var attachments: [Attachment] = []
}
```

在`Swift5.0`和更早版本中，想要实例化这个结构体，我们必须为上述所有属性传递初始化参数，不管它们是否有默认值。但是，在`Swift5.1`中，我们不需要这么做了，我们现在可以初始化`Message`仅传入一个`subject`，就像这样：

```swift
var message = Message(subject: "Hello, world!")
```

这真的很酷，我们初始化`结构体`比以前更加方便了。 但是，也许更酷的是，就像使用标准默认参数时一样，我们仍然可以通过为其传递参数来覆盖任何默认属性，这提供了很大的灵活性：

```swift
var message = Message(
    subject: "Hello, world!",
    body: "Swift 5.1 is such a great update!"
)
```

但是，尽管成员初始化方法在`App`或`Module`中非常有用，但它们仍未作为`Module`公共API的一部分公开，这意味着，如果我们创建`frameork`或者`pod`库时，仍然需要我们`手动`创建构造函数并添加`public`。

## 使用Self引用封闭类型

以前Swift的`Self`关键字（实际是类型）使我们能够在不知道具体类型的上下文中动态引用类型，例如，在协议扩展中引用协议的实现类型：

```swift
extension Numeric {
    func incremented(by value: Self = 1) -> Self {
        return self + value
    }
}
```

现在它仍然可行，并且Swift5.1扩展了`Self`的使用范围，使`Self`包括具体类型（例如枚举、结构体和类），让我们能够将`Self`当做类型访问方法或属性的别名，像这样：

```swift
extension TextTransform {
    static var capitalize: Self {
        return TextTransform { $0.capitalized }
    }

    static var removeLetters: Self {
        return TextTransform { $0.filter { !$0.isLetter } }
    }
}
```

我们现在可以使用上面的`Self`而不是完整的`TextTransform`类型名称，这当然是一种`语法糖`，但它可以使我们的代码更加紧凑，尤其是在处理长类型名称时 。 我们甚至可以在方法或属性中内联使用`Self`，从而使上述代码更加紧凑：

```
extension TextTransform {
    static var capitalize: Self {
        return Self { $0.capitalized }
    }

    static var removeLetters: Self {
        return Self { $0.filter { !$0.isLetter } }
    }
}
```

除了引用`类型`本身，我们现在还可以使用`Self`来访问静态的方法和属性，这在我们要在类型的所有实例之间重用相同值的情况下非常有用。 在下面例子中，使用`Self`访问`cellReuseIdentifier`：

```swift
class ListViewController: UITableViewController {
    static let cellReuseIdentifier = "list-cell"

    override func viewDidLoad() {
        super.viewDidLoad()

        tableView.register(
            ListTableViewCell.self,
            forCellReuseIdentifier: Self.cellReuseIdentifier
        )
    }
}
```

当然，我们可以在访问`静态属性`时简单地在上面使用`ListViewController`，但是使用`Self`确实可以提高代码的可读性，并且在我们`重命名`视图控制器时，不必更新访问其静态成员的方式。。

## switch可选值

接下来，让我们看一下`Swift 5.1`如何让可选对象的执行`模式匹配`变得更加容易的。 举例来说，假设我们正在开发一个包含`Song`模型的音乐类App，`Song`具有可选的`downloadState`属性：

```
struct Song {
    ...
    var downloadState: DownloadState?
}
```



上面属性是可选属性的原因是，我们希望`nil`表示缺少下载状态，也就是说，一首歌根本没有下载。

`Swift`的高级模式匹配功能使我们能够直接访问一个可选值-无需先将其拆包，但是，在Swift 5.1之前，这样做需要我们在每个匹配项的后面添加一个问号，如下所示：

```swift
func songDownloadStateDidChange(_ song: Song) {
    switch song.downloadState {
    case .downloadInProgress?:
        showProgressIndiator(for: song)
    case .downloadFailed(let error)?:
        showDownloadError(error, for: song)
    case .downloaded?:
        downloadDidFinish(for: song)
    case nil:
        break
    }
}
```

在Swift 5.1中，不再需要那些尾随的问号，我们现在可以直接引用每种情况，就像遍历非可选值时一样：

```swift
func songDownloadStateDidChange(_ song: Song) {
    switch song.downloadState {
    case .downloadInProgress:
        showProgressIndiator(for: song)
    case .downloadFailed(let error):
        showDownloadError(error, for: song)
    case .downloaded:
        downloadDidFinish(for: song)
    case nil:
        break
    }
}
```



## 有序集合的差异

最后，让我们看一下作为Swift 5.1的一部分引入的全新标准库API，有序集合差异（`ordered collection diffing`）。 随着Combine和SwiftUI之类的工具越来越接近声明式编程的世界，能够计算两种状态之间的差异变得越来越重要。毕竟，`声明式`UI开发就是关于不断呈现状态的新快照。

例如，假设我们正在构建一个`DatabaseController`，它使我们能够通过内存中的Model轻松地更新磁盘上的数据库。 为了弄清楚是应该插入还是删除`Model`，我们现在可以简单地调用新的`difference(from:)`API来计算旧数组与新数组之间的差异，然后依次遍历该差异中的更改执行我们的数据库操作：

```swift
class DatabaseController<Model: Hashable & Identifiable> {
    private let database: Database
    private(set) var models: [Model] = []
    
    ...

    func update(with newModels: [Model]) {
        let diff = newModels.difference(from: models)

        for change in diff {
            switch change {
            case .insert(_, let model, _):
                database.insert(model)
            case .remove(_, let model, _):
                database.delete(model)
            }
        }

        models = newModels
    }
}
```

但是，上述实现并未考虑已移动的`Model`，因为默认情况下，移动将被视为单独的`插入`和`删除`。 为了解决这个问题，我们在计算差异时也要调用`inferringMoves()`方法，然后查看每个插入是否与`移除`关联，如果这样，则将其视为`移动`，如下所示：

```swift
func update(with newModels: [Model]) {
    let diff = newModels.difference(from: models).inferringMoves()
    
    for change in diff {
        switch change {
        case .insert(let index, let model, let association):
            // If the associated index isn't nil, that means
            // that the insert is associated with a removal,
            // and we can treat it as a move.
            if association != nil {
                database.move(model, toIndex: index)
            } else {
                database.insert(model)
            }
        case .remove(_, let model, let association):
            // We'll only process removals if the associated
            // index is nil, since otherwise we will already
            // have handled that operation as a move above.
            if association == nil {
                database.delete(model)
            }
        }
    }
    
    models = newModels
}
```

Swift 5.1将`difference(from: )`内置到标准库（以及UIKit和AppKit中）真是太了不起了，因为编写高效，灵活和健壮的差异算法非常困难。


![logo](https://upload-images.jianshu.io/upload_images/1159872-54c44cd2b80f7827?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

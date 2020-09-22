

`Xcode11`发布后，我们一直在惊叹`SwiftUI`的强大，却忽略了`storyboard`的一些改进。据我所知，`Apple`在`WWDC`期间也没有提到过`segue`或`自定义初始化`(`initializers`)的修改。 我们可以从Xcode和`iOS 13`发行说明中看到一些修改提示。

### SegueAction

假设我们使用`storyboards`时通过Segue进行页面跳转


![001](https://upload-images.jianshu.io/upload_images/1159872-6ee2a2f521d7c8cb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



左侧的`BookControlle`r有一个`Button`，点击该按钮时，它会跳转到右侧的`PreviewController`，以显示该Book的详情。 使用`storyboard`，您可以通过从按钮拖拽到目标控制器创建`segue`实现页面跳转。


![002](https://upload-images.jianshu.io/upload_images/1159872-1475967a9c5c32f8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



要完成segue的配置，必须在属性检查器（ attributes inspector）中添加一个唯一标识符(Identifier)：




![003](https://upload-images.jianshu.io/upload_images/1159872-b7ade570da4e8e8d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



页面跳转时，我们需要将Model数据（本例中的Book）从源传递到目标ViewController。 在Xcode 10和iOS 12中，我们使用源视图控制器中的 `prepare(for:sender)` 来实现：

```swift
// BookController
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
  guard let previewController = segue.destination as? PreviewController else {
    fatalError("Missing PreviewController")
  }
  previewController.book = book
}
```

**注意，我们没有创建目标视图控制器**。 UIKit通过调用`init(coder:)` 方法来初始化新的`ViewController`。 我们可以配置数据并将其传递给`controller`，但只能在UIKit创建`controller`之后。

我的`PreviewController`有一个`Book`属性，但它是可选的：

```swift
// PreviewController
import UIKit

final class PreviewController: UIViewController {
  @IBOutlet private var textView: UITextView!

  var book: Book?

  override func viewDidLoad() {
    super.viewDidLoad()
    title = book?.title
    textView.text = book?.preview
  }
}
```

`book`属性是optional的，因为在初始化`PreviewController`期间无法设置它。我想在创建`PreviewController`时初始化`book`，并把`book`成员变量改成`let`不可修改的,怎么办呢？

`segue`是UIKit在调用segue方法期间，创建目标视图控制器的一种方法。

**从Xcode 11开始，我们还有另一种将数据传递到目标视图控制器的方法**。我们可以通过使用`@IBSequeAction`在源视图控制器中标记一个方法来创建`segue action`：

```swift
@IBSegueAction
private func showPreview(coder: NSCoder, sender: Any?, segueIdentifier: String?)
    -> PreviewController? {
    return PreviewController(coder: coder, book: book)
}
```

SegueAction方法具有三个参数。 必需的`NSCoder参数`以及可选的sender和segueIdentifier。 如果不需要，我们可以省略可选参数：

```swift
@IBSegueAction
private func showPreview(coder: NSCoder)
    -> PreviewController? {
    return PreviewController(coder: coder, book: book)
}
```

如果该方法返回`nil`，则UIKit将调用 `init(coder:)` 方法来创建视图控制器。 SegueAction不会阻止segue的发生。 无论哪种方式，都会在segue对象中传递新创建的视图控制器给`prepare(for:sender)`方法。 由于这里我们不再需要它，因此从视图控制器中删除了`prepare(for:sender)`方法。

注意，Swift 5.1允许我们省略具有单个表达式的方法的return语句，我们进一步简化SegueAction方法：

```swift
@IBSegueAction
private func showPreview(coder: NSCoder)
    -> PreviewController? {
    PreviewController(coder: coder, book: book)
}
```



要将storyboard中的`segue`连接到ViewController中的`SegueAction`方法，请从segue对象向`view controller`拖动，然后选择SegueAction方法：



![004](https://upload-images.jianshu.io/upload_images/1159872-b201bb50b99b2da6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![0041](https://upload-images.jianshu.io/upload_images/1159872-8ddac91a1733fa19?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



如果正确连接了`SegueAction`，在`segue`的属性检查器(attributes inspector)中会看到该方法的Selector变成了你定义的方法：


![005](https://upload-images.jianshu.io/upload_images/1159872-bb2887c4c325da4d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



现在，PreviewController可以创建一个自定义`initializer`方法，该初始化方法在创建视图控制器时 用我们`SegueAction`传递的Book做参数：

```swift
// PreviewController
import UIKit
final class PreviewController: UIViewController {
  @IBOutlet private var textView: UITextView!

  let book: Book

  init?(coder: NSCoder, book: Book) {
    self.book = book
    super.init(coder: coder)
  }

  required init?(coder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
  }

  override func viewDidLoad() {
    super.viewDidLoad()
    title = book.title
    textView.text = book.preview
  }
}
```



自定义初始化方法必须调用`super.init(coder:)` 并传递从`SegueAction`中接收到的`coder`参数。 另外，`Book`属性不再是`可选的`，因此我们可以将其从`var`更改为`let`。



### 自定时实例化方法

页面跳转时我们可以使用storyboards而不必使用segue。 例如，我可以将按钮连接到视图控制器中的`IBAction`方法，而不是创建segue：

```swift
@IBAction private func showPreview(_ sender: UIButton) {
  guard let previewController = storyboard?.instantiateViewController(
      withIdentifier: "PreviewController") as? PreviewController else {
      fatalError("Unable to create PreviewController")
    }
    previewController.book = book
    show(previewController, sender: self)
}
```



`showPreview`方法从`storyboard`中实例化视图控制器，进行配置然后显示。 这种方式存在与`segue`相同的一些问题。 我们调用storyboard的`instantiateViewController（withIdentifier：）`方法，然后返回一个`ViewController`。 任何Model数据（例如我们的Book）都必须是目标视图控制器的可选属性。



苹果在`iOS 13`发行说明中对这个问题进行了完善：

> You can now invoke a custom initializer from a creation block that’s passed through instantiateInitialViewController(creator:) or instantiateViewController(identifier:creator:).

例如，使用我们的自定义初始化方法传递`Model`数据：

```swift
@IBAction private func showPreview(_ sender: UIButton) {
  guard let previewController = storyboard?.instantiateViewController(
        identifier: "PreviewController", 
        creator: { coder in
        PreviewController(coder: coder, book: self.book)
      }) else {
      fatalError("Unable to create PreviewController")
    }
    show(previewController, sender: self)
}
```

`storyboard.instantiateViewController`多了一个creator参数，`creator block`提供了我们需要传递给自定义`view controller`初始化方法的`coder`，现在就把book可选参数改成`let`了。



### 迟到总比没有好

未来可能是SwiftUI一统天下，但我仍然很高兴`storyboard`有所完善。 第一个改动使用`Xcode11`开发低版本`iOS`也可以使用，第二个改动就需要`iOS13`及以上版本支持了。 所以也许这就是`迟到总比没有好`。




> 作者：kharrison
> 翻译整理：乐Coding
> 原文地址：https://useyourloaf.com/blog/better-storyboards-with-xcode-11/ 


![image](https://upload-images.jianshu.io/upload_images/1159872-33da898f6042ac01?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

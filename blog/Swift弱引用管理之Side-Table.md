
![Photo by Content Pixie](https://upload-images.jianshu.io/upload_images/1159872-e621831318a7dff8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`Side Table`的引入是`Swift`弱引用管理系统中的一个明智改进，它最早出现在`Swift 4`中。

让我们仔细研究一下`Side Table`的概念以及它解决了哪些问题。

### Swift引用计数 - 核心概念

- 强引用对象会持有这个实例，并且只要强引用仍然存在，就不允许对其进行释放。
- 弱引用是一种引用，它不会持有所引用的实例，因此也不会阻止ARC销毁所引用的实例。
- `Unowned`引用方式也是弱引用的一种。 区别是访问计数器为零的引用会导致运行时错误。 当另一个实例的寿命相同或更长时，可以使用它。



同样值得注意的是，Swift中的对象有三种计数器：强引用，弱引用和无主引用。 这些计数器存储在`isa`之后或者存储在`Side table`中，稍后将对其进行说明。

除此之外，弱引用和无主引用的计数器比强引用多+1。 当对象执行完析构和销毁内存后，这个附加值将减小。 为了使本文简单，我将使用0作为两种类型引用的起点。

### Swift4之前的弱引用管理

为了演示旧引用计数的工作原理，让我们从一个示例入手。

```
class User {
    let id: Int
    let email: String
}
```

这是一个带有两个属性的`User`类。 下面的图片表示对象在内存中的表示。


![image](https://upload-images.jianshu.io/upload_images/1159872-007b7a8248e4f36b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

类，属性和引用计数器是内联存储的。 它比将数据存储在外部内存块中要快一些。

假设有一个弱引用 引用了这个User，并且一段时间后，强计数器变为了零，而弱计数器仍然不为零。

![layout2](https://upload-images.jianshu.io/upload_images/1159872-9d96f5e0d2e939ff?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这种情况下，自动引用计数将会把该对象从内存
中删除。 尽管这样会导致对象销毁，但不会释放其内存。



当另一个对象通过弱引用访问被销毁的对象时，弱计数器将减少。 当弱计数器达到零时，将最终释放内存。 这意味着我们的User成为可能长时间占用内存的僵尸对象。

### Swift 的 Side Table

`Side Table`是一个单独的内存块，用于存储对象的其他信息。 现在它只存储了计数器和flags。

![layout3](https://upload-images.jianshu.io/upload_images/1159872-d832c0af367be9b4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


该对象最初是没有`Side Table`的，在以下情况下会自动创建：

- 对象由弱引用指向时
- `strong`或 `unowned` 计数器溢出（在32位系统上，嵌入式计数器很小）。

对象和`Side Table`都具有指向彼此的指针。 获取`Side Table`是一种单向操作。



另一件需要引起注意的事，“弱”引用现在直接指向`Side Table`，而“强”和“无主”引用仍然直接指向对象。 这样就可以**完全释放对象的内存**。


![layout4](https://upload-images.jianshu.io/upload_images/1159872-793ce2f6cb16286a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 总结

`Side Table` 为原来的引用计数带来了明显的改进：

- 首先，它允许弱引用指向的对象按时释放而不会存在僵尸对象。
- 其次，它可能会在将来的发行版中为扩展其他存储属性打开大门。

### 推荐阅读

- Apple Swift Book — Automatic Reference Counting   https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html#ID52
- Swift Source Code — RefCount.h    https://github.com/apple/swift/blob/d1c87f3c936c41418ee93320e42d523b3f51b6df/stdlib/public/SwiftShims/RefCount.h#L44
- 阿里大牛从源码解析弱引用 https://juejin.im/post/5c7b835af265da2d881b4457



>作者：Maxim Eremenko
>
>翻译：乐Coding


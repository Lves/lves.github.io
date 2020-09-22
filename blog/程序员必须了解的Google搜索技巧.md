为了能按时交付项目开发人员的工作效率很重要。在此博客文章中，我将为你提供一些技巧和窍门，我每天都会使用这些技巧和窍门来更快地找到答案。

### 使用通配符`*`

当你使用Xcode开发过程中遇到问题时，通常会遇到一条包含特定类型的错误消息。


![1](https://upload-images.jianshu.io/upload_images/1159872-ade989067190eddf?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

尽管你用错误直接搜索也可能会找到答案，但并不是每次都管用。 为此，你可以使用通配符`*`。 只需将本示例的特定类型替换为`*`，例如本示例中的`Coyote.HomeViewController(0x1010b01f0)`和`Coyote.BucketListViewController(0x10bd7f580)`这两句中包括你的App名称，类名和指针，它们对于你的应用程序都是唯一的。 你可以在Google中这样搜索：


![2](https://upload-images.jianshu.io/upload_images/1159872-899932d85665277c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


通过上述改进，搜索结果总计为578.000条，而没有用`*`通配符搜出来的结果为2条。 正如你在上面的示例中看到的那样，搜索结果更加精确，而且满足你的需求！

### 使用特定域

如果你知道要搜索网站的域名，则可以进一步提高开发人员的工作效率。 如果你要搜索某段代码示例，则可能要在`Stack Overflow`上进行搜索。 还是上面的示例，加上`site:`后搜索结果如下：


![3](https://upload-images.jianshu.io/upload_images/1159872-4c5a120b1b83bc09?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结果数减少到810条。 结果更加具体地针对我们的案例，并且更有可能包含我们所寻找的答案。

### 添加“solved”关键词

尽管`solved`不是Google官方支持的功能，但它会帮助过滤未解决的那些答案，确实提高了工作效率。


![4](https://upload-images.jianshu.io/upload_images/1159872-a7166f67688c5673?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这将结果的数量减少到295条，并使我们更接近正确的答案。

如果你想知道为什么我将`solved`关键字放在开头而不是结尾，因为这样确实有用！ 关键字的顺序会影响优先级并带来不同的结果。 实际上，以上示例中最后使用`solved`的第一个结果并未在Stack Overflow上标记为solved。 但是Google足够聪明，可以根据其数据确定答案是否正确。

#### 使用“accepted”代替“solved”

有趣的是，搜索“ Stack Overflow”上的内容时，使用`accepted`关键词比`solved`效果更好，因为Stack Overflow会将答案标记为`accepted`，而不是`solved`。 但是，`solved`关键词通常也可以搜索到其他网站上的结果。

### 直接在Stack Overflow上进行搜索是否会进一步提高我们的效率？

好吧，你可能会认为是对的。 但是，在stackoverflow的搜索页面搜索相同的问题并没有用。 即使只搜索Swift相关的答案并包含`accepted`关键词，搜索结果也经常令人失望。


![5](https://upload-images.jianshu.io/upload_images/1159872-aa3e08841656bb1b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

经过我们测试Stack Overflow是这样的，其他网站可能会好点，也可能存在相同的问题。 因此，这要视情况而定，有时你必须自己尝试比较才知道。

### 其他搜索例子

上面的示例非常适合我们代码出问题时搜索答案。 但是，有时你想搜索其他东西，例如Swift文档库，你可以这样搜索：


![6](https://upload-images.jianshu.io/upload_images/1159872-b535677dd4d30db8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 作者：twannl
>
> 翻译：乐Coding

更多iOS、Swift开发技巧请关注公众账号：乐Coding


![logo](https://upload-images.jianshu.io/upload_images/1159872-95b982219c50f761?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

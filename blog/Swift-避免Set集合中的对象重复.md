![coding-man-2.jpg](https://upload-images.jianshu.io/upload_images/1159872-5e39ea32c6d3a631.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Julian Schiavo 写道：“我使用Set来确保我集合中的值唯一，但是Set中的每个对象都有一个date变量。 刷新数据会更新Date，这样就会导致重复。 有什么推荐的解决方案吗？”

这个问题很好，Swift的协议可帮助我们打造一个真正的智能解决方案。

首先，让我们看一个示例代码。 这个示例是一个“ NewsStory”结构体，该结构包含id、title和date：

```swift
struct NewsStory {
    var id: Int
    var title: String
    var date = Date()
}
```

如您所见，我给date设置当前日期为默认值。

使用该结构，我们可以创建三个实例：

```swift
let story1 = NewsStory(id: 1, title: "What's new in Swift 5.1?")
let story2 = NewsStory(id: 2, title: "What's new in Swift 6.0?")
let story3 = NewsStory(id: 3, title: "What's new in Swift 6.1?")
```

Julian 希望将这些数据存储在[Set而不是Array](https://www.hackingwithswift.com/example-code/language/when-to-use-a-set-rather-than-an-array )中，这是一个明智的选择。 因此，我们想编写如下代码：

```swift
var stories = Set<NewsStory>()
stories.insert(story1)
stories.insert(story2)
stories.insert(story3)
print(stories)
```

上面代码将创建NewsStory的Set集合体，添加三个NewsStory，然后将其打印出来。 但是，该代码无法编译：为了使Set能够唯一地标识每个成员，我们需要使NewsStory遵守Hashable协议，以便它可以生成表示每个新闻内容的唯一哈希值 。

Swift在这里真的很聪明，因为如果自定义类型的全部属性都是可哈希的，给自定义类型添加“`Hashable`”协议后，它就可以自己完成剩下的工作，从而为我们计算自定义类型的哈希值。 因此，我们需要将`NewsStory`结构更新为：

```swift
struct NewsStory: Hashable {
    var id: Int
    var title: String
    var date = Date()
}
```

现在我们的代码可以运行了，到目前为止还不错。

然而当下面的代码执行后，Julian的问题就出现了。

```swift
let story4 = NewsStory(id: 1, title: "What's new in Swift 5.1?")
stories.insert(story4)
print(stories)
```

这样会创建另一个具有相同`id`和`title`的“ NewsStory”实例，并将其添加到`Set`中，然后打印出集合的内容。现在您将在其中看到四个NewsStory，这其中有一个是 重复的。

就像我之前说的，当您让仅包含“哈希”属性的类型遵守“`Hashable`”协议时，Swift将完成为我们计算哈希值所需的所有工作。 它使用的公式很简单：获取我们类型中所有属性的哈希值，并将它们组合在一起。

因此，客观上我们认为两个`NewsStory`相同时它们具有相同的title和id。但是Swift认为它们是不同的，因为它还考虑了日期，即使日期之间的差异很小 。

我们需要做的是为Swift提供一个自定义的相等规则。如果两个NewsStory它们的id和title相同,我们就认为他们是一个对象，不管它们的日期是什么。

为此，我们需要给“ NewsStory”添加两个方法：一个用于生成自定义哈希值，另一个用于检查两个“ NewsStory”是否相同。

第一个需要实现的方法是用id和title生成哈希值：

```swift
func hash(into hasher: inout Hasher) {
    hasher.combine(id)
  	hasher.combine(title)
}
```

第二个需要实现的方法是运算符重载“ ==”，该比较检查 id和title是否相等：

```swift
static func ==(lhs: NewsStory, rhs: NewsStory) -> Bool {
    return lhs.id == rhs.id && lhs.title == rhs.title
}
```

终于完成了，而且此新版本比Swift的自动合成方法执行得更快，因为我们现在仅散列并检查重要的属性，而不是所有属性。

当然，我们在这里仅使用`id`和`title`属性，但是您可以根据需要哈希任意数量的属性以确保实例不同。

这是“ NewsStory”结构的最终代码：

```swift
struct NewsStory: Hashable {
    var id: Int
    var title: String
    var date = Date()

    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
      	hasher.combine(title)
    }

    static func ==(lhs: NewsStory, rhs: NewsStory) -> Bool {
        return lhs.id == rhs.id && lhs.title == rhs.title
    }
}
```



重要提醒：当两个对象因为我们自定义“ ==”的方法返回true而被视为相同时，Swift可以自由选择其中一个。 它可能总是选择第一个，也可能总是选择第二个，或者每次都选择一个不同的，并且这种行为可能会在以后的Swift版本中改变。 记住，我们告诉Swift，对象是相同的，但是如果对象的选择很重要，那么您就会遇到问题！

> 文章作者：Paul Hudson
>
> 翻译：阿乐
>
> 英文文章地址：https://www.hackingwithswift.com/articles/199/avoiding-near-duplicates-in-sets

---------

最新Swift文章请关注作者的公众账号：`乐Coding`。
建了个【Swift开发交流】微信群，欢迎加入。

![31572961609_.pic_hd.jpg](https://upload-images.jianshu.io/upload_images/1159872-ddd9cd06088de01f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

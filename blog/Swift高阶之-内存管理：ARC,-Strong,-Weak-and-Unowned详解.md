

![image](https://upload-images.jianshu.io/upload_images/1159872-ff250bafef18e88b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

内存管理是任何编程语言中的核心概念。 尽管有很多教程解释了Swift自动引用计数的基本原理，但我发现没有一个可以从编译器的角度对其进行解释。 在本文中，我们将学习iOS内存管理，引用计数和对象生命周期等基础知识之外的内容。

让我们从基础开始，逐步进入ARC和Swift Runtime的内部，首先思考以下问题：

- 内存是什么？
- Swift编译器是如何实现自动引用计数的？
- 强，弱和无主引用是如何实现的？
- Swift对象的生命周期是怎么样的？
- 什么是side table？



### 内存管理

从硬件层面，内存只是一长串字节。 在虚拟内存中它被分成三个主要部分：

- 栈区，所有局部变量都存放在哪里。
- 全局数据，其中包含静态变量，常量和类型元数据。
- 堆区，所有动态分配的对象都在其中。 基本上，所有具有生命周期的东西都存储在这里。

我们将继续交替使用“对象”和“动态分配的对象”。 这些是Swift引用类型以及值类型的一些特殊情况。

内存管理是控制程序内存的过程。 了解它的工作原理至关重要，否则您可能会遇到随机崩溃和莫名的小bug。

### ARC

`内存管理`与所有权的概念紧密相关。 所有权会决定哪些代码会造成对象被销毁[1]。

自动引用计数（ARC）属于Swift的所有权系统，它规定了一组用于管理和转让所有权的约定。

可以指向对象的变量别名叫做`引用`。 Swift引用具有两个强度级别：强和弱。 此外，弱引用包含无主引用和弱引用。

> Swift内存管理的本质是：如果一个对象被强引用指向，Swift会保留它，否则将其释放。 剩下的只是实现细节。

### 理解Strong, Weak and Unowned

强引用的目的是使对象保持存活状态。 强引用可能会导致几个有意义的问题[2]：

- *循环引用*。 考虑到Swift语言不是循环收集（cycle-collecting）的，一个对象的强引用`R`如果同时被对象强引用(可能是间接的)，则会导致循环引用。 我们必须编写大量代码来显式打破循环。
- 并非总是可以使强引用在对象构造上立即有效，例如代理（delegates）。

弱引用解决了反向引用的问题。 如果有指向对象的弱引用，则可以销毁该对象。 弱引用访问不再存在的对象时将返回nil。 这称为调零或归零（*zeroing*）。

无主引用是弱函数的另一种形式，旨在用于严格的有效性不变式。 无主引用是非归零的。 当试图通过无主引用读取不存在的对象时，程序将因断言错误而崩溃。 它们用于跟踪和修复一致性问题很有用。

```
class MyClass {	
    lazy var foo = { [weak self] in	
        // Must be validated	
        guard let self = self else { return }	
        self.doSomething()	
    }()	
    func doSomething() {}	
}
```

无主引用无需在使用时进行验证：

```
lazy var bar = { [unowned self] in	
  // No validation needed
  self.doSomething()	
}()	
```

在这个示例中，使用无主引用是明智的，因为属性`bar`和`self`具有相同的生存期。

我们对Swift内存管理的进一步讨论会处于较低的抽象层面。 我们将深入研究如何在编译器级别实现ARC，以及每个Swift对象在销毁之前要经历的步骤。

### Swift Runtime

`ARC`机制在`Swift Runtime`库中声明。 它包含了诸如`运行时类型系统`之类的核心功能，例如：动态转换，泛型和协议一致性注册[3]

Swift Runtime 使用`HeapObject`结构体表示每个动态分配的对象。 它包含构成Swift对象的所有数据：引用计数和类型元数据。

`HeapObject`中每个Swift对象都有三个引用计数：每种引用都有一个。 在SIL生成阶段，*swiftc*编译器会在适当的地方插入`swift_retain()`和`swift_release()`函数。 这是通过拦截`HeapObject`的初始化和销毁来完成的。

> 编译是Xcode Build System的步骤之一

如果您是Objective-C老程序员，并且想知道`autorelease`在哪里，可以告诉你：纯Swift对象没有这个东西。



现在，让我们继续*弱引用*。 它们的实现方式与`Side table`的概念紧密相关。

> 想要详细了解SideTable，请阅读我之前的一篇文章：[Swift弱引用管理之Side Table](https://mp.weixin.qq.com/s?__biz=MzA3MTA5Mzk2MA==&mid=2664081886&idx=1&sn=12ad15e0d1b16293e5174757c38d35e6&chksm=840cd279b37b5b6f9a519345035fa1397d96decb487bc775bf57c6617806384a9f12418a2af5&token=397528633&lang=zh_CN#rd)

### Side Tables介绍

**Side tables** 是实现Swift弱引用的核心。

大多数情况，对象没有任何“弱”引用，因此为每个对象中的弱引用计数保留存储空间是浪费的。 此信息存储在外部的 `side table`中，只有在确实需要时才会分配。

弱引用变量不是直接指向对象，而是指向`side table`，而`side table`又指向对象。 这解决了两个问题：为弱引用计数节省内存，直到对象真正需要它才创建； 允许安全地将弱引用归零，因为它不会直接指向对象，并且不再是`竟态条件`的主体。

> 当两个线程竞争同一资源时，如果对资源的访问顺序敏感，就称存在竞态条件。

Side table只包含一个引用计数 和 一个对象的指针。 它们在Swift Runtime 中声明如下（C ++ 代码）[5]：

```
class HeapObjectSideTableEntry {
  std::atomic<HeapObject*> object;
  SideTableRefCounts refCounts;
  // Operations to increment and decrement reference counts
}
```



### Swift对象生命周期

Swift对象具有自己的生命周期，在下图中我用`有限状态机`表示。 方括号表示触发状态转换的条件。


![1](https://upload-images.jianshu.io/upload_images/1159872-404fbfa22192986b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在**Live**状态时，对象处于活动状态。 其引用计数被初始化为 *strong*：1， *unown*：1和 *weak*：1（side table从+1开始）。  一旦有弱引用指向对象，便会创建side table。 `弱引用`指向`side table`而不是对象。



一旦强引用计数达到零，则对象从**Live**状态进入**Deiniting**状态。 处于Deiniting状态表示`deinit()`正在进行中。 在这一点上，强引用操作无效。 如果存在关联的`side table `，通过弱引用访问将返回`nil`。 通过`unowned`访问将触发断言失败。 通过新的unowned引用仍然可以存储。 从此状态开始，可能选择两条分支：

- 快速判断如果没有weak，unowned的引用和side table。 该对象将转换为**Dead**状态，并立即从内存中删除。
- 否则，对象将变为**Deinited**状态。

在**Deinited**状态下，`deinit()`已经执行完成，该对象还有未完成的unown引用（至少是初始值：1）。 此时，通过强和弱引用进行存储和读取无法发生。 *Unowned*引用存储也不会发生。 通过*Unown*读取会触发断言错误。 该对象可以从此处进入两条分支：

- 如果没有弱引用，则可以立即释放该对象。 它过渡到**Dead**状态。
- 否则，仍然有一个`side table`要移除，并且对象进入**Freed**状态。

在**Freed**状态之前，对象已完全释放，但它的 side table仍处于活动状态。 在此阶段，弱引用计数将置0，并且 side table会被销毁。 对象将转换为最终状态。

除指向对象的指针外，在**Dead**状态下对象已被全部销毁。 指向“HeapObject”的指针也从堆中释放出来，在内存中找不到该对象的任何痕迹。


### 总结

自动引用计数并不是什么神奇的东西，我们对它越了解，我们的代码就越不容易出现内存管理错误。 这里是要记住的几个关键点：

- 弱引用指针指向side table。 无主和强引用指针指向对象。
- 自动引用计数是在编译器级别实现的。 swiftc编译器会在适当的时候插入`swift_retain()` 和`swift_release()`。
- Swift对象不会立即销毁。 它们在生命周期中经历了五个阶段：*live* -> *deiniting* -> *deinited* -> *freed* -> *dead*

> 作者：Vadim Bulavin
> 翻译：乐Coding

### 推荐

[1] : https://github.com/apple/swift/blob/master/docs/OwnershipManifesto.md

[2] : https://github.com/apple/swift/blob/master/docs/weak.rst

[3] : https://github.com/apple/swift/blob/master/docs/Runtime.md

[4] : https://github.com/apple/swift/blob/a030e6190ab98b4076474353407a0ed01d4e884b/stdlib/public/SwiftShims/RefCount.h#L1199

[HeapObject] : (https://github.com/apple/swift/blob/4fd0671e542299d7805e41cf9426640ab3b399af/stdlib/public/SwiftShims/HeapObject.h

[6] : https://github.com/apple/swift/blob/a030e6190ab98b4076474353407a0ed01d4e884b/stdlib/public/SwiftShims/RefCount.h


-----


![logo](https://upload-images.jianshu.io/upload_images/1159872-3fd7f84309dd06b2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

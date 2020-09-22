原文链接：[http://lvesli.com/2016/05/24/Objective-C-The-Preprocessor/](http://lvesli.com/2016/05/24/Objective-C-The-Preprocessor/) , 转载请到原文下留言申请。

`Objective-C`源文件在编译之前要先经过预编译器处理，然后再扔给`LLVM`处理、优化。`Objectice-C`编译器从源文件的输入到编译后的输出文件,处理过程分解后如下图：
![123.png](http://upload-images.jianshu.io/upload_images/1159872-9338649a2784e917.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如上图所示编译过程大体包括词法分析、语法分析、生成代码和优化、汇编链接，最后输出可执行的二进制文件。关于其中每个阶段的具体行为，我们就不做具体研究了，已经超出了我的能力范围（恨自己当年大学期间编译原理没有好好学啊），如果你感兴趣可以研究一下“龙书”《Compilers: Principles, Techniques, and Tools》，传说`LLVM`的作者`Chris Lattner` 曾经修炼了这本武功秘籍，还未毕业就被Apple盯上了。`龙书`真容如下（快膜拜）：
![234.png](http://upload-images.jianshu.io/upload_images/1159872-a3e7a5906cf0eb1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

今天我们主要看看和预处理器（词法分析部分）相关的内容。预处理器处理过程主要包括三个步骤：

* 文本翻译： 将输入的源文件分成代码行，并进行一些翻译处理。
* 记号转换： 将上一步的处理结果转换成记号语言。
* 基于预处理语言的转换： 如果上一步结果中含有预处理语言元素，就会进行转换。

前两部是自动处理的，第三部要根据预处理语言函数进行处理，
##预处理语言

预处理语言对源文件进行转换主要包括文件引用、条件编译、宏展开。预处理语言定义了预处理指令和宏定义。预处理指令是预处理器执行的命令和编译器无关，宏定义只是一个具有名称的一段代码，在文件中会被预处理器替换成相应代码。

###指令
指令的格式: `#指令名 指令参数` 例如

    #import "Student.h"
    #define kBackColor  [UIColor redColor]
    
指令主要包括以下四种：

* 头文件引用
* 条件编译
* 错误诊断
* Pragma

#####头文件引用

头文件引用主要由`#inlude` 和`#import` 两种。每种又分为尖括号(`<>`)引用和双引号(`" "`)引用 。

1. `#inlude`  与 `#import` 的区别是： `#import` 不会造成重复引用，它会自己检查是否已经引用过，也可以防止递归包含。
  > import的实现是通过#ifndef一个标志进行判断，然后在引入后#define这个标志，来避免重复引用的。
2. 尖括号(`<>`)引用与双引号(`" "`)引用的区别是
   * 双引号(`" "`)引用的文件，编译器会首先在存储源文件的同一目录下搜索，如果文件没有找到编译器会搜索默认目录（配置文件中配置的头文件引用目录）。
   * 尖括号(`<>`)引用 只会在默认目录下搜索。


#####条件编译
条件编译指令主要包括 (#if, #elif, #else, #endif, #ifdef, #ifndef) 利用他们可以选择性的编译代码。一个`#if`或者`#ifdef` 最后一定以`#endif`结束,中间写条件处理或者插入else指令。例如：

   #ifdef, #ifndef
 
	#ifdef DEBUG
  		 NSLog (@"File search complete. Found %i files", filecount");
	#endif
	
   #if, #elif, #else, #endif
	

	#if constant_expression
	
	#else
	
	#endif
 

or

	#if constant_expression
	
	#elif constant_expression
	
	#endif

#####诊断指令
诊断指令主要包括 `#error` 和`#warning`

	#ifndef    ifOpen
	#error "Not Open"
	#endif

如果进入到`#error`指令，则编译器不会往下执行，如果编译到`#warning`指令，只会显示一个警告信息，还会继续编译。

#####Pragma指令

`pragma` 指令之一种编译器指令，它可以在特定平台下使用，像`mark`指令可以对代码进行分段标记，让代码更容易查找和跳转到指定位置。 我在自己的Controller中经常这样`mark`如下：


	#pragma mark -
	#pragma mark Life Cycle
	
	#pragma mark -
	#pragma mark Private Method

	#pragma mark -
	#pragma mark Action

	#pragma mark -
	#pragma mark Setter&Getter

###宏定义

宏定义就是对代码段起个名字，编译器编译之前预处理器会进行简单的字符串替换。宏定义可以进行简单的替换：

    #define MY_CONSTANT 4
    
也可以定义一个带参数的代码段 

    #define SQUARE(x)  ((x) * (x))
   
因为宏定义默认只支持一样，如果要定义多行时每行结尾要加一个斜线（`\`），最后一行不用。

    #define SWAP(a, b) \
    a^=b;\
	b^=a;\
	a^=b;

宏定义`枚举`和`Block`在`OC中经常使用。宏定义会在编译之前进行处理，而且一旦定义在其作用范围内都可以引用，可以提高编程效率。宏定义功能强大，当然宏定义也不能乱用它也存在确定，比如难以维护和查错。真机测试时定义太多的宏，当修改一个值就会重新编译好久。建议经常修改的值不要使用宏。一些宏能用常量替换的尽量使用常量。

原文链接：[http://www.lvesli.com/?p=386](http://www.lvesli.com/?p=386)    微信公众账号同步更新：`lecoding`
下面列出我自己项目中经常使用的宏定义：

    //1. 打印日志
	#ifdef DEBUG
	#    define DLog(...) NSLog(__VA_ARGS__)
	#else
	#    define DLog(...)
	#endif
	
	//2. 获取屏幕 宽度、高度
	#define kScreenWidth ([UIScreen mainScreen].bounds.size.width)
	#define kScreenHeight ([UIScreen mainScreen].bounds.size.height)
	
	//3. 颜色
	#define RGB(r, g, b, a)  [UIColor colorWithRed:r/255.0f green:g/255.0f blue:b/255.0f alpha:a]
	#define HEXCOLOR(c)       [UIColor colorWithRed:((c>>16)&0xFF)/255.0f green:((c>>8)&0xFF)/255.0f blue:(c&0xFF)/255.0f alpha:1.0f]
	//背景色  
	#define BACKGROUND_COLOR [UIColor colorWithRed:242.0/255.0 green:236.0/255.0 blue:231.0/255.0 alpha:1.0]  
	//清除背景色  
	#define CLEARCOLOR [UIColor clearColor] 
	
	//4.加载图片宏：
	#define LOADIMAGE(file,type) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle]pathForResource:file ofType:type]]
	//5. NavBar高度
	#define NavigationBar_HEIGHT 44
	//6. 获取系统版本
	#define IOS_VERSION [[[UIDevice currentDevice] systemVersion] floatValue]
	#define CurrentSystemVersion [[UIDevice currentDevice] systemVersion]

	//7. 判断是真机还是模拟器
	#if TARGET_OS_IPHONE
	    //iPhone Device
	#endif
 
	#if TARGET_IPHONE_SIMULATOR
	   //iPhone Simulator
	#endif
	
	//8. 设置View的tag属性
	#define VIEWWITHTAG(_OBJECT, _TAG)    [_OBJECT viewWithTag : _TAG]
 
	//9. GCD
	#define BACK(block) dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), block)
	#define MAIN(block) dispatch_async(dispatch_get_main_queue(),block)
 
	//10. NSUserDefaults 实例化
	#define USER_DEFAULT [NSUserDefaults standardUserDefaults]

---
原文链接[http://lvesli.com/2016/05/24/Objective-C-The-Preprocessor/](http://lvesli.com/2016/05/24/Objective-C-The-Preprocessor/)
>本博客也会在 *** lecoding *** 微信公众号中同步更新，欢迎大家订阅，有什么问题可以在此一起交流。公众号搜索：*** 乐Coding *** 或者 *** lecoding *** 或者微信扫描下方二维码：

![http://www.lvesli.com/wp-content/uploads/2014/10/qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-8126ca6ffcdbda50.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

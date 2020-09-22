最近项目中要做本地缓存，所以就选择了简单的`CoreData`进行存储，存储的时候遇到一个问题就是CoreData中无法存储`数组`，只能存储像`Int、String、Date`等这种基本数据类型，看了看有一个自己不熟悉的类型`Transformable`，上网查了下他可以解决数组、字典、图片等存储问题。

今天我们分享一个使用CoreData的小技巧。CoreData是一个相当神器的API。今天我们来解决一个包含非基本数据类型的实体存储问题。

`Transformable` 数据类型拥有把属性（NSArray、UIIMage、JSON 数据等）转成`NSData`存储，获取时再转成相应对象的能力 。

为了介绍Transformable在CoreData实体存储中的使用，我们创建一个Event实体如下图：


![123123.png](http://upload-images.jianshu.io/upload_images/1159872-f0978ea24c6ee5f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在右侧数据模型审查器（Data Model Inspector）我们把`numbersArray`设置成了`Transformable`类型，并且把name设置成了`NumbersArray`. 这个名字可以自己随意取，一会我们会用这个名字作为自定义类的名字并实现`transformable`。

接下来选中Event实体，选择 `Editor > Create NSManagedObject Subclass….` 好的，你自定义的类就创建成功了。注意其中numbersArray的类型是`id`类型，你可能不知道怎么实现了。他会在运行时通过`NSValueTransformer`子类处理。我们在`Event.h`中定义一个`NSValueTransformer`的子类：


	@interface Event : NSManagedObject
	
	@property (nonatomic, retain) NSDate * timeStamp;
	@property (nonatomic, retain) id numbersArray;
	
	@end
	
	@interface NumbersArray : NSValueTransformer
	@end


其中`NumbersArray`是刚才你给`numbersArray`变换类型起的名字，并且他继承自`NSValueTransformer`。现在我们看下在`.m`文件中如何实现。

	#import "Event.h"
	
	@implementation Event
	
	@dynamic timeStamp;
	@dynamic numbersArray;
	
	@end
	
	@implementation NumbersArray
	
	+ (Class)transformedValueClass
	{
	return [NSArray class];
	}
	
	+ (BOOL)allowsReverseTransformation
	{
	return YES;
	}
	
	- (id)transformedValue:(id)value
	{
	return [NSKeyedArchiver archivedDataWithRootObject:value];
	}
	
	- (id)reverseTransformedValue:(id)value
	{
	return [NSKeyedUnarchiver unarchiveObjectWithData:value];
	}
	@end
	


我们实现一些方法来指明这个类将要转换成什么，并且允许在转换数值和反向转换数值之间变换。

主要函数是

	transformedValue:

把NSArray转换成NSData使用`NSKeyedArchiver`和

	reverseTransformedValue:
	
把NSData转换成NSArray使用 `NSKeyedUnarchiver`。

到此自定义存储类型就结束了，快去试试吧。

> 更多iOS、Android开发精彩文章请关注微信公众账号：`乐coding`,你也可以扫描下方二维码关注我们。

![qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-fc0ea2c48064eb49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


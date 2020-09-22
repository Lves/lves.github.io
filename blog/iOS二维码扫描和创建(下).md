在上一篇文章《二维码扫描和创建（上）》中我们已经介绍了三种扫描二维码的方式和如何扫描相册中的二维码图片，想查看的可以关注我们的微信公众号：`lecoding` ,里边有更多 `iOS`开发精彩文章。今天我们谈谈我了解到关于创建二维码的几种方式。

我在iPhone APP `扫码神奇`中使用的就是下面第二种方法，大家可以到APPStore搜索`扫码神奇`,下载体验一下（记得给个好评呦）。`扫码神奇`下载链接：
[https://itunes.apple.com/cn/app/id1074173840](https://itunes.apple.com/cn/app/id1074173840)

![icon108.png](http://upload-images.jianshu.io/upload_images/1159872-247f1c5e6b9e25bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### libqrencode
源文件我已经上传到百度网盘 下载链接：http://pan.baidu.com/s/1qXrFOq4 ，`github`链接：https://github.com/fukuchi/libqrencode  ,
* 下载解压后，把文件夹拖入项目中
* 文件中加入以下框架：`AVFoundation.framework、CoreMedia.framework、CoreVideo.framework、QuartzCore.framework、libiconv.dylib`
* 需要使用的页面.m文件中引用头文件`#import "QRCodeGenerator.h"`
生成二维码只需要一行代码：


	UIImage *image = [QRCodeGenerator qrImageForString:self.codeString imageSize:self.imageView_Code.bounds.size.width];  

使用`libqrencode` 生成二维码无法生成彩色二维码。下面可以看看原生的可以实现生成彩色二维码。

###原生

我在网上找到了一种不使用第三方库创建彩色二维码的方式，分享给大家：



	//
	
	//  LLQRGenerator.m
	
	//  QRenManager
	
	//
	
	//  Created by LiXingLe on 16/1/12.
	
	//  Copyright © 2016年 com.wildcat. All rights reserved.
	
	//
	
	
	
	#import "LLQRGenerator.h"
	
	
	
	
	
	@implementation LLQRGenerator
	
	
	
	+ (UIImage *) getQRImageWithContent:(NSString *)content red:(CGFloat)red green:(CGFloat)green blue:(CGFloat)blue{
	
	    
	
	    // 1.创建滤镜
	
	    CIFilter *filter = [CIFilter filterWithName:@"CIQRCodeGenerator"];
	
	    
	
	    // 2.还原滤镜默认属性
	
	    [filter setDefaults];
	
	    
	
	    // 3.设置需要生成二维码的数据到滤镜中
	
	    // OC中要求设置的是一个二进制数据
	
	    NSData *data = [content dataUsingEncoding:NSUTF8StringEncoding];
	
	    [filter setValue:data forKeyPath:@"InputMessage"];
	
	    
	
	    // 4.从滤镜从取出生成好的二维码图片
	
	    CIImage *ciImage = [filter outputImage];
	
	    
	
	   return  [self createNonInterpolatedUIImageFormCIImage:ciImage size:500 withRed:red green:green blue:blue];
	
	    
	
	}
	
	
	
	
	
	
	
	
	
	//生成高清二维码方法
	
	+ (UIImage *)createNonInterpolatedUIImageFormCIImage:(CIImage *)ciImage size:(CGFloat)widthAndHeight withRed:(CGFloat)red green:(CGFloat)green blue:(CGFloat)blue
	
	{
	
	    CGRect extentRect = CGRectIntegral(ciImage.extent);
	
	    CGFloat scale = MIN(widthAndHeight / CGRectGetWidth(extentRect), widthAndHeight / CGRectGetHeight(extentRect));
	
	    
	
	    // 1.创建bitmap;
	
	    size_t width = CGRectGetWidth(extentRect) * scale;
	
	    size_t height = CGRectGetHeight(extentRect) * scale;
	
	    CGColorSpaceRef cs = CGColorSpaceCreateDeviceGray();
	
	    CGContextRef bitmapRef = CGBitmapContextCreate(nil, width, height, 8, 0, cs, (CGBitmapInfo)kCGImageAlphaNone);
	
	    
	
	    CIContext *context = [CIContext contextWithOptions:nil];
	
	    
	
	    CGImageRef bitmapImage = [context createCGImage:ciImage fromRect:extentRect];
	
	    CGContextSetInterpolationQuality(bitmapRef, kCGInterpolationNone);
	
	    CGContextScaleCTM(bitmapRef, scale, scale);
	
	    CGContextDrawImage(bitmapRef, extentRect, bitmapImage);
	
	    
	
	    // 保存bitmap到图片
	
	    CGImageRef scaledImage = CGBitmapContextCreateImage(bitmapRef);
	
	    CGContextRelease(bitmapRef);
	
	    CGImageRelease(bitmapImage);
	
	    
	
	    //return [UIImage imageWithCGImage:scaledImage]; // 黑白图片
	
	    UIImage *newImage = [UIImage imageWithCGImage:scaledImage];
	
	    return [self imageBlackToTransparent:newImage withRed:red andGreen:green andBlue:blue];
	
	}
	
	
	
	void ProviderReleaseData (void *info, const void *data, size_t size){
	
	    free((void*)data);
	
	}
	
	//设置图片透明度
	
	+ (UIImage*)imageBlackToTransparent:(UIImage*)image withRed:(CGFloat)red andGreen:(CGFloat)green andBlue:(CGFloat)blue{
	
	    const int imageWidth = image.size.width;
	
	    const int imageHeight = image.size.height;
	
	    size_t      bytesPerRow = imageWidth * 4;
	
	    uint32_t* rgbImageBuf = (uint32_t*)malloc(bytesPerRow * imageHeight);
	
	    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
	
	    CGContextRef context = CGBitmapContextCreate(rgbImageBuf, imageWidth, imageHeight, 8, bytesPerRow, colorSpace,
	
	                                                 kCGBitmapByteOrder32Little | kCGImageAlphaNoneSkipLast);
	
	    CGContextDrawImage(context, CGRectMake(0, 0, imageWidth, imageHeight), image.CGImage);
	
	    // 遍历像素
	
	    int pixelNum = imageWidth * imageHeight;
	
	    uint32_t* pCurPtr = rgbImageBuf;
	
	    for (int i = 0; i < pixelNum; i++, pCurPtr++){
	
	        if ((*pCurPtr & 0xFFFFFF00) < 0x99999900)
	
	        {
	
	            uint8_t* ptr = (uint8_t*)pCurPtr;
	
	            ptr[3] = red; //0~255
	
	            ptr[2] = green;
	
	            ptr[1] = blue;
	
	        }
	
	        else
	
	        {
	
	            uint8_t* ptr = (uint8_t*)pCurPtr;
	
	            ptr[0] = 0;
	
	        }
	
	    }
	
	    // 输出图片
	
	    CGDataProviderRef dataProvider = CGDataProviderCreateWithData(NULL, rgbImageBuf, bytesPerRow * imageHeight, ProviderReleaseData);
	
	    CGImageRef imageRef = CGImageCreate(imageWidth, imageHeight, 8, 32, bytesPerRow, colorSpace,
	
	                                        kCGImageAlphaLast | kCGBitmapByteOrder32Little, dataProvider,
	
	                                        NULL, true, kCGRenderingIntentDefault);
	
	    CGDataProviderRelease(dataProvider);
	
	    UIImage* resultUIImage = [UIImage imageWithCGImage:imageRef];
	
	    // 清理空间
	
	    CGImageRelease(imageRef);
	
	    CGContextRelease(context);
	
	    CGColorSpaceRelease(colorSpace);
	
	    return resultUIImage;
	
	}
	
	
	
	@end
	
	
扫描二维码和创建二维码项目展示：APPStore搜索`扫码神奇`(记得给个好评呦)。


> 更多`iOS`、`Android`精彩文章请关注微信公众账号：`lecoding`,你也可以扫描下方二维码关注我们。

![qrcode_for_gh_af22362bf4bb_258.jpg](http://upload-images.jianshu.io/upload_images/1159872-fc0ea2c48064eb49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

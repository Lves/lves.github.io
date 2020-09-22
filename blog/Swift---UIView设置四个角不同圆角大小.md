###创建Path

```
typealias CornersRadius = (topLeft: CGFloat, topRight: CGFloat, bottomLeft: CGFloat, bottomRight: CGFloat)

//创建Path
func createPath(bounds:CGRect, cornersRadius:CornersRadius) -> CGPath {
        let minX = bounds.minX
        let minY = bounds.minY
        let maxX = bounds.maxX
        let maxY = bounds.maxY

        let topLeftCenterX = minX + cornersRadius.topLeft
        let topLeftCenterY = minY + cornersRadius.topLeft
        let topRightCenterX = maxX - cornersRadius.topRight
        let topRightCenterY = minY + cornersRadius.topRight
        let bottomLeftCenterX = minX + cornersRadius.bottomLeft
        let bottomLeftCenterY = maxY - cornersRadius.bottomLeft
        let bottomRightCenterX = maxX - cornersRadius.bottomRight
        let bottomRightCenterY = maxY - cornersRadius.bottomRight
        
        let path = CGMutablePath()
        path.addArc(center: CGPoint(x: topLeftCenterX, y: topLeftCenterY), radius: cornersRadius.topLeft, startAngle: CGFloat(Double.pi), endAngle: CGFloat(3 * Double.pi / 2.0), clockwise: false)
        path.addArc(center: CGPoint(x: topRightCenterX, y: topRightCenterY), radius: cornersRadius.topRight, startAngle: CGFloat(3 * Double.pi / 2.0), endAngle: 0, clockwise: false)
        path.addArc(center: CGPoint(x: bottomRightCenterX, y: bottomRightCenterY), radius: cornersRadius.bottomRight, startAngle: 0, endAngle: CGFloat(Double.pi / 2.0), clockwise: false)
        path.addArc(center: CGPoint(x: bottomLeftCenterX, y: bottomLeftCenterY), radius: cornersRadius.bottomLeft, startAngle: CGFloat(Double.pi / 2.0), endAngle: CGFloat(Double.pi), clockwise: false)
        path.closeSubpath()
        
        return path
}
```

###使用
```
let subView = UIView(frame: CGRect(x: 80, y: 230, width: 300, height: 80))
let shapLayer = CAShapeLayer()
let cornersRadius = CornersRadius(topLeft: 30, topRight: 10, bottomLeft: 0, bottomRight: 10)
shapLayer.path = createPath(bounds: subView.bounds, cornersRadius: cornersRadius)
subView.layer.mask = shapLayer
subView.backgroundColor = UIColor.gray
```

###效果展示：
![屏幕快照](https://upload-images.jianshu.io/upload_images/1159872-616bde8bbf1d2218.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)






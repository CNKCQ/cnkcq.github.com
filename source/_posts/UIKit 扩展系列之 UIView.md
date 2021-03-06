title: UIKit 扩展系列之 UIView
date: 2017-08-13 2:48:13
tags: [iOS,Swift]
---

## 前言
  对 iOS 熟悉的同学应该都知道，在开发的过程中，我们经常会创建一些扩展来封装一些常用的代码块以便我们快速开发，我也不例外，在开发的过程中我搜集了一些比较常用的代码片段放在github 仓库里，这里我要讲的是对 UIView 的一些扩展。
  <!-- more -->
## 正文
1. flatSubviews
  想必大家有过这样的经历吧，有时候，我们需要对系统控件进行更改，当然也可能是对第三方控件进行修改，这个时候我们往往需要去遍历一个视图的所有子视图，当然这个时候大家可能首先会想到使用系统的 subviews 属性，但是，这只能得到当前视图的所有子视图，那么子视图的子视图呢？这里我为 UIView 扩展了一个属性  flatSubviews ，这个属性包含了视图的子视图以及子视图的子视图，其实实现方法并不难，这里主要使用到了迭代模式。下面我们来看看其中的内部实现：

```swift
/// `self`'s all subviews include the subview's subviews.
    var flatSubviews: [UIView] {
        var views: [UIView] = []
        self.subviews.forEach { (sub) in
            views.append(sub)
            if !sub.subviews.isEmpty {
                views += sub.flatSubviews
            }
        }
        return views
    }
```
看到这里大家是不是觉得其实很简单呢？☺

2. functions
(1) 便捷的为 View 添加圆角

```swift
    ///  When positive, the background of the layer will be drawn with rounded corners. Also effects the mask generated by the`masksToBounds' property. Defaults to true. Animatable.
    func cornerRadius(_ radius: CGFloat) {
        layer.cornerRadius = radius
        layer.masksToBounds = true
    }
```
(2) 快速添加点击手势

```swift 
    /// Add tapGesture/action for particular event.
    ///
    /// - Parameters:
    ///   - tapNumber: Default is 1. The number of taps required to match
    ///   - target: `self`
    ///   - action: a `Selector`
    func addTapGesture(_ tapNumber: Int = 1, target: Any = self, action: Selector) {
        let tap = UITapGestureRecognizer(target: target, action: action)
        tap.numberOfTapsRequired = tapNumber
        isUserInteractionEnabled = true
        addGestureRecognizer(tap)
    }
```

(3) 截取视图

```swift
/// Returns an image of the current view
    ///
    /// - Parameter scale: scale
    /// - Returns: A new image
    func getSnapshot(scale: CGFloat = 0) -> UIImage {
        UIGraphicsBeginImageContextWithOptions(bounds.size, false, scale)
        drawHierarchy(in: bounds, afterScreenUpdates: true)
        let image = UIGraphicsGetImageFromCurrentImageContext()!
        UIGraphicsEndImageContext()
        return image
    }
```
(4) 添加边线
   为 View 添加边线也是一个高频的需求，我们会经常遇到为 UITextField 或者 自定义的 Cell 添加下边线。通常情况下，我们会想到使用贝塞尔曲线，我开始也是这样用的，后来发现在适配横竖屏的时候比较麻烦，如果你比较好的实现方式，希望你能告诉我。后来还是决定 使用 UIView 自定义 Line ，然后用 AutoLayout 添加约束，这样就完美的适配了横竖屏了。

```swift
    func addBorder(for edges: [UIRectEdge], width: CGFloat = 1, color: UIColor = .black, insets: UIEdgeInsets = UIEdgeInsets(all: 0)) {
        var edgesValues: [UIRectEdge] = edges
        if edges.contains(.all) {
            edgesValues = [.top, .bottom, .left, .right]
        }
        let allSpecificBorders:[UIRectEdge] = [.top, .bottom, .left, .right]
        for edge in allSpecificBorders {
            if let view = viewWithTag(Int(edge.rawValue)) {
                view.removeFromSuperview()
            }

            if edgesValues.contains(edge) {
                let view = UIView()
                view.tag = Int(edge.rawValue)
                view.backgroundColor = color
                view.translatesAutoresizingMaskIntoConstraints = false
                addSubview(view)
                var horizontalVisualFormat = "H:"
                var verticalVisualFormat = "V:"
                switch edge {
                case UIRectEdge.bottom:
                    horizontalVisualFormat += "|-(\(insets.left))-[v]-(\(insets.right))-|"
                    verticalVisualFormat += "[v(\(width))]-(\(insets.bottom))-|"
                case UIRectEdge.top:
                    horizontalVisualFormat += "|-(\(insets.left))-[v]-(\(insets.right))-|"
                    verticalVisualFormat += "|-(\(insets.top))-[v(\(width))]"
                case UIRectEdge.left:
                    horizontalVisualFormat += "|-(\(insets.left))-[v(\(width))]"
                    verticalVisualFormat += "|-(\(insets.top))-[v]-(\(insets.bottom))-|"
                case UIRectEdge.right:
                    horizontalVisualFormat += "[v(\(width))]-(\(insets.right))-|"
                    verticalVisualFormat += "|-(\(insets.left))-[v]-(\(insets.right))-|"
                default:
                    break
                }
                self.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: horizontalVisualFormat, options: .directionLeadingToTrailing, metrics: nil, views: ["v": view]))
                self.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: verticalVisualFormat, options: .directionLeadingToTrailing, metrics: nil, views: ["v": view]))
            }
        }
    }
```
使用效果：
![simulator screen shot 2017 8 12 9 20 15](http://upload-images.jianshu.io/upload_images/121208-9afc52ee4f74c1e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

UI 扩展，请看    [UIKit.plus](https://github.com/CNKCQ/UIKit.plus)
水平有限，如有疏漏，望指正
欢迎交流，欢迎 star  [CNKCQ](https://github.com/CNKCQ)
遗人🌹，手有余香      [blog](http://wangchenquan.com/)


title: 完美适配，您需要知道的 SnapKit
date: 2017-08-12 9:48:13
tags: [iOS,Swift]
---

  
## 前言
   众所周知，iPhone 的屏幕是越来越多样化了，多样化的屏幕满足了用户差异化的需求，同时也增加了开发者的工作 ----- 适配，面对多样化、不同屏幕尺寸的设备，之前在 iOS 中的 frame 布局以显得不合时宜，于是 Apple 推出了 AutoLayout ，AutoLayout 在可是化编程中的优势是不容置喙的，然而对于习惯于 code 界面的朋友来说就不那么友好了，面对这样的问题，众多第三方框架横空出世，目前为止支持率比较高的当属 SnapKit 了。
<!-- more -->
## 正文
    当然，如果你之前对 Masonry 非常熟悉，那么恭喜你，你可以平滑的过度到 SnapKit, 如果你对 SnapKit 比较陌生，那么也没关系，请继续往下看☺。

###### 安装
   如果你的项目是用 CocoaPods 来管理组件的，那么你只需要在 Podfile 里加入 ：


```ruby
 pod 'SnapKit', '~> 3.0'
```
然后在 terminal 里执行 


```ruby
pod install
```
等待安装成功就可以 import SnapKit 使用了。
###### 用法
    SnapKit  框架的设计，简单易用，可读性好。当你想要构建一个 tableView , tableView 在父视图中的 padding 为 20, 那么用 SnapKit 怎么做呢？


```swift 
tableView.snp.makeConstraints { (make) in
            make
                .edges
                .equalTo(self.view)
                .inset(UIEdgeInsets(
                    top: 20,
                    left: 20,
                    bottom: 20,
                    right: 20
                )
            )
        }
``` 
or 


```swift 
tableView.snp.makeConstraints { (make) in
            make.left.top.equalTo(self.view).offset(20)
            make.right.bottom.equalTo(self.view).offset(-20)
        }

```
这是最常用的写法了，当然我们的实际项目中的布局是非常复杂的，并不是所有的视图都能用 equalTo 来进行布局的，有了 SnapKit ，你可以用：

.equalTo  等价于 NSLayoutRelation.Equal
.lessThanOrEqualTo 等价于 NSLayoutRelation.GreaterThanOrEqual
.greaterThanOrEqualTo 等价于 NSLayoutRelation.GreaterThanOrEqual

我们用一张表来罗列出 SnapKit 的属性与 NSLayout 中的属性对应关系：

|    ViewAttribute    | NSLayoutAttribute |
| ---------- | --- |
| view.snp.left  |  NSLayoutAttribute.left |
| view.snp.right   |  NSLayoutAttribute.right |
| view.snp.top     |  NSLayoutAttribute.top |
| view.snp.bottom    |  NSLayoutAttribute.bottom |
| view.snp.leading     |  NSLayoutAttribute.leading |
| view.snp.trailing  |  NSLayoutAttribute.trailing |
| view.snp.width   |  NSLayoutAttribute.width |
| view.snp.height  |  NSLayoutAttribute.height |
| view.snp.centerX   |  NSLayoutAttribute.centerX |
| view.snp.centerY   |  NSLayoutAttribute.centerY |
| view.snp.lastBaseline |  NSLayoutAttribute.lastBaseline |

通过这张表，我们可以根据自己的需要对相应的动画进行布局，但这还不能完全满足我们的需求，我们的应用是非常复杂的，如果产品要求我们的 APP 需要根据用户的交互反馈来更新视图的 frame 或者 需要做动画呢？别担心，SnapKit 为我们提供了 Constraint 属性关联。也就是说我们可以将要更改的约束进行关联，然后改变它，如：
声明关联属性

```swift 
var topConstraint: Constraint? = nil
```
初始化


```swift
button.snp.makeConstraints { (make) in
            self.topConstraint = make.top.equalTo(self.view).offset(100).constraint
            make.centerX.equalTo(self.view.snp.centerX)
            make.width.equalTo(60)
            make.height.equalTo(30)
        }
```
更改


```swift 
UIView.animate(withDuration: 5) {
            self.topConstraint?.update(offset: 200)
            self.view.layoutIfNeeded()
        }
```
这样一个简单的动画就完成了。当然，如果你想更新约束，你还可以用 .snp.updateConstraints


```swift
 self.button.snp.updateConstraints({ (make) in
                make.top.equalTo(200)
            })
            self.view.layoutIfNeeded()
```
还有一个问题是，如果我们引用了一个三方视图库，我们想改变他的约束，担忧不知道原来的约束怎么办呢？其实我们可以用  snp.remakeConstraints

```swift
        button.snp.remakeConstraints { (make) in
            make.top.equalTo(self.view).offset(100)
            make.centerX.equalTo(self.view.snp.centerX)
            make.width.equalTo(60)
            make.height.equalTo(30)
        }
```
这样首先会移除原有的控件的约束，然后重新设置约束。
看到这里相信大家对 SnapKit 的使用并不陌生了吧！

这里有一个常见的问题， 如果我们需要通过数组动态创建一组控件，要求长宽高都相等，并列排放在父视图中：

```swift
for (idx, item) in items.enumerated() {
            if items.count < 2 { return }
            if idx == 0 {
                item.snp.makeConstraints({ (make) in
                    make.left.top.bottom.equalTo(self)
                    make.width.equalTo(items[idx + 1])
                    make.right.equalTo(items[idx + 1].snp.left)
                })
            } else if idx == items.count - 1 {
                item.snp.makeConstraints({ (make) in
                    make.right.top.bottom.equalTo(self)
                    make.width.equalTo(items[idx - 1])
                })
            } else {
                item.snp.makeConstraints({ (make) in
                    make.top.bottom.equalTo(self)
                    make.left.equalTo(items[idx - 1].snp.right)
                    make.width.equalTo(items[idx - 1].snp.width)
                })
            }
        }
```
这样，一组动态的创建好了！
请看    [AutoLayoutDemo](https://github.com/iOS-examples-Swift/AutoLayoutDemo)
水平有限，如有疏漏，望指正
欢迎交流，欢迎 star  [CNKCQ](https://github.com/CNKCQ)
遗人🌹，手有余香      [blog](http://wangchenquan.com/)

## 参考
[snapKit](http://snapkit.io/docs/)

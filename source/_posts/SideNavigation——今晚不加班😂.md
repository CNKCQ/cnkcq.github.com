title: SideNavigation——今晚不加班😂
date: 2017-09-22 1:48:13
tags: [iOS,Swift,Menu]
---


## 前言

&emsp;&emsp;在 Android app 中，侧边栏的应用是非常普遍的，从 Google 的官方 UI 设计指南中可以看出这种设计对 UI || UED 的引导，不仅如此，Google 自家的应用也对侧边栏进行了广泛的实践，inbox gmail 就是其中之一。因此，你也可能遇到有一天你的 UI || UED  跑过来对你说我们 iOS 也支持一下 “侧边栏” 导航吧。如果你有这个需求又恰好看到这篇文章，那么恭喜你，本来可能需要加班的你可以解放了。
<!-- more -->


![image.png](http://upload-images.jianshu.io/upload_images/121208-710ab9f57c187cb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)

## 正文
&emsp;&emsp;如上图，像这样的效果实现方式很多，我们项目中原来就有实现过，既然实现过，那么为什么还要去实现一遍呢？这就要从我苦逼的 iPad 适配之旅开始讲了，我们的项目是一个 OC + Swift + RN + H5 的一个混合项目，适配起来那个酸爽别提了，侧边是由 OC 写的老旧代码，在 iPad 的横竖屏适配上特别糟糕😰，于是我决定用 Swift 封装一个 SideNavigation 的轮子。为了满足需求这个轮子应该具备以下特点：

1. 横竖屏适配，满足 iPad 适配需求
2. 支持 Swift && OC 满足混编需求
3. 用户可以通过向右或者向左滑动呼出侧边栏
4. 用户还可以通过拖拽关闭侧边栏

###### 接口要简单
&emsp;&emsp;把复杂留给自己，简单留给他人，好的接口大底应该如此。如：
```swift
public convenience init(_ viewController: UIViewController, left: UIViewController) 
public convenience init(_ viewController: UIViewController, right: UIViewController)
```
由上可知，我们只需要通过初始化一个 SideMenuManager 就能实现你想要的侧边抽屉效果，实在是太方便了😂。

###### 实现
&emsp;&emsp;
(1)  实现 UIViewControllerTransitioningDelegate 
&emsp;&emsp; 我们可以通过 UIViewControllerTransitioningDelegate 来实现转场动画：

```swift 
    public func presentationController(forPresented presented: UIViewController, presenting: UIViewController?, source: UIViewController) -> UIPresentationController? {
        let presentationController = PresentationController(presentedViewController: presented, presenting: presenting)
        presentationController.delegate = self
        presentationController.direction = direction
        self.dismissInteractor = PercentDrivenInteractiveTransition(self.presentController, with: presentationController.dimmingView, present: nil, direction: self.direction)
        return presentationController
    }

    public func animationController(forDismissed dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        let animator = AnimatedTransitioning()
        animator.direction = direction
        animator.transitionType = .dismiss
        return animator
    }

    public func animationController(forPresented presented: UIViewController, presenting: UIViewController, source: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        let animator = AnimatedTransitioning()
        animator.direction = direction
        animator.transitionType = .present
        return animator
    }

    public func interactionControllerForDismissal(using animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        return self.dismissInteractor.isInteractiveTransition ? self.dismissInteractor : nil
    }

    public func interactionControllerForPresentation(using animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        return self.presentInteractor.isInteractiveTransition ? self.presentInteractor : nil
    }
```
通过使用 SideNavigation 我们可以知道，我们的侧边效果是通过转场来实现的，要达到侧边栏的效果，我们可以自定义转场动画，👆代码中的代理就是转场协议的实现，包括对 non-interactive 和 interactive 动画进行自定义实现。

（2）自定义 UIPresentationController
&emsp;&emsp; 在 `public func presentationController(forPresented presented: UIViewController, presenting: UIViewController?, source: UIViewController) -> UIPresentationController? ` 函数中我们需要返回一个指定的 `UIPresentationController`, 当然，我们这里需要对我们的 `UIPresentationController` 适当的做一些改造来满足我们的需求:

```swift
   // MARK: - Initializers
    override init(presentedViewController: UIViewController, presenting presentingViewController: UIViewController?) {
        super.init(presentedViewController: presentedViewController, presenting: presentingViewController)
    }

    override var frameOfPresentedViewInContainerView: CGRect {
        var frame: CGRect = super.frameOfPresentedViewInContainerView
        switch direction {
        case .right:
            frame.origin.x = frame.size.width / 3
        default:
            break
        }
        frame.size = size(forChildContentContainer: presentedViewController, withParentContainerSize: containerView!.bounds.size)
        return frame
    }

    override func containerViewWillLayoutSubviews() {
        presentedView?.frame = frameOfPresentedViewInContainerView
    }

    override func size(forChildContentContainer container: UIContentContainer, withParentContainerSize parentSize: CGSize) -> CGSize {
        return CGSize(width: parentSize.width*(2.0/3.0), height: parentSize.height)
    }

    override func presentationTransitionWillBegin() {
        containerView?.insertSubview(dimmingView, at: 0)
        NSLayoutConstraint.activate(NSLayoutConstraint.constraints(withVisualFormat: "V:|[dimmingView]|", options: [], metrics: nil, views: ["dimmingView": dimmingView]))
        NSLayoutConstraint.activate(NSLayoutConstraint.constraints(withVisualFormat: "H:|[dimmingView]|", options: [], metrics: nil, views: ["dimmingView": dimmingView]))
        guard let coordinator = presentedViewController.transitionCoordinator else {
            dimmingView.alpha = 1.0
            return
        }

        coordinator.animate(alongsideTransition: { _ in
            self.dimmingView.alpha = 1.0
        })
    }

    override func dismissalTransitionWillBegin() {
        guard let coordinator = presentedViewController.transitionCoordinator else {
            dimmingView.alpha = 0.0
            return
        }

        coordinator.animate(alongsideTransition: { _ in
            self.dimmingView.alpha = 0.0
        })
    }

    lazy var dimmingView: UIView = {
        let dimming = UIView()
        dimming.translatesAutoresizingMaskIntoConstraints = false
        dimming.backgroundColor = UIColor(white: 0.0, alpha: 0.5)
        dimming.alpha = 0.0
        let recognizer = UITapGestureRecognizer(target: self, action: #selector(handleTap(recognizer:)))
        dimming.addGestureRecognizer(recognizer)
        return dimming
    }()

    @objc dynamic func handleTap(recognizer: UITapGestureRecognizer) {
        presentingViewController.dismiss(animated: true)
    }
```
在自定义的  `UIPresentationController` 的过程中，我们对 `presentedView` 的 `frame` 进行简单的更改，已达到我们的期望。从效果图中我们也看到，我们还需要一个 `dimmingView ` 来实现半透明效果，这里我们还未它添加了一个手势以便于 `dismiss` 抽屉。 

（3）实现 UIViewControllerAnimatedTransitioning 

&emsp;&emsp; `public func animationController(forDismissed dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning?` 需要返回一个 `UIViewControllerAnimatedTransitioning` 这里我们来初略的看一下：

```swift 
    func transitionDuration(using transitionContext: UIViewControllerContextTransitioning?) -> TimeInterval {
        return kAnimationDuration
    }

    func animateTransition(using transitionContext: UIViewControllerContextTransitioning) {
        let from = transitionContext.viewController(forKey: UITransitionContextViewControllerKey.from)
        let to = transitionContext.viewController(forKey: UITransitionContextViewControllerKey.to)
        switch transitionType {
        case .present:
            animatePresenting(in: transitionContext, to: to!, from: from!)
        case .dismiss:
            animateDismissing(in: transitionContext, to: to!, from: from!)
        }
    }

    func animatePresenting(in transitionContext: UIViewControllerContextTransitioning, to: UIViewController, from: UIViewController) {
        var fromRect = transitionContext.initialFrame(for: from)
        var toRect = fromRect
        switch direction {
        case .left:
            toRect.origin.x = -toRect.width / 3 * 2 // for the edge panGesture
            if #available(iOS 11, *) {
                // it's maybe a bug of iOS 11, it should be checked some time
                fromRect = CGRect(x: fromRect.minX - (toRect.width / 3)/2, y: fromRect.minY, width: fromRect.width, height: fromRect.height)
            }
        case .right:
            toRect.origin.x = toRect.width / 3 * 2
            if #available(iOS 11, *) {
                // it's maybe a bug of iOS 11, it should be checked some time
                fromRect = CGRect(x: fromRect.minX + (toRect.width / 3)/2, y: fromRect.minY, width: fromRect.width, height: fromRect.height)
            }
        }
        to.view.frame = toRect
        transitionContext.containerView.addSubview(to.view)
        UIView.animate(withDuration: kAnimationDuration, animations: {
            to.view.frame = fromRect
        }) { (_) in
            if transitionContext.transitionWasCancelled {
                transitionContext.completeTransition(false)
            } else {
                transitionContext.completeTransition(true)
            }
        }
    }

    func animateDismissing(in transitionContext: UIViewControllerContextTransitioning, to: UIViewController, from: UIViewController) {
        var fromRect = transitionContext.initialFrame(for: from)
        switch direction {
        case .left:
            fromRect.origin.x = -fromRect.width
        case .right:
            fromRect.origin.x = fromRect.width
        }
        UIView.animate(withDuration: kAnimationDuration, animations: {
            from.view.frame = fromRect
        }) { (_) in
            if transitionContext.transitionWasCancelled {
                transitionContext.completeTransition(false)
            } else {
                transitionContext.completeTransition(true)
            }
        }
    }

```
从  `animatePresenting`  以及 `animateDismissing` 我们可以清晰的看到，这里就是对转场这个过程的 `start` 到 `end` 过程的位置状态做一个约束，然后在辅以简单的动画。

（4）UIPercentDrivenInteractiveTransition 的继承
&emsp;&emsp; 还记得我们在第一步实现的 `func interactionControllerForDismissal(using animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? `  &&  `func interactionControllerForPresentation(using animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? `  这两个函数吗？我们的用户交互效果就是通过这两个函数来实现的，在这里我们对 `UIPercentDrivenInteractiveTransition` 进行自定义：

```swift
    convenience init(_ viewController: UIViewController, with view: UIView?, present: UIViewController?, direction: Direction? = .left) {
        self.init()
        self.viewController = viewController
        self.direction = direction ?? .left
        self.presentViewController = present
        if self.presentViewController != nil {
            switch self.direction {
            case .left:
                let edgePanGesture = UIScreenEdgePanGestureRecognizer(target: self, action: #selector(onPan(sender:)))
                edgePanGesture.edges = .left
                edgePanGesture.delegate = self
                self.viewController.view.addGestureRecognizer(edgePanGesture)
            case .right:
                let edgePanGesture = UIScreenEdgePanGestureRecognizer(target: self, action: #selector(onPan(sender:)))
                edgePanGesture.edges = .right
                edgePanGesture.delegate = self
                self.viewController.view.addGestureRecognizer(edgePanGesture)
                break
            }
        } else {
            let panGesture = UIPanGestureRecognizer(target: self, action: #selector(onPan(sender:)))
            view?.addGestureRecognizer(panGesture)
            let dismissPanGesture = UIPanGestureRecognizer(target: self, action: #selector(onPan(sender:)))
            self.viewController.view.addGestureRecognizer(dismissPanGesture)
        }
    }

    func gestureRecognizerShouldBegin(_ gestureRecognizer: UIGestureRecognizer) -> Bool {

        /// to avoid the interactivePopGestureRecognizer of UINavigationController
        if let nav = viewController as? UINavigationController {
            return nav.viewControllers.count < 2
        }
        return true
    }

    @objc func onPan(sender: UIPanGestureRecognizer) {
        let  translation = sender.translation(in: sender.view?.superview)
        switch sender.state {
        case .began:
            self.isInteractiveTransition = true
            if self.presentViewController != nil {
                self.viewController.present(self.presentViewController!, animated: true, completion: nil)
            } else {
                self.viewController.dismiss(animated: true, completion: nil)
            }
        case .changed:
            let screenWidth = -UIScreen.main.bounds.size.width
            var dragAmount = self.presentViewController == nil ? screenWidth : -screenWidth
            switch direction {
            case .left:
                dragAmount = self.presentViewController == nil ? screenWidth : -screenWidth
            case .right:
                dragAmount = self.presentViewController != nil ? screenWidth : -screenWidth
            }
            let threshold: CGFloat = 0.20
            var percent = translation.x / dragAmount
            percent = max(percent, 0.0)
            percent = min(percent, 1.0)
            update(percent)
            self.shouldComplete = percent > threshold
        case .cancelled, .ended:
            self.isInteractiveTransition = false
            if self.shouldComplete == false || sender.state == .cancelled {
                cancel()
            } else {
                finish()
            }
        default:
            break
        }
    }
``` 
对 `UIPercentDrivenInteractiveTransition` 自定义的主要目的是对 转场动效的更新过程进行控制：
```swift 
    // These methods should be called by the gesture recognizer or some other logic
    // to drive the interaction. This style of interaction controller should only be
    // used with an animator that implements a CA style transition in the animator's
    // animateTransition: method. If this type of interaction controller is
    // specified, the animateTransition: method must ensure to call the
    // UIViewControllerTransitionParameters completeTransition: method. The other
    // interactive methods on UIViewControllerContextTransitioning should NOT be
    // called. If there is an interruptible animator, these methods will either scrub or continue 
    // the transition in the forward or reverse directions.
    open func update(_ percentComplete: CGFloat)
```
正是如此，我们在  `UIPercentDrivenInteractiveTransition ` 的自定义过程中，通过对 中间视图和侧边视图添加手势来控制转场 `update` 的进度。
&emsp;&emsp;看看项目中的使用效果：
![Simulator Screen Shot - iPhone 8 - 2017-09-22 at 09.45.13.png](http://upload-images.jianshu.io/upload_images/121208-1eaeac71cacb5879.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)![Simulator Screen Shot - iPhone X - 2017-09-22 at 10.15.15.png](http://upload-images.jianshu.io/upload_images/121208-730824aa958d22ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)

![](https://thumbs.gfycat.com/MagnificentWhichFlyinglemur-size_restricted.gif)
## 总结
&emsp;&emsp;实现一个简易的侧边抽屉效果还是很简单的，我们通过实现专场协议，自定义转场动画，添加滑动手势，设置转场始末位置状态，就可以轻松搞定侧边栏。

[Project](https://github.com/CNKCQ/SideNavigation.git)


水平有限，如有疏漏，望指正
欢迎交流，欢迎 star  [CNKCQ](https://github.com/CNKCQ)
遗人🌹，手有余香      [blog](http://wangchenquan.com/)


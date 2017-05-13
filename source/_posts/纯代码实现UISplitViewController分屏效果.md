title: 纯代码实现UISplitViewController分屏效果
date: 2016-05-20 9:48:13
tags: [iOS,Swift,UISplitViewController,分屏]
---

##

### 一、应用场景
  UISplitViewController 是 iphone 6s plus,ipad系列常用的控件 在iOS8以上的iPhone中得到支持（ In iOS 8 and later）一个典型的场景：iPhone 6s plus 内置的设置APP; 根据苹果设计指南，UISplitViewController一般作为更控制器来使用即rootViewController（. Split view controllers are normally installed at the root of your app’s window），不能作为navigationController的子控制器。
  <!-- more -->
### 二、简单用例

  1、注册为根控制器，为splitViewController指定master-detail控制器
  ``` bash
  let masterController: MasterController = MasterController()
       masterNav.addChildViewController(masterController)
       let detailController = DetailController()
       detailNav.addChildViewController(detailController)
       splitViewController.viewControllers = [masterNav,detailNav];
       preferredDisplayMode = .Automatic
       window?.rootViewController = SplitController()

  ```
  其中，viewControllers[0]为master viewControllers[1]为detail 本例中master为TableViewController
  2、cell事件的响应，判断横竖屏，实现spliViewController的转场方法

  ``` bash
  let dest = DetailController()
  if UIScreen.mainScreen().bounds.width > UIScreen.mainScreen().bounds.height {
     self.showDetailViewController(dest, sender: names[indexPath.row])
  } else {
     self.showViewController(dest, sender: names[indexPath.row])
  }
  ```
  需要提醒一下的是，为了在dest中接收到的sender数据，dest必须实现split view controller的代理方法
  ``` bash
  func splitViewController(splitViewController: UISplitViewController, showDetailViewController vc: UIViewController, sender: AnyObject?) -> Bool {
    title = "\(sender!)"
    return true
  }

  ```
  这样就简单实现了分屏效果

  # 实例代码: [SplitViewController](https://github.com/CNKCQ/SplitViewController)

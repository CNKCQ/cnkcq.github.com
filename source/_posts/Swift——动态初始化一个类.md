title: Swift：如何动态初始化一个类
date: 2015-04-10 9:50:10
tags: [iOS,Swift,动态加载]
---


### 一、产品需求
  现在市面上的APP有很多都有这样一个需求，那就是tabBarItem的动态配置，也就是如果节假日呀或者什么特殊日子呀tabBarItem的icon和title就有后台配置一下就可以改变了。
  <!-- more -->
### 二、命名空间 
  
  有一定经验的人都知道，要实现上面的需求，iOS端必须要动态创建类，在oc中这个好办：
  ``` bash
  Class cls = NSClassFromString(@"NewsViewController");
  [self.navigationController pushViewController:cls.new animated:YES]; 
  ```
  但是在Swift中就没有这么简单了，总所周知oc是没有命名空间的，而Swift有命名空间，在 swift 中，类名是包含命名空间的，命名空间默认是项目名称，同一个命名空间全局共享，如果新建项目时，项目名称中包含有中文，可以按照下图修改：
  ![ns](http://7xslr9.com1.z0.glb.clouddn.com/2015-04-10%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4.jpg)
  命名空间我们可以从info.plist配置文件里面或取:
  ``` bash
  let namespace = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
  ```
  在Swift中我们就可以这样动态的初始化一个类了：
  ``` bash
  let clsName = namespace + "." + "NewsViewController"
  let cls: AnyClass = NSClassFromString(clsName)!
  let vc = cls.alloc() as! UITableViewController
  ```







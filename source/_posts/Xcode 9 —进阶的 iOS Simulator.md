title: Xcode 9 —进阶的 iOS Simulator
date: 2017-08-15 1:05:13
tags: [iOS,Swift]
---


## 前言
   iOS Simulator 是 iOS 开发过程中不可或缺的一部分，我平时工作中也有很大一部分时间在使用 iOS Simulator， 相比于 Android ，iOS Simulator 具有启动时间快，运行效率高等显著的特点。 因此，用好 iOS Simulator 对于提高我们的开发效率是有很大帮助的. Xcode 9 中，有一大堆新特新等你来撩！！

<!-- more -->

## 正文
1、 在 Xcode 的全屏模式下使用 Simulator

    如果你用的是 13 寸的电脑，那么 Xcode 的全屏模式你应该早已习惯，其实即使是配有显示器，我还是习惯在 Xcode 的全屏模式下开发😎。很可惜的是不能在 Xcode 的全屏模式下使用 Simulator, 自 Xcode 9b3 以后，这个问题便不存在了：

![](https://thumbs.gfycat.com/SlimyGrouchyGoldenmantledgroundsquirrel-size_restricted.gif)

这个功能并不是对所有人默认开放的，如果不幸你的 Xcode 没有默认开启这个功能也不要灰心，我们可以通过打开 Apple Internal menu 来启用这个功能，通过 Apple Internal menu  你能挖掘出 Simulator 的更多新特性。那么问题来了，怎么开启  Apple Internal menu 呢？首先，在更目录里创建 AppleInternal 文件夹，然后重启 Simulator:


![internal-menu.png](http://upload-images.jianshu.io/upload_images/121208-cc84bf762415541a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```shell
cd ~/
sudo mkdir /AppleInternal
```

重启 Simulator 后就可以看到 internal 选项 ☝️
再次重申，你需要下载 Xcode 9b3 或更新的版本才行

2、同时打开多个模拟器

还记以前我们在不同尺寸的手机上去适配吗？通常情况下，我们通过 Storyboard 可以直观的看到 View 在不同尺寸设备上的形态，然而纯 Code 码出的界面就得多次启动不同的模拟器来进行可视化调试了。作为开发者，你肯定也想过，要是能同时启动多个模拟器多好，那么很高兴的告诉你，在 Xcode 9 中这已不再是问题了。

![](https://thumbs.gfycat.com/FortunateCrazyKiwi-size_restricted.gif)

3、拖动放大或缩小 Simulator

在 Xcode 9 之前， 我们需要通过 command + 1、2、3、4 来对 Simulator 的大小进行调整，Xcode 9 以后就不用这么麻烦，我们可以通过拖拽来控制模拟器的大小了。如果您有多个模拟器打开，这是非常有用的一些细节，它可以帮助你有效地组织工作空间。

![](https://thumbs.gfycat.com/InconsequentialGreatInsect-size_restricted.gif)

4、Simulator 屏幕捕捉

在 Apple 官方的 “What's new”  Xcode 9 文档中，苹果声称现在可以录制模拟器屏幕的视频。 其实这并不新鲜，即使在旧版本中我们也可以通过 simctl 实现这样的功能。 您可以从界面启用视频录制（除了iOS 11中的内置屏幕录制）。

![](https://thumbs.gfycat.com/GrandPiercingCreature-size_restricted.gif)

执行 command 的命令，就可以得到我们想要录制的视频了。

```shell
xcrun simctl io booted recordVideo --type=mp4 <PATH TO VIDEO FILE>
```
booted —— 即是 simctl 当前选中的模拟器，如果当前有多个模拟器，那么选中的活动的 Simulator 为准。

5、Finder 和 Simulator 共享文件

现在，Simulator 具有 Finder 扩展，我们可以直接从 Finder 窗口共享文件给 Simulator 。

![](https://thumbs.gfycat.com/FondIndelibleCaudata-size_restricted.gif)

当然，如果你使用的是旧版本的 Xcode ，我们也可以使用 simctl 命令来进行类似操作：

```shell
xcrun simctl addmedia booted <PATH TO MEDIA FILE>
```
这个功能太棒了，毕竟，我更喜欢通过拖拽来实现 Simulator 和 Finder 的文件共享。

6、 通过 Simulator 打开 URLs
通过 simctl 也可以实现 Simulator 打开 URLs， 所以你也可以在旧的模拟器上打开自定义的 URL。

![](https://thumbs.gfycat.com/LimpApprehensiveIguana-size_restricted.gif)

执行下面的 shell 命令

```shell	
xcrun simctl openurl booted <URL>
```
Apple 官方文档 [check out the documentation](https://developer.apple.com/library/content/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html)

7、快速打开 app 的文件系统

通过 simctl 你只需要简单的执行下面一句命令，你就能获取 app 的文件系统，但是，你得知道 bundle identifier：

```shell	
xcrun simctl get_app_container booted <APPS BUNDLE ID>
```
或者您可以通过 open 命令在 Finder 中快速打开目标文件夹：

```shell
open `xcrun simctl get_app_container booted <APPS BUNDLE ID>` -a Finder
```
8、通过 Command 打开 Simulator 中的 APP
有了 simctl ，你可以通过 Command 启动 Simulator 中的 app, 或者做一些环境变量的控制，这在自动化测试中是非常重要的。

![](https://thumbs.gfycat.com/QuickBoldGalago-size_restricted.gif)

你只要使用一下命令即可实现：

```shell
xcrun simctl launch --console booted <BUNDLE ID> <YOUR ARGUMENTS>
```
9、 通过 Bundle ID 获取 app 的 info 信息

有时候，我们想获得 app 的 更多的信息，可以通过 simctl appinfo 获得以下格式的信息：

```plist
{
   ApplicationType = User;
   Bundle = <PATH TO APP FILE>;
   BundleContainer = <PATH TO FOLDER WITH APP FILE>;
   CFBundleDisplayName = TestiOSApp;
   CFBundleExecutable = TestiOSApp;
   CFBundleIdentifier = "com.ahmed.app.TestiOSApp";
   CFBundleName = TestiOSApp;
   CFBundleVersion = 1;
   DataContainer = <PATH TO YOUR DATA>;
   GroupContainers =     {
   };
   Path = <PATH TO APP FILE>;
   SBAppTags =     (
   );
}
```
执行以下命令你就能获取以上格式的信息：

```shell
xcrun simctl appinfo booted <BUNDLE ID>
```
最后......

iOS Simulator 功能强大，是我们开发中必备的神器，当你掌握了它的相关细节以及使用技巧可以极大的提高你的开发效率。当然，作为一个有追求的 iOS 工程师，你更应该去挖掘它那些鲜为人知的功能，然后记得要告诉我哟😊！

## 参考

[iOS Simulator on Steroids: Tips & Tricks in Xcode 9](https://www.appcoda.com/ios-simulator-tips-tricks/)



水平有限，如有疏漏，望指正
欢迎交流，欢迎 star  [github](https://github.com/CNKCQ)
遗人🌹，手有余香

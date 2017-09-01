title: iOS 11 ——迟来的 NFC
date: 2017-09-1 2:48:13
tags: [iOS,Swift,NFC]
---
  
## 前言
  NFC 这个词已经并不陌生了，前一段时间北京地铁支持 NFC 支付一时成为霸占头条的热点。其实在 90 年代末到 2000 年初，二维码和 NFC 就已经相继诞生，由于二维码成本低廉，技术门槛相对较低，因此，二维码迅速抢占了移动支付的市场，但 NFC 的发展并未因此停止。在 Android 端的 NFC 发展已经非常迅猛了，可惜 Apple 迟迟为开放接口，在今年的 WWDC 上，苹果宣布开放其 NFC 接口 [ CoreNFC](https://developer.apple.com/documentation/corenfc), 这为以后 NFC 的应用提供了更多的可能。
<!-- more -->
## 正文
  如果你对 NFC 这项技术还比较陌生，那么这里科普一下，NFC（Near Field Communication）近场通信，当两个设备相互靠近时能进行信息交流。许多企业讲 NFC 芯片放到卡片里，用带有 NFC   芯片的卡片来授予权限将允许谁有权限，比如说进出入公司。Apple CoreNFC 目前支持的格式有限，NFC 数据交换格式或 NDEF（通常用于当今市场上的大多数平板电脑和手机），比如 Apple Pay 。
## CoreNFC Demo
  这里我们通过一个简单的实例程序来演示怎么使用 CoreNFC，这个程序可以用来读取存储在卡片上 NDEF 格式的信息。

![working-scanner.png](http://upload-images.jianshu.io/upload_images/121208-cec7ca6804a7f911.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)

为此，我使用 Arduino Uno 与 Adafruit PN532 Shield 配对，将其发送到样品 NDEF 格式的卡上。 如果你不具备这些东西，或者根本不想在这样的硬件上投入时间和金钱，请尝试找一张带有信息的预格式化卡。 本文中，我不会演示 NFC 格式化以及如何把数据存储到 NDEF 卡 中。

## Getting Started

  打开 Xcode 9 创建一个简单的 Swift 工程。使用 Storyboard 创建简单的页面：

![](http://upload-images.jianshu.io/upload_images/121208-8c47685e193eed81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

ViewController 如下：

```swift
import UIKit
 
class ViewController: UIViewController {
 
    @IBOutlet weak var messageLabel: UILabel!
 
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }
 
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
 
    @IBAction func scanPressed(_ sender: Any) {
        // this is our newly created IBAction
    }
 
}
```

## Entitlements & Privacy
  我们的 app 要使用 NFC 必须进行应用授权：前提是你得有一个有效 Apple id （交过保护费的). 进行应用程序授权和隐私设置，打开[developer.apple.com](https://developer.apple.com/)。 登录你的帐户，创建一个证书 —— 注册一个新的 APP ID。应用说明应该要支持 NFC 点击下一步，确保你的确认页面如下图：

![](http://upload-images.jianshu.io/upload_images/121208-8fa7a430ec5fd4cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

然后再创建  provisioning profile ：

![](http://upload-images.jianshu.io/upload_images/121208-638cc3dabbb815c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

这一步完成了，在我们刚创建的项目中导入 证书和描述文件，完成之后呢，我们还需进行 Info.plist 配置 Privacy：

![](http://upload-images.jianshu.io/upload_images/121208-db5efc3625e73429.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

至此，我们的开始工作就完成了。👇我们进入 coding 阶段。

## Core NFC

  要实现 NFC 功能，我们得接入 Core NFC framework：

```swift
import CoreNFC 

```
目前为止，iOS模拟器尚未支持 CoreNFC。 这意味着如你尝试导入CoreNFC，会收到一条错误，表示没有名为 CoreNFC 的模块。 遇到这种情况，请选择你的 iPhone 或  Generic iOS Device。接下来我们实现 NFCNDEFReaderSessionDelegate 协议：

```swift
import UIKit
import CoreNFC 

class ViewController: UIViewController, NFCNDEFReaderSessionDelegate { 

    @IBOutlet weak var messageLabel: UILabel!
    var nfcSession: NFCNDEFReaderSession?

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }

    @IBAction func scanPressed(_ sender: Any) {

    }

    func readerSession(_ session: NFCNDEFReaderSession, didInvalidateWithError error: Error) {
        print("The session was invalidated: \(error.localizedDescription)")
    }

    func readerSession(_ session: NFCNDEFReaderSession, didDetectNDEFs messages: [NFCNDEFMessage]) {
        // Parse the card's information
    }
}
```
其中两个 readerSession 函数会分别告诉我们 NFC 会话成功或失败，成功后则返回 NFCNDEFMessage 格式的通信数据，失败后会返回 error 信息。
  当然，我们首先还需要初始化 NFCNDEFReaderSession 并开启 NFC 监听。

```swift
@IBAction func scanPressed(_ sender: Any) {
    nfcSession = NFCNDEFReaderSession.init(delegate: self, queue: nil, invalidateAfterFirstRead: true)
    nfcSession?.begin()
}
```
然后运行程序，看看：

![](http://upload-images.jianshu.io/upload_images/121208-c8d352a51aaf5e5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)

如果提示 Session is invalidated unexpectedly ，那么请仔细核对 证书、描述文件以及 Privacy 设置是否正确。
这个过程并不难，简单几步就能搞定，下面我们来看看怎么解析获取到的 message 。

## 解析 Message
  首先，让我们来看一下这个函数：
```
func readerSession(_ session: NFCNDEFReaderSession, didDetectNDEFs messages: [NFCNDEFMessage]) 
```
让我们来看看每一个 message 对象包含了哪些信息：

```swift
print(messages[0])
```
我们可以看到：

```json
( // Payload one (There's only one payload in this card)
    "TNF=1, /* Type Name Format */
    Payload Type=<55>,
    Payload ID=<>,
    Payload=<0048656c 6c6f21>" /* What we're really interested in */
)
```
根据打印的结果我们可以看出：
. messages 是一个 NFCNDEFMessages  对象的数组。
. NFCNDEFMessage 有一个 NFCNDEFPayload 对象数组 records
然后我们再来看看每一个 payload 又包含了哪些信息：
1. identifier.
2. type.
3. typeNameFormat. 
4. payload.

这里其实我们只关心 payload 。好了，看看如何解析 records 吧。

```swift
func readerSession(_ session: NFCNDEFReaderSession, didDetectNDEFs messages: [NFCNDEFMessage]) {
    var result = ""
    for payload in messages[0].records {
        result += String.init(data: payload.payload.advanced(by: 3), encoding: .utf8)! // 1
    }

    DispatchQueue.main.async {
        self.messageLabel.text = result
    }
}
```
来看看最终的效果：

 ![nice result](http://upload-images.jianshu.io/upload_images/121208-8afdb4462796a9e9.gif?imageMogr2/auto-orient/strip)

## 参考
[Working with CoreNFC in iOS 11
](https://www.appcoda.com/corenfc-introduction/)

水平有限，如有疏漏，望指正
欢迎交流，欢迎 star  [CNKCQ](https://github.com/CNKCQ)
遗人🌹，手有余香      [blog](http://wangchenquan.com/)


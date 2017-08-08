title: 神奇，Swift 中竟然可以这样使用保留字!
date: 2017-08-8 13:48:13
tags: [iOS,Swift]
---


前一段时间，记得我在写一个 enum 时，发现了一个有意思的事情，当我在定义 enum 时会习惯性的定义一个 default ，在 Swift 中这是编译不过的：

```Swift
enum RefreshState {
    case default
    case always
    case didLoad
}

```
<!-- more -->

比如说这样写就会报 error :
Keyword 'default' cannot be used as an identifier here
我们可以看到 keyword 是不能用作 identifier 的，编译器在报这个 error 时，大家一定会注意到可以通过 fix 来避免这样的问题：

```Swift
enum RefreshState {
    case `default`
    case always
    case didLoad
}

```

这样就没有任何 warning 或者 error 了
同理，我们在声明变量的时候也可以这样做：

```Swift
var class: String!

```
to

```Swift
var `class`: String!

```

这样看来，当我们在使用到 keyword 的时候也就不必为了避开它委屈求全了。
在 OC 中大家为了避免循环引用会经常用到 weakSelf  and strongSelf，那么 RAC 中提供了 @weakify(self) and @strongify(self), 这个实现是非常优雅的。
然而在 Swift 中，我想大家对这样的写法并不陌生吧：

```Swift
DispatchQueue.main.async { [weak self] in
            guard let strongSelf = self else {
                return
            }
            strongSelf.label.frame = CGRect(x: 40, y: 200, width: 40, height: 40)
            strongSelf.label.backgroundColor = .blue
            strongSelf.label.text = "hello"
            strongSelf.label.font = UIFont.systemFont(ofSize: UIFont.systemFontSize)
        }

```

对于这段代码，我们有什么需要优化的呢？鉴于上面所讲的我们也可以这样：

```Swift
DispatchQueue.main.async { [weak self] in
            guard let `self` = self else {
                return
            }
            self.label.frame = CGRect(x: 40, y: 200, width: 40, height: 40)
            self.label.backgroundColor = .blue
            self.label.text = "hello"
            self.label.font = UIFont.systemFont(ofSize: UIFont.systemFontSize)

        }

```

我更倾向与这样魔性的写法，你觉得呢？

水平有限，如有疏漏，望指正
欢迎交流，欢迎 star  [github](https://github.com/CNKCQ)
遗人🌹，手有余香

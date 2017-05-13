# <center>iOS设计编码规范</center>

### 1, iOS 常用系统控件

<p>1.0.1 Status Bar 状态栏</p>
状态栏的信息内容有两种不同的风格：暗色（黑）和亮色（白）:
![](http://img.ui.cn/data/file/2/8/3/138382.jpg?imageView2/2/w/900/q/90)<br>
#### 1.0.2 Navigation Bar 导航栏
导航栏总在屏幕的顶部，状态栏的正下方。默认的，导航栏背景会进行轻微半透明处理，以及对下面的内容进行毛玻璃般的模糊处理:
![](http://img.ui.cn/data/file/3/8/3/138383.jpg?imageView2/2/w/900/q/90)<br>

###### 1.0.3 Tab Bar 标签栏
标签栏在屏幕底部。默认情况下背景使用和导航栏一样的轻微半透明效果，以及使用和系统一样的模糊处理下面遮住的内容。:
![](http://img.ui.cn/data/file/3/9/3/138393.jpg?imageView2/2/w/900/q/90)<br>
###### 1.0.4 Table View 表格视图
表格视图用于呈现大多数列表风格的信息，可以一列或者多列，也可以选择几行来划分信息或分组。
纯表格:
![](http://img.ui.cn/data/file/4/9/3/138394.jpg?imageView2/2/w/900/q/90)<br>
分组表格(头部和尾部字体：13pt):
![](http://img.ui.cn/data/file/3/0/4/138403.jpg?imageView2/2/w/900/q/90)<br>
默认:
![](http://img.ui.cn/data/file/5/0/4/138405.jpg?imageView2/2/w/900/q/90)<br>
系统自带cell 默认中高度 44pt <br>
带副标题:
![](http://img.ui.cn/data/file/6/0/4/138406.jpg?imageView2/2/w/900/q/90)<br>
带数值:
![](http://img.ui.cn/data/file/9/0/4/138409.jpg?imageView2/2/w/900/q/90)<br>
表格视图每一行cell选中自带高亮
#### 1.0.5 Alerts 警告提醒
警告提醒用于通知用户关键信息，以及可以强制用户做出一些动作选择。
警告视图总包含一个标题文本，可以不限于一行（对于纯信息警告如“OK”），以及不限一个或两个按钮（请求式的决定，如“发送”和“取消”）。:
![](http://img.ui.cn/data/file/7/1/4/138417.jpg?imageView2/2/w/900/q/90)<br>
#### 1.0.6 Actions Sheets 动作菜单
用于从可执行的动作中选择执行一个动作，要求App用户选择一个动作继续，或者取消。:
![](http://img.ui.cn/data/file/5/1/4/138415.jpg?imageView2/2/w/900/q/90)<br>
#### 1.0.7 Switch 开关
用于从可执行的动作中选择执行一个动作，要求App用户选择一个动作继续，或者取消。:
![](http://img.ui.cn/data/file/6/3/4/138436.jpg?imageView2/2/w/900/q/90)<br>
#### 1.0.8 Search Bar 搜索栏
搜索栏默认有两种风格：凸显（Prominent）和最小（Minimal）风格。两种风格的功能都相同。:
（无提示栏、有提示栏）
![](http://img.ui.cn/data/file/8/8/3/138388.jpg?imageView2/2/w/900/q/90)<br>
（最小风格的搜索栏）
![](http://img.ui.cn/data/file/0/9/3/138390.jpg?imageView2/2/w/900/q/90)<br>
#### 1.0.9 Edit menu 编辑菜单
在一个元素被选定时(文本，图片及其他)，编辑菜单允许用户执行复制、粘贴、剪切等操作:
![](http://img.ui.cn/data/file/8/1/4/138418.jpg?imageView2/2/w/900/q/90)<br>

#### 1.1.1 Edit menu 浮动窗
当要求用户在程序进行的同时输入多个信息时，浮动框（Popover）是个绝佳选择:
![](http://img.ui.cn/data/file/4/2/4/138424.jpg?imageView2/2/w/900/q/90)<br>

#### 1.1.2 Segment Controls 分段控件
分段控件包含一系列分段（至少两个），可以用于筛选内容或为整理的分类内容创建标签:
![](http://img.ui.cn/data/file/9/2/4/138429.jpg?imageView2/2/w/900/q/90)<br>

#### 1.1.3 Sliders 滑块
滑块控件可以让用户从一个允许范围内滑动滑块选择一个特定的值:
![](http://img.ui.cn/data/file/3/3/4/138433.jpg?imageView2/2/w/900/q/90)<br>

#### 1.1.4 Stepper 进步器
一个步进器需要包含两个分段按钮，一个用于减少当前值，一个用于增加。:
![](http://img.ui.cn/data/file/5/3/4/138435.jpg?imageView2/2/w/900/q/90)<br>

#### 1.1.5 Picker 选择器
选择器通常用作级联表达，常见的时间选择器:
![](https://designcode.io/cloud/chapter1/Design-Picker.jpg)<br>

#### 1.1.6 Activity dialog  活动对话框
活动对话框用于内容（text, images, links）分享:
![](https://designcode.io/cloud/chapter1/Design-Share.jpg)<br>
#### 1.1.7 Collection View 网格视图
通常用于网格布局（九宫格，瀑布流）:
![](https://designcode.io/cloud/chapter1/Design-CollectionView.jpg)<br>
Collection默认选中没有高亮
#### 1.1.8 Toolbar 工具栏
当遇到多种操作可以考虑使用工具栏:
![](https://designcode.io/cloud/chapter1/Design-Toolbar.jpg)<br>

#### 1.0.8 System icons 系统icon
系统图标往往有特定的意向表达，被用户熟知，适当的应用系统图标会降低app的使用难度,  提高用户体验:
![](https://designcode.io/cloud/chapter1/iOS-Icons.jpg)<br>


### 2, iOS 常用配色方案
#### 2.0.1 iOS 系统默认颜色
|Name          |hex    |
|-----------------     |
|groupTableViewBackgroundColor	  |#EFEFF4      |
|lightTextColor   |#FFFFFF      |
|darkTextColor	  |#000000    |
|blackColor	  |#000000      |
|darkGrayColor     |#555555    |
|lightGrayColor	      |#AAAAAA    |
|grayColor|#7F7F7F     |
|whiteColor |#FFFFFF|
|redColor     |#FF0000    |
|greenColor    |#00FF00      |
|blueColor |#0000FF|
|cyanColor     |#00FFFF    |
|yellowColor    |#FFFF00      |
|magentaColor |#FF00FF|
|orangeColor     |#FF7F00    |
|purpleColor    |#7F007F      |
|brownColor     |#996633    |
|clearColor    |#000000      |

#### 2.0.2 iOS 苹果官方设计指南推荐常用颜色
![](https://cloud.githubusercontent.com/assets/8440220/18087618/b4d2e3c8-6ee8-11e6-8b9b-c0ad049e3736.png)<br>
#### 2.0.3 iOS 推荐配色
![](https://designcode.io/cloud/chapter1/Colors.jpg)<br>

### 3, iOS 常用系统字体

|Element          |Size(pt)|Weight    |Spacing(pt)|Type      
|-----------------|--------|----------|-----------|-------   |
|systemFont   |14       |default|default|Text|
|smallSystemFont|12|default|default|Text|
|buttonFont   |18|default|default|Text|
|labelFont  |17|default|default|Text|
|Nav Bar Title	  |17      |Medium    |	0.5       |Display   |
|Nav Bar Button   |17      |Regular   |0.5       |Display    |
|Search Bar	      |13.5    |Regular   |0         |Text       |
|Tab Bar Button	  |10      |Regular   |0.1       |Text       |
|Table Header     |12.5    |Regular   |0.25      |Text       |
|Table Row	      |16.5    |Regular   |0         |Text       |
|Table Row Subtitle|12      |Regular   |0         |Text       |
|Table Footer     |12.5    |Regular   |0.2       |Text       |
|Action Sheets    |20      |Regular / Medium|0.5 |Display    |


https://developer.apple.com/ios/human-interface-guidelines iOS官方设计指南 <br>
http://www.ui.cn/detail/32167.html 中文 <br>
http://ivomynttinen.com/blog/ios-design-guidelines 英文 <br>
http://ms.csdn.net/geek/72062 全新Apple iOS设计规范指南 <br>
http://wiki.jikexueyuan.com/project/ios-9-human-computer-interface-guidelines/ iOS 9 人机交互指南 <br>
https://designcode.io/iosdesign-guidelines <br>

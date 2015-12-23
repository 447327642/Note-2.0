# Programming iOS9 翻译及学习笔记

不会逐字逐句翻译，但是会尽量记录关键的概念和要点，以便深入理解。具体的名词我就只在标题中翻译，不然一是容易弄混，二是难以保持一致。

<!-- MarkdownTOC -->

- 第一章 视图 Views
    - The Window
- Drawing
- Layers
- Animation
- Touches

<!-- /MarkdownTOC -->


# 第一章 视图 Views

一个 view (一个 `UIView` 类 / `UIView` 的子类对象或者) 知道如何在界面中的一个矩形区域绘制自己。

一个 view 也是一个 responder (`UIView` 是 `UIResponder` 的子类)。也就是说，view 不仅可以给用户看，还可以给用户交互。

View hierarchy 是主要的 view 组织形式。一个 view 可以有多个 subviews，但是一个 subview 只能有一个直接的 superview。所以很多 view 就会组成一棵树。如果一个 view 被移出 view hierarchy，它的子类也会被移除；如果一个 view 被隐藏，它的子类也会被隐藏；如果一个 view 移动，它的子类也会被移动。View hierarchy 也是 responder chain 的基础（但是不一定完全一致）

一个 view 可以从 nib 生成，也可以在代码中创建。总体来看，哪种方法都没有比另一种好特别多，具体选择哪种需要具体情况具体考虑，不能一概而论

## The Window

View hierarchy 的顶层是应用的 window，是 `UIWindow`(`UIView` 的子类) 的一个实例。你的应用应该只有一个 main window。它将在应用启动时被创建并且不会被销毁或者代替。它是应用的背景并且是终极 superview，也就是所有其他的 view 都是它的 subviews。

> 假如你的应用要显示在外接屏幕上，就需要创建额外的 `UIWindow`，但是这种情况不在这一章的讨论范围

应用的 window 必须填充设备的 `screen`，具体的做法是在 window 初始化时把 window 的 `frame` 设置成 screen 的 `bounds`（`frame` 和 `bounds` 是很容易搞混的概念，之后会解释它们的异同）。使用 main storyboard 的话这个事情会由 `UIApplicationMain` 函数在应用启动的时候自动完成。如果不用 main storyboard 的话就需要自己在应用的声明周期中创建 window 并且设置好 `frame`

```swift
let w = UIWindow(frame: UIScreen.mainScreen().bounds)

// iOS 9 中可以不传入 frame，默认会设置成 screen 的 bounds，如下
let w = UIWindow()
```

这个 window 必须在应用而生命周期中一直保持着。为了做到这样，`app delegate` 类会用一个 strong retain policy 来持有一个 `window` 属性。具体的过程是：在应用启动时，`UIApplicationMain` 方法会初始化 `app delegate` 类并且一直持有它，然后 window 实例就会被赋值到 `app delegate` 的 `window` 属性上，所以也会被一直持有。

通常来说不要手动或者直接在 main window 上添加任何 view。通常来说，你会得到一个 view controller 并且会被赋值到 main window 的 `rootViewController` 属性上。如果你用的是 main storyboard，这都都会自动初始化好。

当一个 view controller 成为 main window 的 `rootViewController`，它的 view 就成为了 main window 有且仅有的一个直接 subview，也就是 main window 的 root view。之后所有的 view 都只能是这个 root view 的 subview。也就是说 root view 是 view hierarchy 中用户通常能看到的地位最高的对象。

但是有些时候用户可能会看到 root view 之后的 window，所以最好给这个 window 设置合适的 `backgroundColor`。但通常来说我们没有理由去对 window 本身做任何修改。

应用的界面在对应的 window 被设置为 key window 之前都是不可见的。这个可以通过调用 `UIWindow` 实例的 `makeKeyAndVisible` 方法来完成。

来总结下 main window 从创建、配置到显示的过程

+ 使用 main storyboard
	+ storyboard 文件在 `Info.plist` 的键为 `Main storyboard file base name` 中指定(`UIMainStoryboardFile`)
	+ `UIapplicationMain` 实例化 `UIWindow` 并设置好 `frame`
	+ 把设置好的 `UIWindow` 的实例指定给 app delegate 的 `window` 属性
	+ 实例化 view controller 并指定给 window 的 `rootViewController` 属性
	+ 这些都发生在 app delegate 的 `application:didFinishLaunchingWithOptions:` 被调用之前
+ 不使用 main storyboard
	+ 因为项目模板都会自带 storyboard，所以需要做以下步骤来获得一个纯净的空白项目
		+ 在 General pane，选择 Main 并且删除
		+ 删除 Main.storyboard 以及 ViewController.swift
		+ 删掉 AppDelegate.swift 中的所有内容
	
![pios1](_resources/pios1.jpg)

通常来说我们不会需要 `UIWindow` 的子类。

应用一旦运行，有多种方法来引用主 windows

+ 如果一个 `UIView` 在界面中，它自动会有一个 `window` 属性，里面有对 window 的引用
	+ 也可以使用 `UIView` 的 `window` 属性来检查这个 view 是不是被嵌入到了 window 中。如果不是，那么 `window` 属性为 `nil`。一个 `window` 属性为 `nil` 的 `UIView` 对用户来说是不可见的
+ app delegate 实例会维护一个指向 window 的引用(`window` 属性)，可以通过 shared application 来获取
	+ `let w = UIApplication.sharedApplication().delegate!.window!!`
	+ 如果想要不那么通用的方法，可以显式转换成 app delegate 类
	+ `let w = (UIApplication.sharedApplication().delegate as! AppDelegate).window!`
+ shared application 会在 `keyWindow` 属性中维护一个指向 window 的引用
	+ `let w = UIApplication.sharedApplication().keyWindow!`
	+ 这个引用不是很稳定，因为系统可能会创建临时的 window 并且把它们当做 key wind

	
		
	
## Experimenting With Views
	
这里只介绍有 storyboard 的情况。创建一个 Single View Application，并在对应的 view controller 的 `viewDidLoad()` 方法中添加如下代码：

![pios2](_resources/pios2.jpg)

效果如下

![pios3](_resources/pios3.jpg)

## Subview and Superview

在 iOS 中，subview 的一部分甚至是全部，可以出现在 superview 之外。一个 view 可以和另一个 view 重叠，即使不是其 subview 也可以绘制部分或全部绘制在另一个 view 之前。

![pios4](_resources/pios4.jpg)

View Hierarchy 的特点

+ 如果一个 view 被移出或者引入它的 superview，它的 subview 会跟着
+ 一个 view 的透明度会被其 subview 继承
+ 一个 view 可以限制 subview 的显示范围，比如不让 subview 超出 view 本身的范围，这叫做 clipping，被设置在 `clipsToBounds` 属性中
+ 一个 superview 拥有它的 subview
+ 如果一个 view 的尺寸变化了，它的 subview 也会自动被重新设置尺寸
	
一个 `UIView` 有一个 `superview` 属性(一个 `UIView`)和一个 `subviews` 属性(一个 `UIView` 对象的数组，back-to-front 顺序)，可以据此来判断 view hierarchy。另外也有一个 `isDescendantOfView:` 方法来检查一个 view 是不是另一个 view 的 subview。View 还有一个 `tag` 属性，可以通过 `viewWithTag:` 来进行引用。

在代码中操作 view hierarchy 很简单。`addSubview:` 方法添加一个 subview，`removeFromSuperview` 移除一个 subview。

注意从 superview 中移除 subview 同时也会释放它，所以如果需要之后重用的话，最好先确定能够把它保存在内存中，通常的方法是把这个 view 保存在一个属性中。

在进行这些操作时系统也会给出通知，重写下列方法就可以根据需要在不同的情况下进行不同的操作：

+ `didAddSubview`, `willRemoveSubview`
+ `didMoveToSuperview`, `willMoveToSuperview`
+ `didMoveToWindow`, `willMoveToWindow`
	
当 `addSubview:` 被调用时，这个 view 会被放到其 superview 的 subview 数组中的最后一个，也就是说会被最后画出来，即出现在最前面。一个 view 的 subviews 是被索引的，从 0 开始(rearmost)。可以把一个 view 插入到指定位置，以及放到前面/后面，或交互两个 view

+ `insertSubview:atIndex:`
+ `insertSubview:belowSubview:`, `insertSubview:aboveSubview:`
+ `exchangeSubviewAtIndex:withSubviewAtIndex:`
+ `bringSubviewToFront:`, `sendSubviewToBack:`
	
奇怪的是，没有一个方法可以直接移除一个 view 的所有 subview。然而，因为一个 view 的 subview 数组是一个不可变的数组，所以可以用如下方法一次移除全部：

```swift
myView.subviews.forEach {$0.removeFromSuperview}
```

## Visibility and Opacity

视图的可见性可以通过设置 `hidden` 属性来更改。一个隐藏的 view 无法接收触摸事件，所以对于用户来说相当于不存在，但实际上是存在的，所以仍然可以在代码中对其操作。

View 的背景颜色可以通过其 `backgroundColor` 属性来设置，颜色属于 `UIColor` 类。如果 `backgroundColor` 为 `nil` 那么背景就是透明的。可以通过设置 view 的 `alpha` 属性来修改透明程度，1.0 是完全不透明，0.0 是透明。假设一个 view 的 `alpha` 是 0.5，那么它的 subview 的 `alpha` 都是以 0.5 为基准的，不可能高于 0.5。而 `UIColor` 也有 `alpha` 这个属性，所以即使一个 view 的 `alpha` 是 1.0，它仍旧可能是透明的，因为其 `backgroundColor` 可以是透明的。一个 `alpha` 为 0.0 的 view 是完全透明的所以是不可见的，通常来说也不可能被点击。

View 的 `alpha` 属性不仅影响背景颜色，也会影响其内容的透明度。

View 的 `opaque` 属性的修改并不会影响 view 的样子，更多的是对于系统绘制时的提示。如果一个 view 的 `opaque` 设为 true，因为不用考虑透明的绘制，所以效率会高一点，并且再设置透明的背景颜色或者 `alpha` 属性都无效。可能会让人吃惊，它的默认值是 true

## Frame

View 的 `frame` 属性(`CGRect` 类) 是它本身的长方形在 superview 中的位置，注意是在 superview 的坐标系中的位置。默认来说，superview 的坐标系原点在左上，向右 x 增加，向下 y 增加。

给 view 的 `frame` 设置不同的 `CGRect` 值相当于重新摆放 view 的位置或改变其尺寸（也可以两个同时更改）。默认的 `frame` 是 `CGRectZero`，所以一般都需要自己初始化来确定所需位置及大小。

一个初学者常见的错误就是没有初始化 `frame` 这样程序会默认一个在原点并且长宽为 0 的矩形，于是也就相当于看不见，可以参考如下代码了解 `frame` 的使用

![pios5](_resources/pios5.jpg)

效果如下（就是之前图中的形状，注意 v2 是添加在 v1 上的）：

![pios6](_resources/pios6.jpg)

## Bounds and Center

`bounds` 属性对应的是一个 view 在自己的坐标系统中的矩形尺寸（注意，`frame` 是在 superview 的坐标系下的），例如如下代码：

![pios7](_resources/pios7.jpg)

效果如下：

![pios8](_resources/pios8.jpg)

这是一种很常见的 `bounds` 的用法，当你需要往一个 view 里放东西的时候，无论是手动绘制还是放置一个 subview，通常都要使用 view 的 `bounds`

当你改变一个 view 的 `bounds` 时，它的 `frame` 也会对应改变，frame 的改变是基于其中心点的（中心点不会变），下面的代码描述了这个情况：

![pios9](_resources/pios9.jpg)


效果就是从上图变成了下图，增加的 20 会被均匀分布在上下左右，正好抵消了之前的设置

![pios10](_resources/pios10.jpg)

当创建一个 `UIView` 时，其 `bounds` 的坐标原点是 (0.0, 0.0)，也就是左上角，如果改变了 `bounds` 的原点，也就改变了其坐标系，其 subview 一般也会有变化，下面代码描述了这种情况

![pios11](_resources/pios11.jpg)

效果如下

![pios12](_resources/pios12.jpg)

可以看到 subviw 向着原点移动方向的反方向进行了移动，这是因为一个 view 的原点与其 frame 的左上角一致。

改变 view 的 bounds 大小会影响到 frame，反之亦然，唯一不变的是 view 的 center，可以通过下面代码获取

```swift
let c = CGPointMake(theView.bounds.midX, theView.bounds.midY)
```

改变 view 的 bounds 不会影响其 center，改变一个 view 的 center 不会影响其 bounds。所以其实一个 view 的 bounds 和 center 就可以确定其在 superview 中的位置，frame 可以看作是一个由 bounds 和 center 组成的表达式的简便写法而已。注意有些情况下 frame 会没有什么意义，但是 bounds 和 center 总是有效的，所以建议多用 bounds 和 center 的组合，也比较容易理解。

+ bounds: 一个 view 自己的坐标系统
+ center: 一个 view 的坐标系统和其 superview 的坐标系统的关系

可以用如下方法来进行不同 view 之间的坐标转换

+ `convertPoint:fromView:`, `convertPoint:toView:`
+ `convertRect:fromView:`, `convertRect:toView:`

如果第二个参数为 `nil`，那么就取 window 的值。例如，如果 v2 是 v1 的 subview，那么要把 v2 放到 v1 的中心，就可以用

```swift
v2.center = v1.convertPoint(v1.center, fromView: v1.superview)
```

注意，通过改变 center 来设置 view 的位置时，如果高或宽不是偶数，那么可能会导致 `misaligned`。可以通过打开模拟器的 Debug -> Color Misaligned Images 来进行检测。一个简单的方法是调整好位置之后调用 `makeIntegralInPlace` 来设置 view 的 frame，

## Window Coordinates and Screen Coordinates

设备屏幕是没有 frame 的，但是有 bounds。Main window 也没有 superview，不过其 frame 被设置为屏幕的 bounds，如：

```swift
let w = UIWindow(frame: UIScreen.mainScreen().bounds)
```

在大部分情况下，window 坐标系就是 screen 坐标系。现在的 iOS 中坐标系和手机是否选择是有关的，有如下两个属性：

+ UIScreen 的 `coordinateSpace` 属性
	+ 这个坐标空间会旋转，就是高和宽在设备旋转时会呼唤，(0.0, 0.0) 是这个 app 本身的左上方
+ UIScreen 的 `fixedCoordinateSpace` 属性
	+ 这个坐标空间不会变化，就是物理上的左上角，从用户来看，这里的 (0.0, 0.0) 可能是 app 本身的任何一个角

可以用下面的方法来对不同坐标空间进行转换：

+ `convertPoint:fromCoordinateSpace:`, `convertPoint:toCoordinateSpace:`
+ `convertRect:fromCoordinateSpace:`, `convertRect:toCoordinateSpace:`

假设界面中有一个 `UIView` v，我们想知道它的实际设备坐标，可以用下面的代码：

```swift
let r = v.superview!convertRect(v.frame, toCoordinateSpace: UIScreen.mainScreen().fixedCoordinateSpace)
```

但实际上你需要这种信息的机会非常少，或者其实几乎都不用担心 window 坐标，因为所有的可见操作都会在 root view contoller 的 main view 中进行，它的 bounds 是会自动调整的。

## Transform

一个 view 的 `transform` 属性改变这个 view 是如何被绘制的，实际上就是一个 `CGAffineTransform`类的 3x3 矩阵（线性代数中的概念）。所有的变换都是以这个 view 的 center 做基准的，下面就是具体的例子：

![pios13](_resources/pios13.jpg)

效果如下，注意这里用的是弧度，需要自己转换一下

![pios14](_resources/pios14.jpg)

注意，这里 view 的 center 和 bounds 都没变，但是 frame 的数值已经没有意义，因为现在它的尺寸是能够覆盖当前 view 的最小的矩形，并不会随着 view 的旋转而选择。

根据仿射变化的定义，因为背后实际上是矩阵乘法，所以不同的变换是可以叠加的，并且顺序是重要的（矩阵乘法不满足交换律）

## Trait Collections and Size Classes

界面上的每个 view 都有一个 `traitCollection` 属性，值是一个 `UITraitCollection`，包含下面四个属性：

+ `displayScale`，由当前屏幕决定的缩放尺寸，1(single resolution) 2(double resolution) 3(iPhone 6/6s Plus)
+ `userInterfaceIdiom`，一个 `UserIterfaceIdiom` 值，可能是 `.Phone` 或 `.Pad`，来标志不同的设备，默认来说和 `UIDevice` 的 `userInterfaceIdiom` 属性一致
+ `horizontalSizeClass`, `verticalSizeClass`，是 `UIUserInterfaceSizeClass` 值，可能是 `.Regular` 或 `.Compact`
	+ 水平和竖直都是 `.Regular` -> iPad
	+ 水平是 `.Compact` 竖直是 `.Regular` -> iPhone 在垂直方向，或者 iPad 的分屏应用
	+ 水平和竖直都是 `.Compact` -> iPhone 在水平方向(iPhone 6/6s plus除外)
	+ 水平是 `.Regular` 竖直是 `.Compact` -> iPhone 6/6s Plus 在水平方向

当应用运行时如果 trait collection 发生改变，会调用 `traitCollectionDidChange` 方法

## Layout

假设 superview 的 bounds 变化，其 subview 的 bounds 和 center 是不会变的，实际应用中我们可能更需要 subview 根据 superview 的变化来变化。通常这就是 Layout。

Layout 有三种主要的执行方式

+ 手动 layout：superview 在被更改尺寸会会发送 `layoutSubviews` 消息，如果你新建自己的子类并且重写 `layoutSubviews` 就可以手动进行更改，这很麻烦，但是可以做任何你想做的事情
+ Autoresizing：iOS 6 之前的方式，主要是通过自己的 `autoresizingMask` 属性来变化
+ Autolayout：根据 view 的 constraints(`NSLayoutConstraint`) 来进行变化，是很强大的功能，不用写代码就可以进行复杂的定制

通常不会用到手动 layout，autoresizing 基本也是自动的，autolayout 主要在 xCode 的编辑器中进行设定。在代码中创建的 view 默认使用 autoresizing 而不是 autolayout

Autolayout 博大精深，我个人感觉还是在编辑器中用一个比较明确的逻辑来设定要比在代码中设定直观，具体的用法可能看视频会更加清晰，这里略过书中的讲解部分。

# 第二章 绘制 Drawing

这一章会介绍如何自定义绘制代码，来让诸如 `UIImageView` 或 `UIButton` 达到期望的展现形式

## Images and Image Views

`UIImage` 可以从磁盘中读入一个文件，支持 TIFF, JPEG, GIF 和 PNG。iOS 对 png 支持比较好，可能的话尽量用 png 格式。还可以通过网络下载的方式获得图片

## Image Files

可以通过 `UIImage` 中的 `init(named:)` 方法来获取一幅已有的图片，它会在如下两个地方查找：

+ Asset catalog（先查找）
+ Top level of app bundle（后查找）

如果调用 `init(named:)` 时已有图片数据的缓存，那么就可以立即被载入。如果使用 `init(contentsOfFile:)` 则不会缓存，这个方法需要提供一个地址字符串，可以通过 `NSBundle.mainBundle()` 来获取当前 bundle 的地址。载入的时候会根据屏幕分辨率自动载入对应的素材，后缀名可能会加上 `@2x` 或 `@3x`。如果名字的后面加上 `~ipad`，则会在 iPad 上运行的时候自动被使用。

使用 Asset catalog 就可以用图形化界面摆脱这些命名，直接拖入到对应的格子即可。

## Image Views

许多 Cocoa 界面对象都可以接受 `UIImage` 来进行绘制，比方说 `UIButton`, `UINavigationBar`, `UITabBar`

一个 `UIImageView` 可以有两幅图片，一幅是 `image` 属性，另一幅是 `highlightedImage` 属性，在用户点击图片的时候不会自动切换到 highlight，在其他一些情况比方说点击 tableview 时会自动切换。

`UIImageView` 也有背景颜色也可以透明，替换图片只需要修改 `image` 属性，移除图片就把 `image` 属性设为 `nil` 即可。

具体的绘制方法由 `contentMode` 属性决定，是拉伸？还是剪裁之类的效果，可以自己设定。

还应该注意 `clipsToBounds` 属性，如果这个是关闭的，图片可能会超出 imageView 本身的边界。

用代码创建 `UIImageView` 可以利用便捷构造函数 `init(image:)`, `init(image:highlightedImage:)`。默认的 `contentMode` 是 `.ScaleToFill`

![pios15](_resources/pios15.jpg)

效果如下

![pios16](_resources/pios16.jpg)

## Resizable Images

有时候需要可以改变尺寸的 image，比方说进度条。创建的方法也很简单，和创建一个正常的 image 一样然后调用 `resizableImageWithCapInsets:resizingMode:` 方法，`capInsets:` 参数是一个 `UIEdgeInsets`，根据 `resizingMode:`(`UIImageResizingMode`)的不同会有不同的表现形式（`.Tile` 和 `.Stretch`）。

下面是不同样式的例子，先是 `.Tile` 模式

![pios17](_resources/pios17.jpg)

![pios18](_resources/pios18.jpg)

![pios19](_resources/pios19.jpg)

![pios20](_resources/pios20.jpg)

然后是 `.Stretch` 模式

![pios21](_resources/pios21.jpg)

![pios22](_resources/pios22.jpg)

![pios23](_resources/pios23.jpg)

![pios24](_resources/pios24.jpg)

还可以通过 slice, clip 等操作来获得更多的自定义效果

## Image Rendering Mode

iOS 应用界面的某些地方会把图片当做 transparency mask(template)，也就是说，颜色的值不重要，只有 alpha 的值有用。比方说 tab bar item 的图片就是这样显示的

怎么被处理是由 image 的 `renderingMode` 决定的，这个属性是只读的，如果需要改变，就要用 `imageWithRenderingMode:` 来创建一个新的图片，不同的模式(`UIImageRenderingMode`)有：

+ `.Automatic`
+ `.AlwaysOriginal`
+ `.AlwaysTemplate`

![pios25](_resources/pios25.jpg)

也可以在 asset catalog 里修改 rendering mode

## Reversible Images

针对不同的阅读方向所设定的属性，有些是从右往左读的，某些时候可能需要反转图片的方向。

设定 `imageFlippedForRightToLeftLayoutDirection`。这个无法在 asset catalog 里设定

![pios26](_resources/pios26.jpg)

## Graphics Context

如果想自己绘制一些图形，就要用 graphics context 了，这一部分主要是自定义，暂时略过，后面会专门介绍

# 第三章 层 Layers

`UIView` 的好伙伴叫做 layer(`CALayer`)。一个 `UIView` 不会直接把自己绘制到屏幕上，而是绘制到 layer 上，然后再由 layer 投射到屏幕上。

Layers 拓展了 view 的能力：

+ Layers have properties that affet drawing
+ Layers can be combined within a single view
+ Layers are the basis of animation

## View and Layer

一个 `UIView` 实例有一个附属的 `CALayer` 实例，可以通过访问 `layer` 属性来获取。这个 layer 没有对应的 `view` 属性，但是 view 是这个 layer 的 delegate。

要自定义的话可以通过下面的方式，其中 `CompassLayer` 是 `CALayer` 的子类

![pios27](_resources/pios27.jpg)

一个 `UIView` 必须是其 layer 的 delegate，也不能是其他 layer 的 delegate。

如果改变了 `UIView` 的尺寸，默认是不会进行重新绘制的，而是用一个拉伸的缓存 layer image，直到调用 `drawRect:` 方法，才会重新绘制

## Layers and Sublayers

layer 可以有 sublayers，一个 layer 只能有一个 superlayer，实际上就和 view 一个意思，但是 layer 可以拓展得更复杂一些

![pios28](_resources/pios28.jpg)

## Manipulating the Layer Hierarchy

和 view hierarcy 一样，也有很多方法来修改 layer hierarchy。一个 layer 有一个 `superlayer` 属性和一个 `sublayers` 属性，以及如下方法：

+ `addSublayer:`
+ `insertSublayer:atIndex:`
+ `insertSublayer:below:`, `insertSublayer:above:`
+ `replaceSublayer:with:`
+ `removeFromSuperlayer:`

和 `subviews` 属性不同的是，`sublayers` 属性是可写的，因此可以一次给一个 layer 多个 sublayers，或者也可以一次清除掉所有的 sublayers，只要把 `sublayers` 属性设置为 `nil` 即可。

因为每个 layer 有一个 `zPosition` 属性(`CGFloat`)，所以可以利用这个属性来设递归绘制的顺序，数字越大越迟绘制也就在越上面（默认值是 0.0）

有些时候使用 `zPosition` 要比调整数组顺序方便得多

当然也提供了坐标系统转换的函数：

+ `convertPoint:fromLayer:`, `convertPoint:toLayer:`
+ `converRect:fromLayer:`, `convertRect:toLayer:`

## Positioning a Sublayer

与 view 不同的是，sublayer 没有 center，一个 sublayer 是通过下面两个属性共同来决定在 superlayer 中的位置的：

+ position: 在 superlayer 坐标系中一个点的位置
+ anchorPoint: position 这个点会放在当前 layer 的哪个位置，相当于把 sublayer 挂在 superlayer 上，是一个 `CGPoint` 值来表示比例，(0.0, 0.0) 是左上，(1.0, 1.0) 是右下

anchorPoint 默认是 (0.5, 0.5)，相当于 center，所以可以说 view 的 center 是一个弱化版的 layer 属性。

position 和 anchorPoint 是相互独立的。layer 的 frame 也是根据 bounds, position 和 anchorPoint 计算出来的。也就是说 frame 其实就是一个设置的接口，设置了 frame 就同时设置了 bounds 和 position

## CAScollLayer

不要被名字舞蹈，实际上这个类不提供任何滚动有关的功能

操作 `CAScrollLayer`

+ `scrollToPoint:` 把当前 `CAScrollLayer` 的 bounds 的原点移动到某一个位置
+ `scrollToRect:` 把当前 `CAScrollLayer` 的 bounds 的原点移动尽量少的位置使得它能被显示出来

## Layout of Sublayers

iOS 中没有 layer 的 automatic layout。当一个 layer 的 bounds 改变或者调用了 `setNeedsLayout` 时才会进行重新布局，有以下两种处理方式

+ 当前 layer 的 `layoutSublayers` 方法会被调用，可以子类化一个 `CALayer` 然后重写该方法
+ 在 layer 的 delegate 中实现 `layoutSublayersOfLayer:` 方法

## Drawing in a Layer

通过给 `content` 属性赋值来进行绘制，注意这里不是用 `UIImage` 而是用 `CGImage`，用 `UIImage` 会什么都显示不出来但是没有任何错误提示！

![pios29](_resources/pios29.jpg)

类似 `UIView` 的 `drawRect` 方法，layer 也有类似的绘制方法，但是都是由 layer 自己维护，绝不要自己自己去调用。以下这些情况会使得 layer 重新绘制自己：

+ 如果其 `needsDisplayOnBoundsChange` 属性是 false（默认值），唯一可以让 layer 重新绘制自己的就是调用 `setNeedsDisplay` 或 `setNeedsDisplayInRect:`，这并不能保证立刻就进行重绘，如果非要重绘不可，那么同时调用 `displayIfNeeded`
+ 如果其 `needsDisplayOnBoundsChange` 属性是 true，那么当 layer 的 bounds 变化时就会进行重绘

以下是 layer 重绘自己时会调用的四个方法，从中挑选一个来实现即可，不要试图组合起来：

+ 子类中的 `display` 	
	+ 你的 `CALayer` 的子类可以重写 `display`。这时没有任何 grphics context，所以其实能力比较有限，基本只能设置 contents image
+ delegate 中的 `displayLayer`
	+ 和上面的情况类似，基本只能设置 contents image
+ 子类中的 `drawInContext`
	+ 你的 `CALayer` 的子类可以重写 `drawInContext:` 方法，参数是 graphic context，并不是自动设为当前的 context
+ delegate 中的 `drawLayer:inContext:`
	+ 和上面的情况类似，第二个参数是 graphic context，并不是自动设为当前的 context

给一个 layer 赋值一幅图片与直接在 layer 上绘制在效果上是互斥的：

+ 如果 content 被赋值为图片，那么图片会立刻被显示出来并覆盖上面所有内容
+ 如果 content 是用上述后面两个方法绘制的，那么绘制的会覆盖 layer 上显示的图片
+ 如果上面四个方法都没有具体实现，那么这个 layer 为空，什么都没有

注意，如果一个 layer 是一个 view 的 underlying layer，那么通常不用上面的方法，而是直接重写 view 的 `drawRect:` 方法

还有，千万不要给 view 的 underlying layer 设定 delegate，这个 view 就是其 delegate。

Layer 也有很 view 一样的类似属性：`contentsScale`, `backgroundColor`, `opacity`, `opaque`

![pios30](_resources/pios30.jpg)

## Content Resizing and Positioning

一个 layer 的内容会被缓存为位图，然后根据不同属性的设定来进行配置

+ 如果 content 来自一幅图片，那么缓存的内容就是那张图，尺寸就是 `CGImage` 的尺寸
+ 如果 content 来自 graphic context，那么会缓存整个 graphic context，尺寸是当时绘制的大小

属性包括：`contentsGravity`, `contentsRect`, `contentsCenter`

## Layers that Draw Themselves

一些内置的 `CALayer` 的子类提供一些非常基础但是有用的自绘能力

`CATextLayer`, `CAShapeLayer`, `CAGradientLayer`

## Transforms

变形和 view 的基本类似，唯一不同是 view 的不动点是 center，layer 的不动点是 anchorPoint

还可以做三维的变换，比如修改 `anchorPointZ` 属性，并且由 `CATransform3D` 这个类来描述具体的变换，具体的变换也是数学，举个例子，下面的代码沿着 y 轴翻转了 layer

```swift
someLayer.transform = CATransform3DMakeRotation(CGFloat(M_PI), 0, 1, 0)
```

## Depth

其实有两个属性可以更改，并且是相关的，是：`zPosition` 和 z-direction translation。一般用前者就好。

修改 `zPosition` 的值会让物体看起来变大或者变小，但是并不是因为实现了透视。

可以利用 `CATransformLayer` 做出景深和立体的动画效果

## Shadows, Border, and Masks

对应的属性为 `shadowColor`, `shadowOpacity`, `shadowRadius`, `shadowOffset`

![pios31](_resources/pios31.jpg)

## Layer Efficiency

通常来说，opaque drawing 是最有效率的，也可以通过更改 `drawAsynchronously` 属性来异步绘制

## Layers and Key-Value Coding

所有 layer 的属性都是通过 键值对来访问的，我们可以用下面两种方法来赋值：

```swift
layer.mask = mask
layer.setValue(mask, forKey: "mask")
```

这种方法主要是为了下一张动画的实现而设计的

# 第四章 动画 Animation

如果要从头折腾动画是很难的，因为有太多的计算，不过好在 iOS 给我们提供了很多帮助，我们只需要描述和指定动画，系统会自动帮我们做，也就是 animation on demand。

设置动画就好像设置属性一样简单，例如下面一行代码就可以实现一个动画：

```swift
myLayer.backgroundColor = UIColor.redColor().CGColor // animate to red
```

## Drawing, Animation and Threading

小提示：在模拟器中可以 Debug -> Toggle Slow Animations 来用慢动作播放动画用来测试

系统会在重绘时累计所有的绘制并一次性绘制出来，举个例子，加入现在背景是绿色的，下列代码并不会使背景改变。

![pios32](_resources/pios32.jpg)

因为直到代码执行完成才会最后绘制最新的改动，也就还是绿色。

动画也大概是这样的工作机制。当你请求一个动画效果，直到下一次重绘时都不会开始动画。动画的机制是很有趣的，比方说你要通过动画把一个 view 从位置 1 移动到位置 2，你可以这样做：

1. 把 view 放置在位置 2，但是还没有到重绘的时间，所以仍然显示是在位置 1
2. 指定一个从位置 1 到位置 2 的动画
3. 然后代码执行完成
4. 现在是重绘的时候，如果没有动画，那么 view 会直接出现在位置 2。但因为有动画，所以动画就从位置 1 开始
5. 动画出于 in-flight 状态，从位置 1 变化到位置 2
6. 动画在位置 2 结束
7. 然后动画被移除，这时候 view 在位置 2，虽然其实一开始它就被放到位置 2 了

了解到动画和真实的 view 不是同一回事儿是配置好动画的关键。动画会在名为 `presentation layer` 上进行展示，可以通过访问 `presentationLayer` 方法来操作

动画是多线程执行的，所以不用特别操心

# 第五章 触摸 Touches



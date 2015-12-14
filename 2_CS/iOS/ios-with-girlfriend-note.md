# 给女朋友的 iOS 开发教程学习笔记

这是来自网络的一个视频教程，以一个完整项目为实战，感觉应该还是不错的，地址在[这里](https://www.youtube.com/watch?v=LEQpV9znZsk)。

基本会覆盖以下内容：

+ Design
+ Network
+ API
+ Xcode 7
+ Swift
+ UICollectionView
+ UIImageView
+ Cocoapods
+ UIButton
+ UIKit Animation
+ Core Animation
+ Git

## 1 Design

+ Font
	+ 适应不同风格的技巧：换字体，居中，全大写，加粗，根据表达的意思来更改设计，更改颜色
+ Elements: 多个元素如何组织
	+ 一起居中，靠近，更改背景来切换感觉，用布局来表现逻辑关系，非主题透明度
	+ 英文可以改成大写减少可读性凸现主体
	+ 边距，线条宽度，是否填充
+ Detail
	+ 无谓的圆角，卡片式设计，文字细节斟酌，操作的融合
	+ 打破常规，沉浸式体验，阴影
+ Color
	+ 颜色对比，圆角按钮，线条按钮，更改字体，实心线条，情绪设计
	+ 主题色：渐变，去掉边框，情感

问自己几个问题

+ 我要通过这个设计表达什么情感？
+ 我要通过这个设计强调什么？
+ 我能不能做一些突破？
+ 如何可以更加雕琢设计？

## 2 Design An App

+ Architecture
	+ Tabbar: Apple Music, 微博, iBooks, Medium
		+ 各个部分没有太紧密的逻辑关系
		+ 简化上下文关系
		+ 生产力，高效
		+ 用户容易理解
	+ Slide Menu: VOUN, ThunderSpace HD, Inbox by Gmail, Uber
		+ Unicorn，最核心的功能只有一个
		+ Immediate，可以快速完成操作
	+ Slider: Kickstarter, Tinder, Sooshi
		+ Focus
	+ Waterfall: Twitter, Instagram, Freeshot, Flipboard
		+ Timeline
		+ Efficiency
		+ Just repeating 简单动作重复，让人上瘾
+ Design and Purpose
	+ 开始阶段
		+ Trust 如何让用户相信，愿意去用
		+ Marketing 用户可能会因为应用漂亮而口碑传播
		+ Identity 让人记住，有辨识度
	+ 发展阶段
		+ Productivity
		+ Function 与产品的定位有关
			+ Category 定位
			+ Character 给人的感觉
			+ Design is not just what it looks like and feels like. Design is how it works. --- Steve Jobs
	+ User Interface
		+ Consistency 一致性
			+ Font Style
			+ Layout
			+ Elements
			+ Color
			+ Interaction 
		+ Custom
			+ Along with your function
			+ Unique experience
			+ Journey of soul 

## 3 Design Interface

Sketch 的使用教程

+ `a`: 打开不同设备不同尺寸模板
+ 按住 `shift` 可以得到正圆形/正方形/正多边形
+ 可以点击 `edit` 来编辑多边形
+ 可以给不同的元素弄成一个 group 方便管理
+ 等比例缩放用 `scale` 工具
+ 注意渐变的使用
+ 可以用 rectangle + radius 来做圆角按钮
+ Avenir 字体很漂亮
+ Mask 功能

其他的都可以自己慢慢摸索，不算特别难，设计部分需要慢慢学习，这里就是大概了解工具怎么用

## 4 Code Swift

前面说了很多编程历史，照这样看来我也可以怒吹一波，接下来是利用 Playground 带大家入门，很基础，其实看我的学习笔记也是可以的

## 5 Code Swift Advance

讲的是面向对象的内容，还是可以看我的学习笔记

## 6 Meet iOS APP

比较基础，这里记录一下大概的步骤

+ 创建一个 Single View Application
+ Debug View Hierarchy 可以把 app 界面立体分解，一个层级结构
+ View Hierarchy 是一个好东西，很清晰展现布局
+ Xcode 基本的使用指导，界面与功能
+ 在 build 到真机的时候会有问题(swift 才会出问题)
	+ Library not loaded: @rpath/libswiftCore.dylib
	+ 最重要的一步，要下载一个[证书](http://developer.apple.com/certificationauthority/AppleWWDRCA.cer)
	+ 安装之后重新 clean build 一下就好了
+ 在 storyboard 中的 custom class 选择关联的 `view controller`
	+ 类似于安卓中 `findviewbyID`
+ `UIViewController` 自带一个 `UIView`
	+ 可以在 `viewDidLoad()` 中对 `view` 变量进行操作
	+ 如：`view.backgroundColor = UIColor.greenColor()`
+ 按住 `option` 键指向函数或者变量可以查看帮助
+ 添加控件可以直接拖拽，然后通过在 `Alignment` 里添加 `Horizantally` 和 `Vertically` 两个约束来让某一个控件居中
+ 给 button 添加动作，切换视图 `show assistant editor`
+ 左键单击 button 并按住 ctrl，从 storyboard 拖一条线到代码中，就可以生成对应执行操作的代码(注意要选择 Action 而不是 outlet，outlet 相当于是连接控件的变量)
+ 插入图片需要对宽度和高度进行约束
+ 图片资源添加到 Assets 里


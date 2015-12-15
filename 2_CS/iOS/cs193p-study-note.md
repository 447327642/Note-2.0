# Stanford - Developing iOS 8 Apps with Swift 2015 学习笔记

虽然有点旧，但是还是可以跟着规范的课程学一发

## 1 Logistics, iOS 8 Overview

### What's in iOS

+ Cocoa Touch
	+ Multi-Touch, Alerts, Core Motion, Web View, View Hierarchy, Map Kit, Localization, Image Picker, Controls, Camera
+ Media
	+ Core Audio, JPEG/PNG/TIFF, OpenAL, PDF, Audio Mixing, Quartz(2D), Audio Recording, Core Animation, Video Playback, OpenGL ES
+ Core Services
	+ Collections, Core Location, Address Book, Net Services, Networking, Threading, File Access, Preferences, SQLite, URL Utilities
+ Core OS
	+ OSX kernel, Power Management, Mach 3.0, Keychain Access, BSD, Certificates, Sockets, File System, Security, Bonjour

### Platform Components

+ Tools
	+ Xcode 6 (Latest is 7)
+ Language(s)
	+ Swift, Modern Language
	+ `let value = formatter.numberFromString(display.text!)?.doubleValue`
+ Frameworks
	+ Foundation, Core Data, UIKit, Core Motion, Map Kit
+ Design Strategy
	+ MVC


### Calculator Tutorial

+ Organization Identifier 是你的身份验证，需要保证独立性
+ 尽可能把控件放到蓝线上，也就意味着某种对齐
+ weak 关键字，与 reference counting 相关，具体后面会说
+ 对象都在 heap 中存在
+ 能用 let 尽量用 let，增加可读性
+ option(alt) 点击变量可以查看帮助
+ optional 类型，可能是当前类型的值，或者是 `nil`，例如 `String?`
	+ `nil` 就是 not set
+ 在 optional 类型后面加 `!` 可以取出对应的值，但如果原来值是 `nil`，就会崩溃
+ 所有的变量都必须被初始化


## 2 More Xcode and Swift, MVC

+ 为什么 `UILabel` 之类的 outlet 可以直接声明 `!` 而不需要像之前所说的那样所有变量都必须被初始化呢？因为作为控件变量称为 implicit unwrap，走 storyboard 另外的机制
+ 右键点击一个控件会显示当前和该控件绑定的事件，可以在这里操作进行绑定和解绑定
+ 新建数组 `var operandStack = Array<Double>()`，可以被 infer 的类型最好就不要明写出来
+ 变量的 set get 中有神奇变量 `newValue`
+ command + / 可以快速注释
+ 利用 Swift 本身的特性可以写出非常精简的代码
+ 之后是 MVC 的介绍

## 3 Applying MVC

+ 新建一个 class：File -> New -> File -> Swift File
+ 虽然没有要求类名和文件名一致，但是最好还是一样，这样比较符合惯例
+ 理论上来说不应该在一个 Model 类中 `import UIkit`
+ 可以用快捷语法来生成数组 `[TypeName]()`
+ 可以用快捷语法来生成字典 `[KeyType: ValueType]()`
+ `*`, `+`, `sqrt` 这些内置函数可以直接当参数传
+ 在字典里返回的是 optional 类型
+ 传参时除非传的是 class，不然都是传入一个复制的值
+ 传值的时候参数隐含是 `let` 的，即不可变
+ 函数可以直接返回一个 tuple
+ option(alt) 加点击文件可以把文件放到另一边
+ if let xxx = xxx {} 是通常的处理 optional 类型的方式
+ switch 如果列举了所有情况就不需要 `default: break`
+ `private enum Op: CustomStringConvertible` 由 `Printable` 更名而来

## 4 More Swift and Foundation Frameworks

不是所有的都记录，只挑了一些我觉得和之前学的语言差异比较大的，很多内容在我之前的学习笔记里都有覆盖

### Optional

An Optional is just an `enum`

```swift
enum Optional<T>{
	case None
	case Some(T)
}
```

```swift
let x: String? = nil
// is
let x = Optional<String>.None

let x: String? = "hello"
// is
let x = Optional<String>.Some("hello")

var y = x!
// is
switch x {
	case Some(let value): y = value
	case None: // raise an exception
}
```

### Other Classes

+ `NSObject`
	+ Base class for all objective-C classes. Some advanced features will require you to subclass from `NSObject`
+ `NSNumber`
	+ Generic number-holding class
+ `NSDate`
	+ Used to find out the date and time right now or to store past or future dates
+ `NSData`
	+ A "bag of bits"

### Data Structures

Three Types: Classes, Structures, Enumerations

+ Similarities
	+ Declasration syntax
	+ Properties and Functions
	+ Initializers
+ Differences
	+ Inheritance (class only)
	+ Introspection and casting (class only)
	+ Value type (struct, enum) vs. Reference type (class) 


### Value vs. Reference

+ Value (`struct` and `enum`)
	+ **Copied** when passed as an argument to a function
	+ **Copied** when assigned to a different variable
	+ **Immutable** if assigned to a variable with `let`
	+ Remember that function parameters are, by default, constants
	+ You can put the keyword `var` on an parameter, and it will be mutable, but it's still a copy
	+ You must note any `func` that can mutate a struct/enum with the keyword `mutating`
+ Reference (`class`)
	+ Stored in the heap and reference counted (automatically)
	+ Constant pointers to a class (`let`) still can mutate by calling methods and changing properites
	+ When passed as an argument, does not make a copy (just passing a pointer to same instance)
+ Choosing which to use?
	+ Usually you will choose `class` over `struct`. `struct` tends to be more for fundamental types. Use of `enum` is situational (any time you have a type of data with discret values)

### AnyObject

+ Special "Type" (actually it's a Protocol)
	+ Used primarily for compatibility with exsiting Objective-C-based APIs
+ Where will you see it?
	+ As properties (eigher singularly or as an array of them), e.g. ...
	+ `var destinationViewController: AnyObject`
	+ `var toolbarItems: [AnyObject]`
+ How do we use `AnyObject`
	+ We don't usually use it directly
	+ Instead, we convert it to another, known type 
+ How do we convert it?
	+ We need to create a new vairable which is of a known object type
+ Casting AnyObject
	+ We "force" an `AnyObject` to be something else by "casting" it using the `as` keyword 

### 总结

语言的细节还有很多，需要在实践中不断熟悉，这里不需要过度在意细节，动手最重要


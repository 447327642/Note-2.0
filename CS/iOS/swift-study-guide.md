# Swift 学习指南

主要是参考苹果官方的教程，加上自己的一点私货，作为笔记，还是重在要点和思想，具体的细节参阅官方手册。

+ 使用 ARC(Automatic Reference Counting) 来简化管理内存
+ 采用 Objective-C 的命名参数以及动态对象模型
+ 支持代码预览

## Swift 概览

这一章主要是通过一些实际的代码让大家对 Swift 有一个初步的感觉，具体的语法以及技术细节会在之后的章节中详细介绍。

### 简单值

+ 使用 `let` 来声明常量
+ 使用 `var` 来声明变量
+ 大部分情况下不用明确声明类型
+ 显式声明的话，用冒号分割 `let explicitDouble: Double = 70`
+ 值永远不会被隐式转换成其他类型，需要时请显式转换
+ 一个简单的值转字符串的方法是把值写到括号中，并且在括号前加一个反斜杠，括号中可以是一个表达式
+ 使用方括号来创建数组和字典，用下标或者 key 来访问元素，最后一个元素后面允许有逗号
+ 创建空数组或者字典要用初始化语法

```swift
// variable and constant value
var myVariable = 42
myVariable = 50
let myConstant = 33

// explicit declare
let implicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70

// cast type
let label = "The width is"
let width = 94
let widthLabel = label + String(width)

// easy cast from value to string
let apples = 3
let appleSummary = "I have \(apples) apples."  

// array
var shoppingList = ["catfish", "water", "tulips"]
shoppingList[1] = "bottle of water"

// dict
var occupations = ["Da": "Captain", "Wang": "Warrior",]
occupations["WDX"] = "Hero"

// init empty array / dict
let emptyArray = [String]()
let emptyDict = [String: Float]()
```

### 控制流

+ 条件操作 `if` 和 `switch`
+ 循环操作 `for-in`, `for`, `while` 和 `repeat-while`
+ 包裹条件和循环变量的括号可以省略，但是语句体必须要大括号

```swift
// for loop with if-else statment
let Scores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in Scores{
	if score > 50 {
		teamScore += 3
	} else {
		teamScore += 1
	}
}
print(teamScore)
```

可以使用 `if` 和 `let` 来处理值缺失的情况。这些值可由可选值来代表。一个可选的值是一个具体的值或者是 `nil` 以表示值缺失。在类型后面加一个问号来标记这个变量的值是可选的。在下面的例子中，如果变量的可选值是 `nil`，条件会被判断为 `false`，大括号中的代码会被跳过

```swift
var optionalString: String? = "Hello"
print(optionalString == nil)

var optionalName: String? = "Da Wang"
var greeting = "Hello!"
if let name = optionalName {
	greeting = "Hello, \(name)"
}
```

另一种方法是通过 `??` 操作符来提供一个默认值。如果可选值缺失，用默认值代替

```swift
let nickName: String? = nil
let fullName: String = "Da Wang"
let greeting = "Hi \(nickName ?? fullName)"
```

switch 支持任意类型的数据以及各种比较操作，而且不需要写 break，执行完子句会自动退出

```swift
let vegetable = "cucumber"
swtich vegetable{
	case "carrot":
		print("Oh Carrot!")
	case "cucumber":
		print("Oh Cucumber!")
	case let x where x.hasSuffix("pepper"):
		print("Oh Special One!")
	default:
		print("Nothing happened")
}
```

可以使用 `for-in` 来遍历字典

```swift
let interestingNumbers = [
	"Prime": [2, 3, 5, 7, 11, 13],
	"Fibonacci": [1, 1, 2, 3, 5, 8],
	"Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (kind, numbers) in interestingNumbers{
	for number in numbers{
		if number > largest {
			largest = number
		}
	}
}
print(largest)
```

使用 `while` 来重复运行一段代码直到不满足条件，循环条件也可以在末尾，保证至少循环一次

```swift
// while loop
var n = 2
while n < 100 {
	n = n * 2
}
print(n)

// repeat while loop
var m = 2
repeat {
	m = m * 2
} while m < 100
print(m)
```

也可以在循环中使用 `..<` 来表示范围，注意 `..<` 创建的范围不包含上界，如果要包含的话，使用 `...`

```swift
var forLoop = 0
for i in 0..<4 {
	forLoop += i
}
print(forLoop)

forLoop = 0
for i in 0...4 {
	forLoop += i
}
print(forLoop)
```

### 函数和闭包

使用 `func` 来声明一个函数，使用名字和参数来调用函数。使用 `->` 来指定函数返回值的类型

```swift
func greet(name: String, day: String) -> String {
	return "Hello \(name), today is \(day)"
}

greet("Da", day: "Friday")
```

使用元组来让一个函数返回多个值。该元组的元素可以用名称或数字来表示

```swift
func calStats(scores: [Int]) -> (min: Int, max: Int, sum: Int){
	var min = scores[0]
	var max = scores[0]
	var sum = 0
	for score in scores{
		if score > max {
			max = score
		} else if score < min {
			min = score
		}
		sum += score
	}
	return (min, max, sum)
}

let stats = calStats([5, 3, 100, 2, 9])
print(stats.sum)
print(stats.2)
```



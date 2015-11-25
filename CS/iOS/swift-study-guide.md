# Swift 2.0 学习指南

主要是参考苹果官方的教程，加上自己的一点私货，作为笔记，还是重在要点和思想，具体的细节参阅官方手册。

+ 使用 ARC(Automatic Reference Counting) 来简化管理内存
+ 采用 Objective-C 的命名参数以及动态对象模型
+ 支持代码预览

## Swift 2 概览

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

函数可以带可变个数的参数，这些参数在函数内表现为数组的形式

```swift
func sumOf(numbers: Int...) -> Int {
	var sum = 0
	for number in numbers {
		sum += number
	}
	return sum
}

sumOf()
sumOf(42, 583, 12)
```

函数可以嵌套。被嵌套的函数可以访问外侧函数的变量，可以用来重构一个太长或者太复杂的函数

```swift
func returnFifteen() -> Int {
	var y = 10
	func add() {
		y += 5
	}
	add()
	return y
}

returnFiftee()
```

函数是第一等类型，也就是说，函数可以作为另一个函数的返回值

```swift
func makeIncrementer() -> (Int -> Int){
	func addOne(number: Int) -> Int {
		return 1 + number
	}
	return addOne
}

var increment = makeIncrementer()
increment(7)
```

函数也可以当做参数传入另一个函数

```swift
func hasAnyMatches(list: [Int], condition: Int -> Bool) -> Bool {
	for item in list {
		if condition(item) {
			return true
		}
	}
	return false
}

func lessThenTen(number: Int) -> Bool {
	return number < 10
}

var numbers = [20, 19, 7, 12]
hasAnyMatches(numbers, condition: lessThanTen)
```

函数实际上是一种特殊的闭包：它是一段能之后被调取的代码。比保重的代码能访问闭包所建作用域中能得到的变量和函数，即使闭包是在一个不同的作用域被执行的

```swift
numbers.map({(number: Int) -> Int in let result = 3 * number return result})
```

如果一个闭包的类型已知，，可以忽略参数的类型和返回值。单个语句闭包会把它语句的值当作结果返回

```swift
let mappedNumbers = numbers.map({number in 3 * number })
print(mappedNumbers)
```

也可以通过参数位置而不是参数名字来引用参数。当一个闭包作为最后一个参数传给一个函数的时候，它可以直接跟在括号后面。当一个闭包是传给函数的唯一参数时，可以完全忽略括号

```swift
let sortedNumbers = numbers.sort { $0 > $1}
print(sortedNumbers)
```

### 对象和类

使用 `class` 和类名来创建一个类，其他的语法和前面说的一致。

+ 使用 `init` 来初始化一个构造器
+ 使用 `deinit` 来创建一个析构函数

```swift
class Shape {
	var numberOfSides: Int = 0
	var name: String
	
	init(name: String){
		self.name = name
	}
	
	func simpleDescription() -> String {
		return "A shape with \(numberOfSides) sides."
	}
}
```

要创建一个类的实例，在类名后面加上括号。使用点语法来访问实例的属性和方法

```swift
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
```

子类的定义方法实在它们的类名后面加上父类的名字，用冒号分割。如果要重写父类的方法，需要用 override 标记，否则会报错，编译器同样会检测 override 标记的方法是否确实在父类中

```swift
class Square: Shape {
	var sideLength: Double
	
	init(sideLength: Double, name: String) {
		self.sideLength = sideLength
		super.init(name: name)
		numberOfSides = 4
	}
	
	func area() -> Double {
		return sideLength * sideLength
	}
	
	override func simpleDescription() -> String {
		return "A square with sides of length \(sideLength)."
	}
	
	let test = Square(sideLength: 5.2, name: "my test square")
	test.area()
	test.simpleDescription()
}
```

除了储存简单的属性之外，属性可以有 `getter` 和 `setter`

```swift
class EquilateralTriangle: Shape {
	var sideLength: Double = 0.0
	
	init(sideLength: Double, name: String){
		self.sideLength = sideLength
		super.init(name: name)
		numberOfSides = 3
	}
	
	var perimeter: Double {
		get {
			return 3.0 * sideLength
		}
		set {
			sideLength = newValue / 3.0
		}
	}
	
	override func simpleDescription() -> String {
		return "An equilateral triangle with sides of length \(sideLength)."
	}
}

var triangle = EquilateralTriangle(sideLength: 3.1, name: "a tragnle")
print(triangle.perimeter)
triangle.perimeter = 9.9
print(triangle.sideLength)
```

如果不需要计算属性，但是仍然需要在设置一个新值之前或者之后运行代码，使用 `willSet` 和 `didSet`

### 枚举和结构体

使用 `enum` 来创建一个枚举，枚举也可以包含方法

```swift
enum Rank: Int{
	case Ace = 1
	case Two, Three, FOur, Five, Six, Seven, Eight, Nine, Ten
	case Jack, Queen, King
	func simpleDescription() -> String {
		switch self{
			case .Ace:
				return "ace"
			case .Jack:
				return "jack"
			case .Queen:
				return "queen"
			case .King:
				return "king"
			default:
				return String(self.rawValue)
		}
	}
}

let ace = Rank.Ace
let aceRawValue = ace.rawValue
```

枚举的成员值是实际值，并不是原始值的另一种表达方法。实际上，以防原始值没有意义，我们不需要设置。

使用 `struct` 来创建一个结构体。结构体和类最大的区别是结构体是传值，类是传引用。

```swift
struct Card {
	var rank: Rank
	func simpleDescription() -> String {
		return "The \(rank.simpleDescription())"
	}
}
```

### 协议和扩展

使用 `protocol` 来声明一个协议，类、枚举和结构体都可以实现协议，在结构体中 `mutating` 关键字用来标记一个会修改结构体的方法

```swift
protocol ExampleProtocal {
	var simpleDescription: String { get }
	mutating func adjust()
}

// class
class simpleClass: ExampleProtocol {
	var simpleDescription: String = "A very simple class."
	var anotherProperty: Int = 15213
	func adjust() {
		simpleDescription += "  Now 100% adjusted"
	}
}

// struct
struct Simple Structure: ExampleProtocol {
	var simpleDescription: String = "A simple strucure"
	mutating func adjust() {
		simpleDescription += " (adjusted)"
	}
}
```

使用 extension 来为现有的类型添加功能

```swift
extension Int: ExampleProtocol {
	var simpleDescription: String {
		return "The number \(self)"
	}
	mutating func adjust() {
		self += 42
	}
}

print (7.simpleDescription)
```

### 泛型

在尖括号里写一个名字来创建一个泛型函数或者类型

```swift
func repeatItem<Item>(item: Item, numberOfTimes: Int) -> [Item]{
	var result = [Item]()
	for _ in 0..<numberOfTimes{
		result.append(item)
	}
	return result
}

repeatItem("knock", numberOfTimes: 4)
```

在类型名后面使用 `where` 来指定对类型的需求，比如，限定类型实现某一个协议，限定两个类型是相同的，或者限定某个类必须有一个特定的父类。

```swift
func anyCommonElements <T: SequenceType, U: SequenceType where T.Generator.Element: Equatable, T.Generator.Element == U.Generator.Element> (lhs: T, _ rhs: U) -> Bool){
	...
}
```

## Swift 2 基础

比较简单的部分会用要点的形式列出来，比较复杂和语言本身特性的，会单独提出来介绍

+ 常量与变量
	+ 用 `let` 声明常量
	+ 用 `var` 声明变量
	+ 一行可以声明多个，用逗号隔开：`var x = 0.0, y = 0.0, z = 0.0`
+ 类型标注(冒号和空格加上类型名称)
	+ `var welcomeMessage: String`
	+ 一行可以声明多个：`var red, green, blue: Double`
+ 输出常量和变量
	+ 函数原型：`print(_:separator:terminator:)`
	+ 通常来说 `separator` 和 `terminator` 有默认值可以忽略，如果想要自定义，可以这样：`print(someValue, terminator: "")` 这样就不会默认换行了
+ 字符串插值 string interpolation
	+ 用反斜杠加一对括号
	+ `print("The value is \(someValue)")`
+ 注释：`//` 和 `/* */`
+ 分号不强制要求，但是为了美观还是加上，或者如果一行写两个语句，就一定要分号
+ 整数：Swift 提供了 8, 16, 32, 64 的有符号和无符号整数类型
	+ 如: UInt8, Int32 等
	+ 整数范围: UInt8.min, UInt8.max，其他的类型类似
	+ 32 位平台上，Int 和 Int32 长度相同
	+ 64 位平台上，Int 和 Int64 长度相同
	+ 统一使用 Int 可以提高代码的可复用性，避免不同类型数字之间的转换，并且匹配数字的类型推断
+ 浮点数
	+ Double 64 位浮点数
	+ Float 32 位浮点数
+ Swift 是一个类型安全的语言
+ Swift 也会尽可能通过类型推断来选择合适的类型
+ 数值型字面量
	+ 十进制数：没有前缀
	+ 二进制数：`0b`
	+ 八进制数：`0o`
	+ 十六进制数：`0x`
+ 类型转换
	+ 整数和浮点数转换必须显式指定类型
+ 类型别名
	+ 用 `typealias` 关键字来定义，如 `typealias AudioSample = UInt16`
+ 布尔值：true 和 false
+ 元组
	+ 圆括号包起来的变量：`http404Error = (404, "Not Found")`
	+ 分解元素内容 `let (statusCode, Message) = http404Error`
	+ 如果只需要用一部分，忽略的部分用下划线表示 `let (justCode, _) = http404Error`
	+ 还可以通过下标来访问元组的中单个元素，下标从零开始：`http404Error.0`
	+ 可以在定义元组的时候给元素命名，这样就可以通过名字来获取对应的值：`let http200Status = (statusCode: 200, description: "OK")`
	+ 元组在临时组织值的时候很有用，但是并不适合创建复杂的数据结构
+ 运算符
	+ 基本的就不提了，和其他语言差不多，这里就记录一些要点
	+ `++` 前置的时候，先自增再返回
	+ `++` 后置的时候，先返回再自增
	+ `===` 恒等，`!==` 不恒等
	+ 三目运算符 `(condition ? statement1 : statement2)`
		+ 应该避免在一个组合语句中使用多个三目运算符
	+ 空合运算 (Nil Coalescing Operator)
		+ `(a ?? b)` 将对可选类型 a 进行空判断，如果 a 包含一个值就进行解封，否则就返回一个默认值 b。
		+ 相当于下列代码的简短表示: `a != nil ? a! : b`
	+ 区间运算符
		+ 闭区间运算符 ( a...b )，包括 a 也包括 b
		+ 半开区间运算符 ( a...<b )，包括 a 不包括 b
	+ 逻辑运算
		+ `!a`, `a&&b`, `a||b` 
+ 字符串是值类型 Strings are value types，也就是说操作的时候会创建一个新的副本
	+ 连接字符串和字符可以用 `+` 号，创建一个新的字符串
	+ 字符串插值(String Interpolation)，如 `let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"`
	+ Swift 的字符串完全兼容 Unicode
	+ 计算字符数量，用字符串 characters 属性的 count 属性
	+ `let string = "Hello World" ; print(string.characters.count)`
	+ 通过字符串索引 `startIndex`, `endIndex`, `startIndex.predecessor()`, `startIndex.succeessor()` 来访问，或者也可以通过下标访问
	+ 调用 `insert(_:atIndex:)` 方法可以在一个字符串的指定索引插入一个字符。
		+ `var welcome = "hello"; welcome.insert("!", atIndex:welcome.endIndex)`
	+ 删除字符或字符串 `removeAtIndex(_:)`, `removeRange(_:)`
	+ 比较字符串：`==`, `!=`, `hasPrefix(_:) / hasSuffix(_:)`
	+ 字符串 Unicode 表示形式
+ For 循环
	+ `for initialization; condition; increment { ... }`
	+ `for var variable in Collection { ... }`
+ While 循环
	+ `while condition { ... }`
	+ `repeat { ... } while condition`
+ if 和 switch 可以看概览中的代码例子
+ 控制转移语句 Control Transfer Statements
	+ continue
	+ break
	+ fallthrough 在分支中加上这个关键字就会落入到下一个分支中，不会检查匹配条件，类似于 C 语言 switch case 没加 break 的效果
	+ return
	+ throw
	+ 还可以利用标签来明确是 break 出哪个循环，这个在多重循环逻辑中比较有用

### 可选类型

使用可选类型(optionals)来处理值可能缺失的情况，可选类型的值有两种可能：1.有值，等于 x。2.没有值，等于nil。举个例子

```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)
```

`convertedNumber` 被推测为 `Int?` 类型，表示可能包含 Int 值，也可能不包含值。

可以给可选变量赋值位 `nil` 来表示没有值，但 `nil` 不能用于非可选的常量和变量。如果声明一个可选常量或者变量但是没有赋值，会被自动设置为 `nil`。

可以使用 if 语句和 nil 比较来判断一个可选值是否包含值。使用 `==` 或 `!=` 来执行比较。当确定可选类型确实包含值之后，可以在名字后面加一个感叹号来获取值，称为可选值的强制解析(forced unwrapping)。要注意的是使用 `!` 来获取一个不存在的可选值会导致运行时错误。

```swift
if convertedNumber != nil {
	print("convertedNumber has an integer value of \(convertedNumber!).")
}
```

### 可选绑定 optional binding

判断可选类型是否包含值，如果包含就把值赋给一个临时常量或者变量。可选绑定可以用在 if 和 while 语句中，这样就可以同时完成赋值和判断两个任务，如

```swift
if let constantName = someOptional {
	statements
}
```

也可以在 if 语句中包含多个可选绑定，然后用 where 子句做布尔值判断，如

```swift
if let firstNumber = Int("4"), secondeNumber = Int("42") where firstNumber < secondNumber {
	print("\(firstNumber) < \(secondNumber)")
}
// print "4 < 42"
```

### 错误处理 error handling

一个函数可以通过在声明中添加 `throws` 关键词来抛出错误消息。当你的函数能抛出错误消息时，应该在表达式中前置 `try` 关键词

```swift
func makeASandwich() throws {
	// ...
}

do {
	try makeASandwich()
	eatASandwich()
} catch Error.OutOfCleanDishes {
	washDishes()
} catch Error.MissingIngredients(let ingredients){
	byGroceries(ingredients)
}
```

### 断言

在运行时判断一个逻辑条件是否为 `true`。全局 `assert(_:_file:line:)` 函数来写一个断言。

```swift
let age = -3
assert(age >= 0, "A person's age cannot be less than zero")
// 因为 age < 0，所以断言会触发
```

适用情景

+ 整数类型的下标索引被传入一个自定义下标脚本实现，但是下标索引值可能太小或者太大
+ 需要给函数传入一个值，但是非法的值可能导致函数不能正常运行
+ 一个可选值现在是 nil，但是后面的代码运行需要一个非 nil 值。

### 集合类型 (Collection Types)

Swift 语言提供 Arrays, Sets 和 Dictionaries 三种基本集合类型，存储的数据必须明确，不能把不正确的数据类型插入其中。

在我们不需要改变集合大小的时候创建不可变集合是很好的习惯。这样 Swift 编译器可以优化我们创建的集合

#### 数组 Arrays

会被桥接到 Foundation 中的 NSArray 类。应该遵循像 `Array<Element>` 这样的形式，其中 `Element` 是这个数组唯一允许存在的数据类型。也可以用 `[Element]` 这样的简单语法。

```swift
// 创建一个空数组
var someInts = [Int]()

// 如果代码上下文中已经提供了类型信息，可以使用一对空方括号来创建
someInts.append(3)
someInts = []

// 带有默认值的数组
var threeDoubles = [Double](count: 3, repeatedValue: 0.0)

// 两个数组相加创建一个数组
var anotherThreeDoubles = Array(count: 3, repeatedValue: 2.5)
var sixDoubles = threeDoubles + anotherThreeDoubles

// 用字面量构造数组
var shoppingList: [String] = ["Eggs", "Milk"]

// 可以使用数组中的只读属性 count 来获取数组中的数据项数量
print("The shopping list contains \(shoppingList.count) items.")

// 使用布尔值属性 isEmpty 作为检察 count 属性的值是否为 0 的捷径
if shoppingList.isEmpty {
	print("The shoppinglist is empty.")
}

// 使用 append(_:) 方法在数组后面添加数据项
shoppingList.append("Flour")

// 也可以直接用 += 来进行添加
shoppingList += ["Baking Powder"]

// 用下标来改变数据
shoppingList[0] = "Six eggs"

// 或者范围批量替换
shoppingList[4...6] = ["Bananas", "Apples"]

// 插入数据
shoppingList.insert("Maple Syrup", atIndex: 0)

// 删除数据
let mapleSyrup = shoppingList.removeAtIndex(0)
let apples = shoppingList.removeLast()

// 用 for-in 循环来遍历数组
for item in shopppingList {
	print(item)
}

// 用 `enumerate()` 方法来同时获取索引值和数据值
for (index, value) in shoppingList.enumerate(){
	print("Item \(String(index + 1)): \(Value)")
}
```

#### 集合 Sets

每个元素只出现一次，元素顺序不重要，会被桥接到 Foundation 中的 NSSet 类。存在集合中的类型必须可哈希化。集合的语法是 `Set<Element>`，但集合没有等价的简化形式

```swift
// 创建空集合
var letters = Set<Character>()

// 如果上下文提供了类型信息，创建空集合时可以简化
letters.insert("a")
letters = [] // 重新设为空，类型为 Set<Character>

// 用字面量声明集合
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]

// 因为用了字面量，其实类型的声明是可以省略的，上面的一句可以写为
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]

// 用 count 获取元素数量
print ("I have \(favoriteGenres.count) favorite music genres.")

// 用 isEmpty 来看集合是否为空
if favoriteGenres.isEmpty {
	print("As far as music goes, I'm not picky.")
}

// insert(_:) 添加新元素
favoriteGenres.insert("Jazz")

// remove(_:) 删除元素，会返回被删除的值，如果不包含则返回 nil
if let removedGenre = favoriteGenres.remove("Rock"){
	print ("\(removedGenre)? I'm over it.")
}

// contains(_:) 检查是否包含一个特定的值
if favoriteGenres.contains("Funk") {
	print ("I get up on the good foot")
}

// 遍历集合，用 for-in，用 sort() 方法可以返回一个有序集合
for genre in favoriteGenres.sort() {
	print("\(genre)")
}
```

其他的一些集合操作

+ `intersect(_:)` 根据两个集合中都包含的值创建一个新的集合
+ `exclusiveOr(_:)` 在一个集合中但不在另一个集合中的值创建一个新的集合
+ `union(_:)` 两个集合的并集
+ `subtract(_:)` 两个集合的差集
+ `==` 判断集合相等
+ `isSubsetOf(_:)` 判断子集
+ `isSupersetOf(_:)` 判断超集
+ `isStrictSubsetOf(_:)` 判断严格子集
+ `isDisjointWith(_:)` 判断两个集合是否不含有相同的值

#### 字典 Dictionary

键值对，被桥接倒 Foundation 的 NSDictionary，语法为 `Dictionary<Key, Value>`。作为 key 的必须是 Hashable 的，可以用 `[Key: Value]` 这样的快捷形式去创建一个字典

```swift
// 创建空字典
var namesOfIntegers = [Int: String]()

// 如果上下文提供了信息，可以简化
namesOfIntegers[16] = "sixteen"
namesOfIntegers = [:] // 设置为空字典

// 用字面量创建字典
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]

// 获取字典中元素的数量
print("The dictionary of airports contains \(airports.count) items.")

// 检查是否为空
if airports.isEmpty {
	print("The airports dictionary is empty.")
}

// 下标添加新数据，如果已有对应的 key，会覆盖更新
// 用 updateValue(_:forKey:) 方法会返回更新前的值，可选类型，如果没有 key 的话会是 nil
airports["LHR"] = "London"
if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB"){
	print("The Old value for DUB was \(oldValue)")
}

// 用下标给某个 key 赋值为 nil 可以删除这个元素
// 也可以用 removeValueForKey(_:)，方法会返回更新前的值，可选类型，如果没有 key 的话会是 nil
airports["APL"] = nil

// 用 for-in 来遍历字典，会以 (key, value) 元组形式返回
for (airportCode, airportName) in airports {
	print("\(airportCode): \(airportName)")
}

// 也可以单独用 keys 或者 values 属性来遍历，如果需要有序，可以用 sort() 方法
for airportCode in airports.keys {...}
for airportName in airports.values {...}
```

### Switch 语句高级用法

switch 语句会尝试把某个值与若干个 pattern 进行匹配。每个 case 之间不需要用 break

比较常见的用法是

```swift
switch some value to consider {
	case value 1:
		respond to value 1
	case value 2, value 3:
		respond to value 2 or 3
	default:
		otherwise
}
```

#### 区间匹配

因为是匹配一个 pattern，所以也可以利用区间，如下

```swift
let approximateCount = 62
switch approximateCount {
	case 0:
		print("no")
	case 1..<5:
		print("a few")
	case 5..<12:
		print("several")
	default:
		print("many")
}
```

#### 元组

可以使用元组来测试多个值。用下划线来匹配所有可能的值

```swift
let somePoint (1, 1)
switch somePoint {
	case (0, 0):
		print("origin")
	case (_, 0):
		print("x-axis")
	case (0, _):
		print("y-zxis")
	default:
		print("else")
}
```

#### 值绑定 Value Bindings

允许将匹配的值帮动刀一个临时的常量或变量上，就可以在 case 分支里引用

```swift
let anotherPoint = (2, 0)
switch anotherPoint {
	case (let x, 0):
		print("x-axis with value \(x)")
	case (0, let y):
		print("y-axis with value \(y)")
	case let (x, y):
		print("else x:\(x) y:\(y)")
}
```

#### where

case 分支模式可以使用 where 语句来判断额外的条件，例如

```swift
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
	case let (x, y) where x == y:
		print("(\(x), \(y)) is on the line x = y")
	case let (x, y) where x == -y:
		print("(\(x), \(y)) is on the line -x = y")
}
```

### Guard 语句

要求条件必须为真才执行之后的代码，而且一定要带有一个 else 分句，如果条件不为真则执行 else 分句中的代码

```swift
guard let name = person["name"] else {
	print("no name")
}
```


### 检测 API 的可用性

最后一个参数 `*` 是必须的，用于处理未来的潜在平台，例如：

```swift
if #available(iOS 9, OSX 10.10, *){
	//... new api
} else {
	//... old api 
}
```

## Swift 2 进阶

### 函数

#### 定义一个函数

```swift
func sayHello(personName: String) -> String {
	let greeting = "Hello, " + personName + "!"
	return greeting
}

print(sayHello("DaWang"))
```

#### 无参数函数

```swift
func sayHelloWorld() -> String {
	return "Hello, World."
}

print(sayHelloWorld())
```

#### 多参数函数

```swift
func sayHello(personName: String, alreadyGreeted: Bool) -> String {
	if alreadyGreeted {
		// ... 
	} else {
		// ...
	}
}
```

#### 无返回值

就不需要返回箭头和返回类型

```swift
func sayGoodBye(personName: String){
	print("Goodbye, \(personName)!")
}

sayGoodbye("David")
```

#### 多重返回值函数

其实就是返回一个 tuple，名字已经由返回类型指定了，这个返回值也可以是一个可选的元组 `(min: Int, max: Int)?`

```swift
func minMax(array: [Int]) -> (min: Int, max: Int){
	var currentMin = array[0]
	var currentMax = array[0]
	for value in array[1..<array.count] {
		if value < currentMin {
			currentMin = value
		} else if value > currentMax {
			currentMax = value
		}
	}
	return (currentMin, currentMax)
}

let bounds = minMax([8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
```

如果不想为第二个及后续的参数设置外部参数名，用一个下划线来代替一个明确的参数

#### 默认参数值

```swift
func someFunction(parameterWithDefault: Int = 12) {
	// ...
}
```

#### 可变参数

可以接受零个或多个值。但是一个函数最多只能有一个可变参数，并且最好放在最后

```swift
func arithmeticMean(numbers: Double...) -> Double {
	var total: Double = 0
	for number in numbers {
		total += number
	}
	return total / Double(numbers.count)
}

arithmeticMean(1, 2, 3, 4, 5)
```

#### 变量函数参数

函数参数默认是常量，也就是说在函数体中不能更改。但是如果想要传入参数变量的值副本，可以像以下这样。但是对变量参数所进行的修改在函数调用结束后便消失了，并且对于函数体外是不可见的

```swift
func alignRight(var string: String, fotalLength: Int, pad: Character) -> String {
	// ...
}
```

#### 输入输出参数

如果需要在函数中对参数的修改仍然存在，就需要用 `inout` 关键字。只能传递变量给 inout 参数，并且需要在参数名前加 `&`，表示这个值可以被函数修改

```swift
func swapTwoInts(inout a: Int, inout _ b: Int){
	let temporaryA = a
	a = b
	b = temporaryA
}

var someInt = 3
var anotherInt = 107
swapTwoInt(&someInt, &anotherInt)
```

#### 函数类型

也就是把函数当成常量或者变量，比如

```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
print("Result: \(mathFunction(2, 3))")
// 也可以让 Swift 来推断函数类型，如
let anotherMathFunction = addTwoInts
```

函数类型作为参数类型

```swift
func printMathResult(mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
	print("Result: \(mathFunction(a, b))")
}

printMathResult(addTwoInts, 3, 5)
```

函数类型作为返回类型，在返回箭头后写一个完整的函数类型

```swift
func stepForward(input: Int) -> Int {
	return input + 1
}

func stepBackward(input: Int) -> Int {
	return input - 1
}

func chooseStepFunction(backwards: Bool) -> (Int) -> Int {
	return backwards ? stepBackward : stepForward
}

var currentvalue = 3
let moveNearerToZero = chooseStepFunction(currentValue > 0)
```

#### 嵌套函数 nested function

默认情况下，嵌套函数对外界不可见，但是可以被外围函数(enclosing function)调用。一个外围函数也可以返回它的某一个嵌套函数，使得这个函数可以在其他域中被使用

```swift
func chooseStepFunction(backwards: Bool) -> (Int) -> Int {
	func stepForward(input: Int) -> Int {
		return input + 1
	}
	
	func stepBackward(input: Int) -> Int {
		return input - 1
	}
	
	return backwards ? stepBackward : stepForward
}

var currentValue = -4
let moveNearerToZero = chooseStepFunction(currentValue > 0)
while currentValue != 0 {
	print("\(currentValue)...")
	currentValue = moveNearerToZero(currentValue)
}
```

### 闭包 Closure

自包含的函数代码块，可以在代码中被传递和使用。



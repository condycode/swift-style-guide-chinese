# swift-style-guide-chinese
A swift style guide in chinese.

## 正确性

应努力使代码在编译时没有任何的警告，例如使用`#selector`方法选择器而不是字符串

## 命名

命名时应做到见名知意，且必须保证意义一致性。

下面列出的命名规则摘录自苹果的[API design guideline](https://swift.org/documentation/api-design-guidelines/)，是其中一些主要的规则:

- 命名必须清晰准确的描述目标的意义
- 为了表达更加清楚，可以适当放宽简洁性要求
- 使用驼峰命名法
- 对类型（和协议）使用大写，对其他所有类型使用小写
- 包括所有需要的单词，同时省略不必要的单词
- 命名的时候是基于实体的抽象含义，而非基于其类型
- 保证抽象含义表达清楚的基础上，适当增加类型信息
- 力求使用流畅
- 工厂方法以单词 make 开头
- 方法名称中需要包含描述其功能的附加效应的词
  - 动词方法遵循非变异版本的-ed，-ing规则
  - 布尔类型应该像断言一样读取
  - 描述某物的协议应该读作名词
  - 描述功能的协议应该以"able"或"ible"结尾
- 使用专业术语或名词的时候，尽量做到不要"让知道的人笑掉大牙，让不知道的人不知所云"
- 尽量不要使用缩写，特别是那些比较生僻的缩写或者是自己发明的缩写
- 命名时做到有据可依，要有先例最好
- 尽量使用属性和方法，不要命名为没有命名空间的变量或函数
- 缩略语的大小写方针要统一，要么统一大写，要么统一小写
- 具有相似含义的若干方法，当应用场景不同的时候，它们的名称需要附加描述含义的相同的前缀或后缀
- swift中支持基于不同返回值时候的重载，但是需要极力避免这样的重载发生
- 使用文档化的参数命名
- 多参数的方法中，不要把第一个参数放进方法名
- 为闭包或元组的参数提供标签
- 尽量利用参数的默认值，能省很多事

### 不要添加类型名前缀

Swift类型由包含它们的模块自动命名，不应添加类前缀，如`YX`。如果来自不同模块的两个名称发生冲突，可以通过在类型名称前面加上模块名称来消除歧义。

```swift
import SomeModule

let myClass = MyModule.UsefulClass()
```

### 代理

创建自定义委托方法时，代理方法的第一个参数应该是代理源，且没有参数标签。（`UIKit`包含许多这样的例子。）

**推荐**:

```swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

**不推荐**:

```swift
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```

### 使用类型推导

充分利用编译器提供的类型上下文推导机制，写更加短小且清晰易懂的代码。

**推荐**:

```swift
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```

**不推荐**:

```swift
let selector = #selector(ViewController.viewDidLoad)
view.backgroundColor = UIColor.red
let toView = context.view(forKey: UITransitionContextViewKey.to)
let view = UIView(frame: CGRect.zero)
```

### 泛型

泛型类型参数名称应该是有意义的，且是大写字母开头的驼峰命名。 只有当泛型类型参数没有确切意义或角色时，才使用传统的单个大写字母命名方式.

**推荐**:

```swift
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

**不推荐**:

```swift
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

### 使用美式英语

使用美式英语拼写来匹配苹果的API。
**推荐**:

```swift
let color = "red"
```

**不推荐**:

```swift
let colour = "red"
```

## 代码结构

使用扩展将代码组织成逻辑功能块。每个扩展名都应该用一个`//mark:-`注释来进行设置，使代码井然有序。

### 协议一致性

当有不同的protocol实现的情况下，协议的实现应该放到`extension`中，这样可以保证相关功能的代码都位于同一片代码区域中。
**推荐**:

```swift
class MyViewController: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

**不推荐**:

```swift
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

由于编译器不允许您在派生类中重新声明父类的协议，因此并不总是需要使用扩展组。如果派生类是一个终端类，且只重写少量的方法，也不是必须使用扩展。因此使用什么形式实现扩展要开发者根据实际情况判断。

### 无效代码

对于未使用的(或者是废弃的)代码，包括Xcode自动生成的代码或者是占位用的注释等，都应该移除掉。 当然如果是你需要保留的一些注释除外，比如未实现的代码的注释等。

另外就是一些自动生成的代码块，实际并没有做任何工作，只是调用父类的实现，这类代码也应该移除。

**推荐**:

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return Database.contacts.count
}
```

**不推荐**:

```swift
override func didReceiveMemoryWarning() {
  super.didReceiveMemoryWarning()
  // Dispose of any resources that can be recreated.
}

override func numberOfSections(in tableView: UITableView) -> Int {
  // #warning Incomplete implementation, return the number of sections
  return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  // #warning Incomplete implementation, return the number of rows
  return Database.contacts.count
}
```

### 最小化引用原则

保证代码中最小限度地引入外部库。 比如，当可以只引入`Foundation`就能完成工作的时候，就不要引入更大的`UIKit`了。

**推荐**:

```swift
import UIKit
var view: UIView
var deviceModels: [String]
```

**推荐**:

```swift
import Foundation
var deviceModels: [String]
```

**不推荐**:

```swift
import UIKit
import Foundation
var view: UIView
var deviceModels: [String]
```

**不推荐**:

```swift
import UIKit
var deviceModels: [String]
```

## 空格

- 使用空格代替缩紧
- 代码块的起始大括号，应和块开头的代码位于同一行，不要另起一行写
- 利用 Xcode 自带的格式化快捷键`control + I`来格式化代码

**推荐**:

```swift
if user.isHappy {
  // Do something
} else {
  // Do something else
}
```

**不推荐**:

```swift
if user.isHappy
{
  // Do something
}
else {
  // Do something else
}
```

- 方法与方法之间应该用一个空行隔开
- 方法内部代码应该使用空行来对不同逻辑功能的代码块进行分隔，但不可过多，过多的分隔意味着这个方法可以被再次重构分解为多个方法
- 冒号的使用原则: 冒号始终靠近左边，并且和左边的词之间没有空格，而右边则需要一个空格:

**推荐**:

```swift
class TestDatabase: Database {
  var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
}
```

**不推荐**:

```swift
class TestDatabase : Database {
  var data :[String:CGFloat] = ["A" : 1.2, "B":3.2]
}
```

- 每行的最大长度应该控制在一个规定值范围内
- 避免在行尾出现空白符
- 每个文件的末尾添加一个空行

## 注释

当需要时使用注释来解释特定代码，注释必须保持最新。
避免在代码中嵌入块注释，因为好的代码是不需要注释的。**如果是用于生成文档的文档注释，则需要详尽**。

## 类和结构

### self的使用

除非必须使用`self`，否则应该省略。

### 计算属性

当写只读属性的`getter`时，应省略`get`块:

**推荐**:

```swift
var diameter: Double {
  return radius * 2
}
```

**不推荐**:

```swift
var diameter: Double {
  get {
    return radius * 2
  }
}
```

## 函数声明

如果函数名长度较短，将左大括号放在行尾即可：

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

对于具有长签名的函数，将每个参数放在新行上，并在后续行上添加额外的缩进：

```swift
func reticulateSplines(
  spline: [Double],
  adjustmentFactor: Double,
  translateConstant: Int, comment: String
) -> Bool {
  // reticulate code goes here
}
```

## 闭包表达式

只有当闭包是方法的最后一个参数时，才考虑写尾随闭包，尽量给闭包中的参数赋予一个具体名称来表达其含义。

**推荐**:

```swift
UIView.animate(withDuration: 1.0) {
  self.myView.alpha = 0
}

UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}, completion: { finished in
  self.myView.removeFromSuperview()
})
```

**不推荐**:

```swift
UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
})

UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}) { f in
  self.myView.removeFromSuperview()
}
```

当上下文意思清晰的情况下，可以省略单行闭包中的return:

```swift
attendeeList.sort { a, b in
  a > b
}
```

另外就是闭包的一个格式问题，由开发者按实际情况决定:

```swift
let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = numbers
  .map {$0 * 2}
  .filter {$0 > 50}
  .map {$0 + 10}
```

## 类型

应尽可能使用 swift 中的原生类型。只有当对性能改善更加明显的情况下，才考虑使用 OC 中的类型。

**推荐**:

```swift
let width = 120.0                                    // Double
let widthString = "\(width)"                         // String
```

**Less Preferred**:

```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**不推荐**:

```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

### 常量

如果一个量的值不会发生变化的情况下，应使用`let`声明这个量，而不是`var`。

应尽量将所有使用到的常量按照功能不同放置在不同名字空间中(如enum)

**推荐**:

```swift
enum Math {
  static let e = 2.718281828459045235360287
  static let root2 = 1.41421356237309504880168872
}

let hypotenuse = side * Math.root2
```

**不推荐**:

```swift
let e = 2.718281828459045235360287  // pollutes global namespace
let root2 = 1.41421356237309504880168872

let hypotenuse = side * root2 // what is root2?
```

### 类方法和类属性

类方法和类属性的作用类似于全局变量，不过由于它们处于某些名字空间下，更加合理安全。

### 可选类型

当方法或函数的返回值可能不存在时，则将返回值类型声明为可选类型。

只有当确定可选值在当前时刻之前的确会存在的情况下，才考虑用隐式可选类型`!`。

当值只会被访问一次的情况下，使用可选链来获取这个值:

```swift
textContainer?.textLabel?.setNeedsDisplay()
```

当值会被多次用到时，则使用可选绑定:

```swift
if let textContainer = textContainer {
  // do many things with textContainer
}
```

由于在类型中已经隐式说明了变量是可选类型的，故在命名时尽量不要使用`optionalString`，`maybeView`这样形式的名字。

**推荐**:

```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, let volume = volume {
  // do something with unwrapped subview and volume
}

// another example
UIView.animate(withDuration: 2.0) { [weak self] in
  guard let self = self else { return }
  self.alpha = 1.0
}
```

**不推荐**:

```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}

// another example
UIView.animate(withDuration: 2.0) { [weak self] in
  guard let strongSelf = self else { return }
  strongSelf.alpha = 1.0
}
```

### 懒加载

实现的时候可以使用一个直接执行的闭包`{ }()`或一个私有工厂方法进行对象创建，`swift`会在使用到这个对象的时候才执行那个闭包或者调用私有工厂方法。

```swift
lazy var locationManager = makeLocationManager()

private func makeLocationManager() -> CLLocationManager {
  let manager = CLLocationManager()
  manager.desiredAccuracy = kCLLocationAccuracyBest
  manager.delegate = self
  manager.requestAlwaysAuthorization()
  return manager
}
```

- 利用闭包实现懒加载时，不需要`[unowned self]`，因为这里不会出现引用循环
- 上述代码中的`locationManager`首次创建的时候会请求用户权限，所以这里使用懒加载来加载它是非常合适的

### 类型推导

尽量利用类型推导来写更加短小简洁的代码。 只有当类型推导的默认类型不满足要求时，才考虑显式指定类型。 另外就是对于短小的数组，尽量直接使用数组的字面量来表达数组。

**推荐**:

```swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = ["Mic", "Sam", "Christine"]
let maximumWidth: CGFloat = 106.5
```

**不推荐**:

```swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
var names = [String]()
```

#### 空数组或字典的类型标识

如果新建空数组或字典供之后使用，则应该使用类型标识。

**推荐**:

```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```

**不推荐**:

```swift
var names = [String]()
var lookup = [String: Int]()
```

### 语法糖

优先使用`swift`语法糖来快速声明类型。

**推荐**:

```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**不推荐**:

```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

## 函数和方法之间的选择

只有当无法将这个功能归入某个类型或子名字空间内时，才考虑使用函数。其余的情况都应该将这些功能作为某个类型的类方法或对象方法。

**Preferred**：

```swift
let sorted = items.mergeSorted()  // easily discoverable
rocket.launch()  // acts on the model
```

**Not Preferred**：

```swift
let sorted = mergeSort(items)  // hard to discover
launch(&rocket)
```

## 内存管理

在可能出现循环引用的情况下，将变量类型标记为`weak`或`unowned`。另外，也可以使用值类型`struct`或`enum`来避免引用循环。

当在闭包中需要使用`self`，且会形成引用循环时，考虑使用`weak`标记`self`，尽量不要使用`unowned`，除非`self`在执行这个闭包时很明显是还存在的情况。

**Preferred**：

```swift
resource.request().onComplete { [weak self] response in
  guard let self = self else {
    return
  }
  let model = self.updateModel(response)
  self.updateUI(model)
}
```

**Not Preferred**：

```swift
// might crash if self is released before response returns
resource.request().onComplete { [unowned self] response in
  let model = self.updateModel(response)
  self.updateUI(model)
}
```

**Not Preferred**：

```swift
// deallocate could happen between updating the model and updating UI
resource.request().onComplete { [weak self] response in
  let model = self?.updateModel(response)
  self?.updateUI(model)
}
```

## 访问控制

合理使用`private`和`fileprivate`，这样代码会更加清晰，且提高了封装的质量。 一般来说，能用`private`的地方尽量用它，除非编译器提示使用`fileprivate`。

除了有`static`或`@IBAction`等其他属性，否则访问控制修饰符应该放在最开始的位置。

**推荐**:

```swift
private let message = "Great Scott!"

class TimeMachine {  
  private dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```

**不推荐**:

```swift
fileprivate let message = "Great Scott!"

class TimeMachine {  
  lazy dynamic private var fluxCapacitor = FluxCapacitor()
}
```

## 流程控制

能够使用`for-in`的时候就不要用`while`方式的循环。

**推荐**:

```swift
for _ in 0..<3 {
  print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
  print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
  print(index)
}

for index in (0...3).reversed() {
  print(index)
}
```

**不推荐**:

```swift
var i = 0
while i < 3 {
  print("Hello three times")
  i += 1
}


var i = 0
while i < attendeeList.count {
  let person = attendeeList[i]
  print("\(person) is at position #\(i)")
  i += 1
}
```

### 三元运算符

三元运算符只能在提高清晰度或代码整洁时使用。出现多个条件时使用`if`语句更容易理解。

**推荐**:

```swift
let value = 5
result = value != 0 ? x : y

let isHorizontal = true
result = isHorizontal ? x : y
```

**不推荐**:

```swift
result = a > b ? x = c > d ? c : d : y
```

## 最佳侧原则

当编写条件判断代码的时候，整个代码对齐的左侧称为"最佳侧"，意思是如果代码越远离左侧，表示嵌套的层次越深，代码的质量也就越低。 当有多层嵌套的时候，尽量使用`guard`来简化嵌套。

**推荐**:

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  guard let context = context else {
    throw FFTError.noContext
  }
  guard let inputData = inputData else {
    throw FFTError.noInputData
  }

  // use context and input to compute the frequencies
  return frequencies
}
```

**不推荐**:

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  if let context = context {
    if let inputData = inputData {
      // use context and input to compute the frequencies

      return frequencies
    } else {
      throw FFTError.noInputData
    }
  } else {
    throw FFTError.noContext
  }
}
```

另外就是 guard 后面可以写多条可选绑定语句，这样写也可以简化很多嵌套代码。

**推荐**:

```swift
guard
  let number1 = number1,
  let number2 = number2,
  let number3 = number3
  else {
    fatalError("impossible")
}
// do something with numbers
```

**不推荐**:

```swift
if let number1 = number1 {
  if let number2 = number2 {
    if let number3 = number3 {
      // do something with numbers
    } else {
      fatalError("impossible")
    }
  } else {
    fatalError("impossible")
  }
} else {
  fatalError("impossible")
}
```

## 分号

swift 不强制使用分号，且一般应该不去写表达式末尾的分号。 只有当想在一行写多条语句的时候，语句间才用分号分隔。

但请记住: 不应将多条语句写在一行上，不应在表达式末尾添加分号。

**推荐**:

```swift
let swift = "not a scripting language"
```

**不推荐**:

```swift
let swift = "not a scripting language";
```

## 括号

尽量避免在条件语句中使用括号。 只有当语句比较复杂的时候，才考虑用括号来组织。

**推荐**:

```swift
if name == "Hello" {
  print("World")
}

let playerMark = (player == current ? "X" : "O")

```

**不推荐**:

```swift
if (name == "Hello") {
  print("World")
}
```

## 多行字符串文字

在构建长字符串文字时，建议使用多行字符串文字语法。

**推荐**:

```swift
let message = """
  You cannot charge the flux \
  capacitor with a 9V battery.
  You must use a super-charger \
  which costs 10 credits. You currently \
  have \(credits) credits available.
  """
```

**不推荐**:

```swift
let message = """You cannot charge the flux \
  capacitor with a 9V battery.
  You must use a super-charger \
  which costs 10 credits. You currently \
  have \(credits) credits available.
  """
```

**不推荐**:

```swift
let message = "You cannot charge the flux " +
  "capacitor with a 9V battery.\n" +
  "You must use a super-charger " +
  "which costs 10 credits. You currently " +
  "have \(credits) credits available."
```

## 不要使用Emoji

不要使用Emoji当作变量名、方法名、类名等，以免造成不必要的麻烦。

Reference resources [raywenderlich/swift-style-guide](https://github.com/raywenderlich/swift-style-guide)

# swift基础语法理解
## 基础知识

* 可选项绑定

    用来判断一个可选项是否有值，如果可选项有值就将其赋值给一个常量或者变量
i
* 隐式展开可选项

    明确知道可选项是否有值（根据代码逻辑确认一定有值，声明的时候并不知道是否有值），可以默认该可选项有值，

## 基础运算符

* `=`没有任何返回值，判断两个值是否相等必须用`==`

* '==='和`!==`判断两个引用是否针对同一实例变量

* `??` optional ?? defaultValue （可选值optional是否有值，如果有就返回可选值的值，如果可选值没有值就返回defaultValue，defaultValue必须和可选值的类型一致）

* `a...b`表示a到b的区间，包含a和b，可以没有a或者b表示单侧区间,`a..<b`表示a到b的区间，不包括b，没有b表示单侧区间

## 字符串和字符 (String and Character)

* 使用`""" """`来表示多行字符串，文本缩进以结尾`"""`为准，如下图

    ![](/Users/maomaoshou/Documents/notes/assets/characterExample.png)

    灰色空格被保留，白色空格被忽略

* 字符串是值类型

## 控制流

* For-In

可以遍历Dictionary,Array,Set,区间

* While

    * while

    * repeat-while

* Switch

    * 匹配到第一个switch情况执行完毕后退出,如果需要执行执行所有条件，使用`fallthrough`

    * 支持区间匹配

    * 支持元组匹配

    * 支持值绑定

    * 可以使用`where`来检查额外情况

    * 复合情况，即支持每个case有多个模式匹配

* 控制转移语句

    * continue 

    >停止当前循环（当前大循环）开始下次循环

    * break

        * 循环语句中的break

         > 结束整个循环体，转移控制到(})后的第一行代码上

        * switch语句里的break

        > break导致switch立即结束它的执行，转移到(})后的第一行代码上

    * fallthrough

    >使某个switch情况末尾贯穿到下个情况(如果想继续执行后面的情况，必须接着在后面加fallthrough)

    * return

    * throw

* 提前退出

    `guard`语句，类似if语句，guard语句总是有一个else分局，当条件不为真时执行else语句

## functions（函数）

* 没有返回值可以省略`->`及其后面的void，形如

        func greet(person: String) {
            //do something
        }

* 忽略函数的返回值可以使用下面形式
    ```
    let _ = self.someFunctionWithAReturnValue()
    ```

* 返回多个值，形如

        func minMax(array: [Int]) -> (min: Int, max: Int) {
            ///do something
        }

    可以使用点语法逐个获取返回值

        let bounds = minMax(array: [1,2,3,3])
        print("\(bounds.min),\(bounds.max)")

* 可以返回可选值，形如

        func minMax(array: [Int]) -> (min: Int, max: Int)? {
            ///do something,may return nil
        }

    notes:注意`(Int, Int)?`和`(Int?, Int?)`的区别

* **Argument Labels**和**Parameter Names**(实参和形参)

        func someFunction(firstParameterName: Int, secondParameterName: Int) {
            // In the function body, firstParameterName and secondParameterName
            // refer to the argument values for the first and second parameters.
        }
        someFunction(firstParameterName: 1, secondParameterName: 2)

        可以指定argumentLabels

        func someFunction(argumentLabel parameterName: Int) {
            // In the function body, parameterName refers to the argument value
            // for that parameter.
        }

        someFunction(argumentLabel)

        可以删除argument labels

        func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
            //In the function body, firstParameterName and secondParameterName
            //refer to the argument values for the first and second parameters.
        }

        someFunction(1, secondParameterName: 2)

* 默认参数值

        func someFunction(parameterWithoutDefault: Int, parameterWithDefault: 12) {
            ///do something
        }

        someFunction(parameterWithoutDefault:3, parameterWithDefault: 6)
        someFunction(parameterWithoutDefault: 4)

* 可变参数

        func arithmeticMean(_ numbers: Double...) -> Double {
            ///do something
        }
        arithmeticMean(1,2,3,5)
        arithmeticMean(3,8.77,3.4)

    **一个方法最多有一个可变参数**

* In-Out参数

   方法的参数默认为常量不能修改， In-Out参数为指针参数，可以在方法内修改参数值，在外部同样生效,调用时需要在In-Out参数名前加上`&`

**可变参数不能被标记为In-Out参数，In-Out参数不能有默认值**

* Function Types（函数类型）

        func addTwoInts(_ a: Int, _ b: Int) -> Int {

        }
        
    上述方法的函数类型为:`(Int, Int) -> Int`

    * 使用函数类型:

        var mathFunction: (Int, Int) -> Int = addTwoInts

    * 函数类型作为参数类型

            func printMathResult(_ mathFunction: (Int, Int) -> Int ,_ a: Int, _ b: Int) {

            }

            printMatnResult:(addTwoInts, 3, 5)

    * 函数类型作为方法返回值

* Nested Functions（内嵌函数）

    声明在方法内部的方法，称为nested functions, 内嵌函数只能被外围函数(enclosing function)调用，外围函数可以返回它的内嵌函数以供外界调用

        func chooseStepFunction(backward: Bool) -> (Int) -> Int {  
            func stepForward(input: Int) -> Int { return input + 1 }  
            func stepBackward(input: Int) -> Int { return input - 1 }
            return backward ? stepBackward : stepForward
        }
        var currentValue = -4
        let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
        // moveNearerToZero now refers to the nested stepForward() function
        while currentValue != 0 {
        print("\(currentValue)... ")
        currentValue = moveNearerToZero(currentValue)
        }
        print("zero!")
        // -4...
        // -3...
        // -2...
        // -1...
        // zero!

## Closures（闭包）

* 闭包语法
```
{ (parameters) -> return type in
    statements
} 
```

* 闭包参数不能设定默认值

* 尾随闭包，调用时省略参数
```
func someFunctionThatTakeAClosure:(closure: () -> Void) {

}

不使用尾随闭包调用
someFunctionThatTakeAClosure(closure: {
    //闭包主体部分
})
使用尾随闭包调用
someFunctionThatTakeAClosure() {
    //闭包主体部分
}
```
* 值捕获，指闭包内部可以捕获外部函数变量或者常量。在外部函数调用结束后依然可以使用捕获到的值
* 逃逸闭包即闭包在函数返回之后才会执行，比如异步操作，函数会立即返回，但是闭包会在异步操作结束后才执行，逃逸闭包作为参数时用`@escaping`标注，闭包内必须显式引用self
* 闭包和函数是引用类型
* `autoclosure`关键字自动将参数转换为闭包，自动闭包可以省略闭包外面的大括号，用普通表达式代替显式的闭包
```
var customersInLine = ["Chris","Alex","Ewa","Barry","Daniella"]
///不使用@autoclosure
func serve(customer customerProvider: () -> String) {
    ///do something
}
serve( customer:{ customerInLine.remove(at:0) } )
///使用@autoclosure
func serve(customer customerProvider: @autoclosure () -> String) {
    ///do something
}
serve(customer: customerInLine.remove(at:0))
```
## Properties（属性）

*  值类型（枚举和结构体）如果被定位常量，其属性不可被修改，引用类型（类）被定义为常量依然可以修改其属性

* **lazy**关键字声明延迟存储属性，即懒加载，注意，只针对存储属性，延迟存储属性的初始化过程非线程安全，即可能被创建多次

* 计算属性提供一个getter和**可选的**setter，计算属性只能定义为变量

* 只读计算属性即只有getter没有setter,可以去掉get及大括号，形如：
```
var volume {
    return width * height * depth
}
```
* 属性观察器`willSet`和`didSet`,可以为**存储属性**和**继承的存储和计算属性**设置属性观察器，不必为非继承的计算属性设置属性观察器，可以直接使用set方法进行属性观察

* 全局变量和局部变量支持属性相关的一切内容，并且全局变量和常量**都是**延迟加载的，不需要用lazy声明，局部常量和变量**从不**延迟加载

## Methods （方法）

* 类型的每一个实例都有一个隐藏的属性`self`，代表该实例本身,如果在类方法中使用self则代表类本身

* 在值类型的实例方法中修改属性，使用`mutating`，可以在可变方法内部修改实例的值，
* 给隐藏的self属性赋予一个全新的实例也需要使用`mutating`

* 类方法在方法前面加上`static`，如
```
static func someTypeMethod {

}
```
类方法中可以直接调用本类型的其它类方法和类属性

## 构造过程（init）

* 构造器以`init`命名

* **常量属性只能在父类构造器中设置初始值**

### 

* 如果类或者结构体中的属性都有默认值，将会自动获得一个默认构造器，
```
class ShoppingListItem {
    var name: String?
    var quantity = 1
    var purchased = false
}
var item = ShoppingListItem()
```
注意：Class.init()和Class()为同一种结果，都是调用默认构造器

* 结构体中如果没有提供自定义构造器，将会自动获得一个逐一成员构造器，即使属性没有默认值

* 如果提供了自定义构造器，将无法访问默认构造器和结构体的逐一成员构造器

### 类的继承和构造过程

* 子类重写父类的指定构造器必须用`override`修饰符
* 由于子类不能直接调用父类的便利构造器，所以子类实现一个和父类一样的便利构造器时不需要用override修饰
* 如果子类中没有提供自定义指定构造器，将自动继承父类指定构造器，相反的，如果子类提供了自定义指定构造器，将不会继承任何父类构造器
* 两段式构造过程

    第一阶段，类中的每个存储属性赋一个初始值

    第二阶段，每个类在新实例准备使用之前可以进一步定制它们的存储属性
* 类构造器规则
1. 子类在调用父类指定构造器之前必须保证所在类的所有属性初始化完成
2. 指定构造器在为继承的属性设置新值之前必须调用父类指定构造器
3. 便利构造器必须在为任意属性设置新值之前调用同一类中的其他构造器
4. 构造器在第一阶段完成之前，不能调用任何实例方法，不能读取任何实例属性的值，不能引用`self`作为一个值
### 协议
* 如果在协议中定义了一个实例方法，该方法会改变遵循该协议的类型的实例，那么在定义协议时需要在方法前加上`mutating`，这样使得结构体和枚举能够遵循该协议并满足方法要求。
>实现协议方法时，如果是类类型，则不需要写`mutating`关键字。如果是值类型则需要写`mutating`关键字
* 协议扩展：指为协议提供一个通用实现，遵循该协议的所有类都会自动获得该实现
* 遵循协议的类如果自己实现了协议，会覆盖协议扩展的实现
* 可以使用`where`关键字为协议扩展指定限制条件，例如
```
extension Collection where Element: Equatable {

}
```
>注意：如果一个遵循的类型满足了为同一方法或属性提供实现的多个限制型扩展的要求，swift使用这个实现方法去匹配那个最特殊的限制
### 泛型
* 扩展泛型类型
```
struct Stack<Element> {

}
///不需要在扩展定义中提供类型参数列表
extension Stack {
    var topItem: Element?
}
```
* 类型约束语法
```
func somFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    /// 这里是范型函数的函数体部分
}
```
* 关联类型，定义协议的时候有可能需要一个或者多个关联类型，定义关联类型的关键字为`associatedtype`
```
protocol Container {
    associatedtype Item
    func mutating append(_ item: Item)
    subscript(i: Int) -> Item { get }
}
```
 Container协议声明了遵循该协议的类，结构体或者枚举必须具备的三个条件
* 可以给关联类型添加约束
```
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    subscript(i: Int) -> Item { get }
}
```
* 在协议的关联类型约束中使用自身，即协议中的关联类型的约束包括遵循自身
```
protocol Suffixable: Container {
    ///Suffixable协议中的关联类型Suffix遵循Suffixable并且Suffix中的关联类型Item和Suffixable协议中的Item相同
    associatedtype Suffix: Suffixable where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}


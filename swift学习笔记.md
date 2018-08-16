# swift基础语法理解

## 基础知识

* 可选项绑定

    用来判断一个可选项是否有值，如果可选项有值就将其赋值给一个常量或者变量

* 隐式展开可选项

    明确知道可选项是否有值（根据代码逻辑确认一定有值，声明的时候并不知道是否有值），可以默认该可选项有值，

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

## functions

* 没有返回值可以省略`->`及其后面的void，形如

        func greet(person: String) {
            //do something
        }

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

   方法的参数默认为常量不能修改， In-Out参数为指针参数，可以在方法内修改参数值，在外部同样生效

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

    声明在方法内部的方法，称为nested functions

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

* 闭包和函数是引用类型
* `autoclosure`关键字自动将参数转换为闭包
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
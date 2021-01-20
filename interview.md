# runtime（元类，方法调用过程，分类相关，常见应用）

## 源码

1. `[receiver message]`过程，详细。包括调用路径，查找方法过程，方法缓存机制

2. 分类可以添加哪些内容，为什么，不能添加哪些内容，为什么。分类添加的内容何时被加到当前类中，和当前类的顺序是怎样的。如果分类中定义一个和原来类中相同方法名的方法。会怎样。分类和类load方法的调用顺序（类先，在`call_load_methods`中）

3. runtime应用场景 

   kvo和通知（NSNotificationCenter），swizzle method，model解析，未实现方法补救（三次机会）,自动归解档， kvc， 增加属性。

   多个子类进行了方法交换会怎样
   
   方法交换时直接进行方法交换会如何（不进行addMethod操作）
   
   交换类方法需要注意什么 
   
   ![](/Users/maomaoshou/Documents/notes/assets/interview_1.png)

# runloop

# 线程

* 常见锁

# 内存

* weak实现
* block中\__weak和__strong的原理
* 自动释放池（autorelease pool）

# 归档解档

# 持久化

# 网络

# 常见三方源码

* sdwebimage
* afnetworking

# 性能

* 如何保证滑动fps
* block分哪几种
* 堆和栈
* 锁
* 线程有哪几种，使用场景
* \__weak __strong 原理
* \__block原理
* 局部变量什么时候释放
* category为什么不能添加实例变量，category和类实现同一个方法会怎样
* 快排
* 反转链表
* 性能优化，启动优化
* 锁的类型及使用场景
* assign和weak
* weak的实现
* 自动释放池的实现及应用
* 自己在工作中比较满意的事
* h5与原生交互调用方法结果如何告知h5
* 组件化如何调用，详细
* 如何获取一个对象的大小
* sdwebimage图片缓存逻辑
* NSURLConnection和NSURLSession的不同点
* NSCache和NSDictionary的区别

## 基础

1. @synthesize和@dynamic分别有什么作用

   1. @property有两个对应的词，@synthesize和@dynamic。如果这两个词都没有写，默认是@sythesize var = \_var
   2. @synthesize的语义是如果没有手动实现setter和getter方法，编译器会自动加上这两个方法，即**自动合成**
   3. @dynamic告诉编译器：属性的setter和getter方法由用户自己实现，不自动生成。如果声明了@dynamic property，然后没有提供getter和setter方法，在调用instance.var = someVar或者someVar = var时，会引发运行时崩溃，即**动态合成**

2. kvo

   1. 实现原理：

      * 当某个类的属性被首次观察时，系统会在运行时动态创建一个继承该类的派生类，在这个派生类中重写基类中任何被观察属性的setter方法。派生类在被重写的setter方法内实现真正的通知机制，然后将被观察对象的isa指针指向该派生类，这样当被观察属性的值发生变化时就会执行派生类重写的set方法
      * 如果原类为Person，那么生成的派生类名为`NSKVONotifying_Person`
      * 键值观察通知依赖`willChangeValueForKey`和`didChangeValueForKey`方法，一个在属性值变化前调用，一个在属性值变化后调用
      * KVO实现机制同时也重写了`class`方法，将返回结果设置为当前类，从而隐藏派生类

      ![](/Users/maomaoshou/Documents/notes/assets/kvo_imp.png)

   2. 如何手动触发：调用`willChangeValueForKey:`和`didChangeValueForKey:`方法

   3. kvo和kvc的keyPath可以是成员变量

3. 事件传递链和响应链

   * 如何获取一个view所在的viewController，思路：while循环，获取nextResponder，判断nextResponder是否是viewController

   ![](/Users/maomaoshou/Documents/notes/assets/vcForView.png)

4. 真机64位需要arm64架构，模拟器64位需要x86_64架构

## App签名

原理：[App签名的原理](http://wereadteam.github.io/2017/03/13/Signature/)


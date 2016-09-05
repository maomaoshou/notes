# 基于UIView的Layer方法

1. layoutSubviews:该方法默认没做任何事，需要子类实现。在ios5.1后，该方法的缺省实现是使用你设置在此view上面的constraints\(Autolayout\)去决定subviews的position和size。只有在autoresizing和constraint-based behaviors of subviews不能提供我们想要的布局结果的时候，我们才应该重写此方法。可以在此方法中直接设置subviews的frame。 我们不应该直接调用此方法，而应当用setNeedsLayout和layoutIfNeeded这两个方法。以下情况会触发该方法:

  * 使用addSubview方法
  * frame值发生变化
  * 滚动scrollView
  * screen发生旋转

2. setNeedsLayout:该方法会将 view当前的layout设置为无效的，在下一个update cycle里去触发layout更新

3. setIfNeeded: 使用此方法强制立即进行layout,从当前view开始，此方法会遍历整个view层次\(包括superviews\)请求layout。因此，调用此方法会强制整个view层次布局。

4. Auto layout在view显示之前，多引入了两个步骤：updating constraints 和laying out views。每一个步骤都依赖于上一个。display依赖layout，而layout依赖updating constraints。 updating constraints-&gt;layout-&gt;display

5. UIView的setNeedsDisplay和setNeedsLayout方法。首先两个方法都是异步执行的。layoutSubviews方便数据计算，drawRect方便视图重绘。setNeedsDisplay会调用自动调用drawRect方法，这样可以拿到UIGraphicsGetCurrentContext，就可以画画了。而setNeedsLayout会默认调用layoutSubViews，就可以处理子视图中的一些数据。

6. sizeToFit和sizeThatFits:

7. drawRect会在以下情况被调用：

  * 如果在UIView初始化时没有设置rect大小，将直接导致drawRect不被自动调用。drawRect 掉用是在Controller-&gt;loadView, Controller-&gt;viewDidLoad 两个方法之后的.所以不用担心在控制器中,这些View的drawRect就开始画了.这样可以在控制器中设置一些值给View\(如果这些View draw的时候需要用到某些变量值\).  
  * 该方法在调用sizeToFit后被调用，所以可以先调用sizeToFit计算出size。然后系统自动调用drawRect:方法。sizeToFit会自动调用sizeThatFits方法。sizeToFit方法不应该在子类中重写，而应该重写sizeThatFits方法。sizeThatFits传入的参数是 receiver当前的size,返回一个合适的size。sizeToFit可以被手动调用。sizeToFit和sizeThatFits都没有递归，也不对subviews负责，只对自己负责。
  * 通过设置contentMode属性值为UIViewContentModeRedraw。那么将在每次设置或更改frame的时候自动调用drawRect:。
  *  直接调用setNeedsDisplay，或者setNeedsDisplayInRect:触发drawRect:，但是有个前提条件是rect不能为0。



    -setNeedsDisplay方法：标记为需要重绘，异步调用drawRect



    -setNeedsDisplayInRect:\(CGRect\)invalidRect方法：标记为需要局部重绘

  以上1，2推荐，3，4不推荐。


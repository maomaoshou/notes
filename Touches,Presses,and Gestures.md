# Touches,Presses,and Gestures
## Handling UIKit Gestures（使用手势处理用户事件）
* gesture recognizers处理点击事件和用户连续触摸事件
* 手势有两种：连续和单次，单次手势只会在达到触发条件时调用一次action,而连续手势则会在手势相关的event改变时就通知你
* 对于连续手势使用`state`属性追踪手势状态
* 自定义手势，需要导入`UIGestureRecognizerSubclass.h`头文件，分别实现`touchesBegan:withEvent:`,`touchesMoved:withEvent:`,`touchesEnded:withEvent:`,`touchesCanceled:withEvent:`,在每个方法里根据event更新`state`属性
## Handling Touches in Your View（使用touch events处理用户事件）
* 实现`UIResponder`的四个方法，`touchesBegan:withEvent:`,`touchesMoved:withEvent:`,`touchesEnded:withEvent:`,`touchesCanceled:withEvent:`，默认每个`event`只会关联第一个`UITouch`对象，即使有多个手指，如果需要关联其他touches,需要设置`UIView`的`multipleTouchesEnabled`为yes
## Responder and the Responder Chain

* 任何`UIResponder`的实例及`UIView`,`UIViewController`和`UIApplication`的子类及实例都可以响应event
* Responder chains
![](/Users/maomaoshou/Documents/notes/assets/responderchains.jpg)

* `UIControl`直接把events传递给对应的target,如果没有对应的target,则会按照responder chains寻找实现action的target
* 手势会先于view接收到事件，如果一个view的手势识别失败，则会把事件传递给view
* UITouch的location或者其它参数改变，UITouch会随着改变，唯一不变的是对象对应的`view`属性
* 可以通过改变`nextResponder`改变事件响应链，注意，只有当前响应者没有处理事件，事件才会顺着响应链向下传递
* 系统已经重写了许多UIKit类的nextResponder
    * UIView 如果UIView是一个view controller的根view,则nextResponder为 view controller,否则为自己的superview
    * UIVIewController 
        * 如果view controller是一个window的根view,则next responder是该window
        * 如果一个view controller是被另一个view controller presented出来的，则next responder是presenting view controller
    * UIWindow next responder是UIApplication对象
    * UIApplication next responder是 app delegate,但是app delegate必须是UIResponder的实例并且不是view,view controller或者app本身
## 处理多手势
* UIKit默认同一时间只会识别一个手势，例如如果一个view同时包含pan和swipe手势，那么swipe手势永远不会被识别
* 使用`UIGestureRecognizerDelegate`相关方法实现
* 
 ```
 - (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldBeRequiredToFailByGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer {
     // Do not begin the pan until the swipe fails.
     if gestureRecognizer == self.swipeGesture && 
          otherGestureRecognizer == self.panGesture {
      return true
    }
    return false
 }

 
 - (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRequireFailureOfGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer {
    // Don't recognize a single tap until a double-tap fails.
   if gestureRecognizer == self.tapGesture && 
          otherGestureRecognizer == self.doubleTapGesture {
      return true
   }
   return false
}
 ```

# layoutSubviews,setNeedLayout,layoutIfNeeded,drawRact

1,layoutSubviews

该方法默认没做任何事，需要子类实现。以下情况会触发该方法

* 使用addSubview方法
* frame值发生变化
* 滚动scrollView
* screen发生旋转


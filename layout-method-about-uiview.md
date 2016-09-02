# layoutSubviews,setNeedLayout,layoutIfNeeded,drawRact

1,layoutSubviews

该方法默认没做任何事，需要子类实现。在ios5.1后，该方法的缺省实现是使用你设置在此view上面的constraints\(Autolayout\)去决定subviews的position和size。只有在autoresizing和constraint-based behaviors of subviews不能提供我们想要的布局结果的时候，我们才应该重写此方法。可以在此方法中直接设置subviews的frame。 我们不应该直接调用此方法，而应当用setNeedLayout和layoutIfNeeded这两个方法。以下情况会触发该方法

* 使用addSubview方法
* frame值发生变化
* 滚动scrollView
* screen发生旋转

特别注意：如果想在外部设置subviews的位置，就不要重写该方法
2,setNeedLayout

该方法标记为需要重新布局，不会马上调用layoutSubviews，但一定会调用


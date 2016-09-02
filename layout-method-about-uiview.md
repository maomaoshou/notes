# layoutSubviews,setNeedLayout,layoutIfNeeded,drawRact

1.  layoutSubviews:该方法默认没做任何事，需要子类实现。在ios5.1后，该方法的缺省实现是使用你设置在此view上面的constraints\(Autolayout\)去决定subviews的position和size。只有在autoresizing和constraint-based behaviors of subviews不能提供我们想要的布局结果的时候，我们才应该重写此方法。可以在此方法中直接设置subviews的frame。 我们不应该直接调用此方法，而应当用setNeedLayout和layoutIfNeeded这两个方法。以下情况会触发该方法
  使用addSubview方法
  frame值发生变化
  滚动scrollView
  screen发生旋转
2.  setNeedLayout:该方法会将 view当前的layout设置为无效的，在下一个update cycle里去触发layout更新
3. setIfNeeded: 使用此方法强制立即进行layout,从当前view开始，此方法会遍历整个view层次\(包括superviews\)请求layout。因此，调用此方法会强制整个view层次布局。


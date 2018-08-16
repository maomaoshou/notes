# 动画相关知识

1.使用UIView相关动画方法进行动画，如果对控件的frame进行改变(*包括位置和大小*),需要在执行动画的内部调用`-(void)layoutIfNeeded`方法。
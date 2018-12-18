* anchorPoint和position
`anchorPoint`是相对于自身坐标系
`position`是相对于父坐标
两者是相对于不同坐标系的重合点

* CALayer的`transform`属性用来scale（缩放），rotation（旋转），transform（移动）view的位置

* UIView和CALayer最大的不同在于CALayer不响应用户交互

* 使用UIView和CALayer原因在于职责分离

* UIView无法处理的事情
1. 阴影，圆角，带圆角的边框
2. 3D变换
3. 非矩形范围
4. 透明遮罩
5. 多级非线形动画

* CALayer的content相关

1. contents:可以是任何类型的对象，一般都是CGImage
2. contentGravity:对应UIView的contentMode,表示内容在layer的边界中怎么对齐
3. contentScale:定义了内容和layer的大小比例，默认是1.0，即一个点绘制一个像素
4. maskToBounds:内容是否超出layer的边界
5. contentRect:0到1的浮点数，表示内容如何被拉伸及显示，如果设置了负数的原点或者大于（1，1）的尺寸，最外面的像素会被拉伸
6. contentCenter:定义一个可拉伸的矩形和图层中可拉伸的区域

* CALayer的position和UIView的center是同一个值
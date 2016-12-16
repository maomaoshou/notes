1. 注意点：

1. 最好使用release包,这样调试出来的结果最准确

2. 用真机调试


1. 相关调试维度

Color Blended Layers\(图层混合\):如果界面中有多个UI元素发生了重叠，并且有透明或者半透明的元素\(即alpha值不为1\),那么CPU需要计算这些元素叠加在一起最终显示的颜色，这需要消耗一部分资源。即发生了图层混合。消除图层混合的方法为：

1. 设置opaque属性为true

2. 给View设置一个不透明的颜色，没有特殊要求一般设置为白色即可\(如果设置label的background需要同时设置label.layer.maskToBounds = YES;UIImageView不仅需要自身这个容器是不透明的，并且imageView包含的内容图片也必须是不透明的\)


Color Hits Green and Misses Red（光栅化）




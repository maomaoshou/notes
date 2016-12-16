### 注意点：

1. 最好使用release包,这样调试出来的结果最准确

2. 用真机调试


## 相关调试维度

**Color Blended Layers\(图层混合\)**:如果界面中有多个UI元素发生了重叠，并且有透明或者半透明的元素\(即alpha值不为1\),那么CPU需要计算这些元素叠加在一起最终显示的颜色，这需要消耗一部分资源。即发生了图层混合。消除图层混合的方法为：

1. 设置opaque属性为true

2. 给View设置一个不透明的颜色，没有特殊要求一般设置为白色即可\(如果设置label的background需要同时设置label.layer.maskToBounds = YES;UIImageView不仅需要自身这个容器是不透明的，并且imageView包含的内容图片也必须是不透明的\)


**Color Hits Green and Misses Red（光栅化）**：光栅化是将一个layer预先渲染成位图\(bitmap\)，再加入到缓存中。shouldRasterize = YES开启光栅化。正确使用光栅化可以得到一定程度的性能提升。

1. 适用情况：一般在图像内容不变的情况下才使用光栅化，例如设置阴影耗费资源比较多的静态内容，如果使用光栅化对性能的提升有一定帮助。

2. 非适用情况：如果内容会经常变动,这个时候不要开启,否则会造成性能的浪费。例如我们在使用tableViewCell中，一般不要用光栅化，因为tableViewCell的绘制非常频繁，内容在不断的变化，如果使用了光栅化，会造成大量的离屏渲染降低性能。


**Color Copied Images（图片颜色格式）**：如果GPU不支持当前图片的颜色格式，那么就会将图片交给CPU预先进行格式转化，并且这张图片标记为蓝色。

**Color Misaligned Images\(图片大小\)**：如果image size和imageView size不匹配，image会出现黄色。要尽可能的减少黄色的出现

**Color Offscreen-Rendered Yellow（离屏渲染）：**指的是GPU在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作。还有另外一种屏幕渲染方式-当前屏幕渲染On-Screen Rendering ，指的是GPU的渲染操作是在当前用于显示的屏幕缓冲区中进行。 离屏渲染会先在屏幕外创建新缓冲区，离屏渲染结束后，再从离屏切到当前屏幕， 把离屏的渲染结果显示到当前屏幕上，这个上下文切换的过程是非常消耗性能的，实际开发中尽可能避免离屏渲染。触发离屏渲染的行为：

（1）drawRect:方法  
（2）layer.shadow  
（3）layer.allowsGroupOpacity or layer.allowsEdgeAntialiasing  
（4）layer.shouldRasterize  
（5）layer.mask  
（6）layer.masksToBounds && layer.cornerRadius

需要注意的是第三条layer.shouldRasterize ，其实就是光栅化，光栅化会触发离屏渲染，因此光栅化慎用。

第六条设置圆角会触发离屏渲染，如果在某个页面大量使用了圆角，会非常消耗性能造成FPS急剧下降，设置圆角触发离屏渲染要同时满足下面两个条件:

> layer.masksToBounds = YES;
>
> layer.cornerRadius = 5;




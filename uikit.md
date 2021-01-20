## UIButton

* titleEdgeInsets和imageEdgeInset设置规则

* top和right，left和right必须一正一负对应设置
* image移到右边，移动距离为title的width（alignment均为center的时候）
* image移到上边，纵向移动距离为title的height，横向移动距离为title的宽度/2（alignment均为center的时候）

* UIButton对title和image的布局规则：

imageView的宽高都能被压缩，titleLabel的宽只能压缩不能拉伸，titleLabel的高只能拉伸不能压缩

约束优先级

    top-left-bottom-right取负值 &gt;不能超出button边界 &gt; imageView不能被压缩
    top-left-bottom-right取负值 &gt;不能超出button边界 &gt;titleLabel水平方向不能被压缩
    titleLabel垂直方向不能被压缩 &gt;不能超出button边界

水平方向  
if \(button.width小于imageView上image的width\){图像会被压缩，文字不显示}

if \(button.width &lt; imageView.width + label.width\){图像正常显示，文字显示不全}

if \(button.width &gt;＝ imageView.width + label.width\){图像和文字都居中显示，imageView在左，label在右，中间没有空隙}

* titleEdgeInsets和imageEdgeInsets

    * imageEdgeInset top,left,bottom,right四个方向的值都可以改变，正值靠近中心，负值向外拉伸

## UITableView

* rowHeight,如果所有Cell高度都一样且有固定值，建议使用`rowHeight`属性设置，减少不必要的高度计算和代理方法调用

## UIView

UIView的frame与bounds和center的关系：

UIView.frame.origin = center - \(bounds.size / 2.0\)

UIView.center = frame.origin + \(bounds.size / 2.0\)

UIView.frame.size = bounds.size


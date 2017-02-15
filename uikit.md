#### UIButton

UIButton对title和image的布局规则：

imageView的宽高都能被压缩，titleLabel的宽只能压缩不能拉伸，titleLabel的高只能拉伸不能压缩

约束优先级

* top-left-bottom-right取负值 &gt;不能超出button边界 &gt; imageView不能被压缩
* top-left-bottom-right取负值 &gt;不能超出button边界 &gt;titleLabel水平方向不能被压缩
* titleLabel垂直方向不能被压缩 &gt;不能超出button边界

水平方向  
if \(button.width小于imageView上image的width\){图像会被压缩，文字不显示}

if \(button.width &lt; imageView.width + label.width\){图像正常显示，文字显示不全}

if \(button.width &gt;＝ imageView.width + label.width\){图像和文字都居中显示，imageView在左，label在右，中间没有空隙}

### UIView

UIView的frame与bounds和center的关系：

UIView.frame.origin = center - \(bounds.size / 2.0\)

UIView.center = frame.origin + \(bounds.size / 2.0\)

UIView.frame.size = bounds.size


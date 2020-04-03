# 开发小技巧

* 扩大UIButton点击区域

重写UIButton的`pointsInside`方法，使得按钮点击区域不够44 * 44pt的自动放大点击区域

        - (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent*)event {
            CGRect bounds = self.bounds;
            //若原热区小于44*44，则放大热区，否则保持原大小不变
            CGFloat widthDelta = MAX(44.0 - bounds.size.width,0);
            CGFloat heightDelta = MAX(44.0 - bounds.size.height,0);
            bounds = CGRectInset(bounds,-0.5 * widthDelta, -0.5 * heightDelta);
            return CGRectContainsPoint(bounds, point);
        }
>备注：CGRectInset会以2倍dx或者dy去放大或者缩小原有区域，所以要乘以0.5。

* Xcode中添加TODO

    * `//TODO:do something`

    * `//???:`

    * `//!!!:`

    * `//FIXME:`

    * `//MARK:`

* Xcode添加TODO编译提示:

    `KEYWORDS="TODO:|FIXME:|\?\?\?:|\!\!\!:"
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -print0 | xargs -0 egrep --with-filename --line-number --only-matching "($KEYWORDS).*\$" | perl -p -e "s/($KEYWORDS)/ warning: \$1/"`
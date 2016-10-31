1. Xcode8在界面未展示前，所有的view的布局都是1000\*1000的。解决办法：**在使用frame或者bounds或者center取值前，使用 layoutIfNeeded 强制进行布局。**

2. 关于Xcode8打印无用log,在Xcode中 标题栏Product \/ Scheme \/ Edit Scheme

3. 进入后点击 RUN \/ Arguments \/ Environment Variables 在其中键入 OS\_ACTIVITY\_MODE : disable



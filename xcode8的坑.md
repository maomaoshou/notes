1，Xcode8在界面未展示前，所有的view的布局都是1000\*1000的。解决办法：**在使用frame或者bounds或者center取值前，使用 layoutIfNeeded 强制进行布局。**


1. 关于加载 cell时的复用问题，如果cell中的控件因model而展示不同，则需要考虑到复用，设置cell展示时进行控件的重置

2. 关于 UIButton的相关属性值设置，要注意保持同一个button在不同状态相同属性值的 state保持同步。形如：                     ```if(a){[button setTitle:@"1" ``state: UIControlStateNormal``];} else {[button setTitle:@"2" ``state: UIControlStateNormal``];}两种情况下button的state要保持同步```

3. 做判断时要流程清晰，保证任何参数都不会crash,逻辑不要冗余

4. 如果设置button的title等属性时传的state参数为UIControlStateDisabled,那么button对应的edable属性要同步设置为NO。否则对应的state参数为UIControlStateNormal。

5. 关于button的几种状态normal,highlighted,selected.disabled.从这里了解：http:\/\/www.jianshu.com\/p\/e87096da5512

6. 模拟器卡可以在命令行执行：sudo sysctl -w kern.timer.coalescing\_enabled=0





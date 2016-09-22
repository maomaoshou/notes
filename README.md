1. 关于加载 cell时的复用问题，如果cell中的控件因model而展示不同，则需要考虑到复用，设置cell展示时进行控件的重置
2. 关于 UIButton的相关属性值设置，要注意保持同一个button在不同状态相同属性值的 state保持同步。形如：                     `if(a){[button setTitle:@"1" state: UIControlStateNormal];} else {[button setTitle:@"2" state: UIControlStateNormal];}`
3. 做判断时要流程清晰，保证任何参数都不会crash,逻辑不要冗余


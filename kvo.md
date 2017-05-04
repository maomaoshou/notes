1. KVC机制通过key找到value的原理

   当通过KVC调用对象时，比如：\[self valueForKey:@”someKey”\]时，程序会自动试图通过下面几种不同的方式解析这个调用。

   * 首先查找对象是否带有 someKey 这个方法，如果没找到，会继续查找对象是否带有someKey这个实例变量（iVar），如果还没有找到，程序会继续试图调用 -\(id\) valueForUndefinedKey:这个方法。如果这个方法还是没有被实现的话，程序会抛出一个NSUndefinedKeyException 异常错误。

   * KVC查找方法的时候，不仅仅会查找someKey这个方法，还会查找getsomeKey这个方法， 前面加一个get，或者\_someKey以\_getsomeKey这几种形式。同时，查找实例变量的时候也会不仅仅查找someKey这个变量，也会查找 \_someKey这个变量是否存在。

   * 设计valueForUndefinedKey:方法的主要目的是当你使用-\(id\)valueForKey方法从对象中请求值时，对象能够在错误发生前，有最后的机会响应这个请求

2.KVO基于KVC，但是只对属性值进行观察，属性值的改变必须遵循KVC


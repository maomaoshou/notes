常见内存泄漏的问题

* 如果调用了addScriptMessageHandler,需要在控制器释放前对称调用removeScriptMessageHandlerForName
* CoreFoundation对象的内存管理需要调用对应的CFRelease方法
* NSTimer的使用
* NSNotification的使用




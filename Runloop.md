
* runloop大致描述
 ```
 while (alive) {
     performTask()//执行任务
     callout_to_observer()//通知外部
     sleep()//休眠
 }
 ```
* mach_msg，这是系统内核在某个port收发消息所使用的函数，收发都调用该函数，发送消息的参数为MACH_SEND_MSG,接收为MACH_RCV_MSG,可以将mach_msg理解为多进程之间的一种通信机制，不同进程可以使用同一个消息队列来交流数据，当使用mach_msg从消息队列读取msg的时候可以设置timeout值，在timeout之前如果没有读取到msg,当前线程会一直处于休眠状态。这也是runloop在没有任务可执行的时候可以进入sleep状态的原因
* 执行任务的5种performTask：
    * DoBlocks()，可以供开发者使用
    * DoSources0(),可以供开发者使用
    * DoSources1(),只能供系统使用，通过读取某个port上内核消息列队的消息来决定执行的任务
    * DoTimers(),NSTimer相关的任务会调用该方法执行
    * DoMainQueue(),加入主线程的任务会调用该方法执行，但是该方法和runloop mode不相关
* callout_to_observer

    * DoObservers-Timer,执行完DoTimers后通知感兴趣的observer
    * DoObservesr-Source0,执行完DoSource0后通知感兴趣的observer
    * DoObservers-Activity,runloop用来通知外部自己当前的状态，具体状态为CFRunLoopActivity
    ```
    typedef CF_OPTIONS(CFOPtionsFlags, CFRunLoopActivity) {
    kCFRunLoopEntry = (1UL <<  0)
    kCFRunLoopBeforeTimers = (1UL << 1)
    kCFRunLoopBeforeSources = (1UL << 2)
    kCFRunLoopBeforeWaiting = (1UL << 5)
    kCFRunLoopAfterWaiting = (1UL << 6)
    kCFRunLoopExit = (1UL << 7)
    kCFRunLoopAllActivities (1UL << 0x0FFFFFFFU)
    };
    ```
    * kCFRunLoopEntry,每次runloop重新进入时的activity
    * kCFRunLoopBeforeTimers,即DoObservers-Timers
    * kCFRunLoopBeforeSources,即DoObservers-Sources
    * kCFRunLoopBeforeWaiting,表示当前线程即将进入休眠，如果从内核队列上读出msg则继续运行任务，
    * kCFRunLoopAfterWaiting,当前线程从睡眠状态中恢复过来
    * kCFRunLoopExit,切换mode的时候有可能用到
* runloop关键流程

![](/Users/maomaoshou/Documents/notes/assets/runloop.png)

细节解释
1. Poll?，如果执行了source0任务，poll的值为true,如果poll值为true,则不会DoObserversBeforeWaiting和DoObserversAfterWaiting,即runloop会直接进入休眠
1. mach_msg,一次loop里有两次调用mach_msg,没有标记出来的发生在DoSource0之后，会主动读取和mainQueue相关的队列，但是这个mach_msg调用不会进入休眠，因为timeout值传入的是0，如果读取到了消息，就直接goto到DoMainQueue的代码
1. PortType，每次runloop被唤醒之后会根据port type决定到底执行哪一类任务

* RunLoop Mode 下面代码列出了runloopMode中主要的信息
```
struce __CFRunLoopMode {
    ...
    CFStringRef _name;
    CFMutableSetRef _sources0;
    CFMutableSetRef _sources1;
    CFMutableArrayRef _observers;
    CFMutableArrayRef _timers;
    CFMutableDictionaryRef _portToV1SourceMap;
    CFIndex _observerMask;
    ...
}
```

* 获取主线程：pthread_main_thread_up()或者[NSThread mainThread]

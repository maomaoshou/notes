# AF源码学习

## 基础知识

* 相关原生类

    1. NSURLConnection：指构成Foundation框架中URL加载体系的相关组件：NSURLRequest,NSURLResponse,NSURLProtocol,NSURLCache,NSHTTPCookieStorage,NSURLCredentialStorage以及NSURLConnection。
    1. NSURLSession:是2013年苹果对NSURLConnection进行重构后产生的替代品，除了包括同NSURLConnection中相同的相关网络类外，NSURLSession将NSURLConnection替换为NSURLSession和NSURLSessionConfiguration，以及三个NSURLSessionTask子类：NSURLSessionDataTask,NSURLSessionUploadTask和NSURLSessionDownloadTask。

## AFURLSessionManagerTaskDelegate

该类主要有两个作用

1. 观察每个task的countOfButesReceived，countOfBytesExpectedToReceive，countOfBytesSent，countOfByteExpectToSend等值的变化，将变化关联到downloadProgress或者uploadProgress，同时观察downloadProgress和uploadProgress的变化，进行block回调
2. 分别实现NSURLSessionTaskDelegate，NSURLSessionDataDelegate，NSURLSessionDownloadDelegate中的一个代理方法，将请求结果进行统一处理
   * URLSession:task:didCompleteWithError: 在该代理方法中，对请求结束返回的data或者downloadFileURL进行相关处理后触发block回调并发送通知，通知中传入userInfo信息，包含相关处理结果
   * URLSession:dataTask:didReceiveData: 在该代理中将data进行append操作
   * URLSession:downloadTask:didFinishDownloadingToURL: 在该代理中将下载的内容从临时位置保存至永久位置（沙盒中）

## \_AFURLSessionTaskSwizzling

该类主要对NSURLSessionTask的resume和suspend方法使用了swizzling技术，进行了方法交换。由于NSURLSessionTask是用类簇实现的（用NSURLSessionTask把真正的类隐藏起来），且在iOS7和iOS8之后的实现中对应真正的类有所不同，且这些类中的resume和suspend方法都没有调用父类实现。所以交换逻辑如下：

1. 通过'NSURLSession'创建一个data task对象，从而获取'__NSLocalDataTask'对象（data task对应的真正类）
2. 获取交换后方法'af_resume'的实现
3. 检查当前类是否有resume方法的实现，如果有，继续步骤4
4. 获取当前类的父类
5. 获取当前类的'resume'方法实现
6. 获取父类的'resume'方法实现
7. 如果当前类的'resume'方法实现和父类（父类resume）与交换后（af_resume）的实现都不同。则进行resume和suspend方法的交换（方法交换的逻辑为先在类中添加'af_resume'实现，再将'af_resume'和'resume'进行交换）

交换后的方法只是在原方法的基础上发送了DidResume和DidSuspend通知

## AFSecurityPolicy

* SSL Pinning方式
  	1. AFSSLPinningModeNone：去系统信任的CA机构列表验证，客户端不需要做任何操作，如果是CA签发的证书会通过，如果是自签名证书不会通过
   	2. AFSSLPinningModeCertificate：用证书绑定方式验证证书，需要客户端保存服务器的证书拷贝。验证分两步，第一步验证证书的域名/有效期等信息，第二步对比服务器返回的证书和客户端保存的是否一致
   	3. AFSSLPinningModePublicKey：这个方式同样需要客户端保存服务端的证书拷贝。验证时只验证证书里的公钥，不验证证书的有效期等信息

## AFURLSessionManager

该类是AFNetworking库的核心类，其余各类都在该类中进行管理。该类持有一个NSURLSession对象session，通过该对象创建不同的task。关注点：

1. 初始化方法中获取session对应的所有task将delegate和progress设置为nil，防止从后台回来重新初始化（不太懂什么意思）
2. 创建task，此处需要对iOS8之前系统做特殊处理，在串行队列同步创建task，防止同一时刻不同session创建的task有相同的taskIdentifier。创建成功后为task添加对应的 AFURLSessionManagerTaskDelegate对象。添加时需要加锁，保证：
   1. 通过task.taskIdentifier将每个task与AFURLSessionManagerTaskDelegate对象放入字典中
   2. 为每个task添加kvo，包括task自身的属性及uploadProgress和downloadProgress
   3. 添加self为task的观察者，观察resume和suspend操作
3. 该类实现了NSCoping和NSSecureCoding协议

## AFHTTPSessionManager

该类继承自AFURLSessionManager，主要针对HTTP请求进行封装

## AFNetworkReachabilityManager

该类主要监测网络变化，网络状态包括Unknown，NotReachable，ReachableViaWWAN，ReachableViaWiFi


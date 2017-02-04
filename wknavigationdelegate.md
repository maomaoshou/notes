

@protocolWKNavigationDelegate &lt;NSObject&gt;

@optional

//请求之前，决定是否要跳转:用户点击网页上的链接，需要打开新页面时，将先调用这个方法。

- \(void\)webView:\(WKWebView \*\)webViewdecidePolicyForNavigationAction:\(WKNavigationAction \*\)navigationActiondecisionHandler:\(void \(^\)\(WKNavigationActionPolicy\)\)decisionHandler;

//接收到相应数据后，决定是否跳转

- \(void\)webView:\(WKWebView \*\)webView decidePolicyForNavigationResponse:\(WKNavigationResponse \*\)navigationResponse decisionHandler:\(void \(^\)\(WKNavigationResponsePolicy\)\)decisionHandler;

//页面开始加载时调用

- \(void\)webView:\(WKWebView \*\)webView didStartProvisionalNavigation:\(null\_unspecified WKNavigation \*\)navigation;

// 主机地址被重定向时调用

- \(void\)webView:\(WKWebView \*\)webView didReceiveServerRedirectForProvisionalNavigation:\(null\_unspecified WKNavigation \*\)navigation;

// 页面加载失败时调用

- \(void\)webView:\(WKWebView \*\)webView didFailProvisionalNavigation:\(null\_unspecified WKNavigation \*\)navigation withError:\(NSError \*\)error;

// 当内容开始返回时调用

- \(void\)webView:\(WKWebView \*\)webView didCommitNavigation:\(null\_unspecified WKNavigation \*\)navigation;

// 页面加载完毕时调用

- \(void\)webView:\(WKWebView \*\)webView didFinishNavigation:\(null\_unspecified WKNavigation \*\)navigation;

//跳转失败时调用

- \(void\)webView:\(WKWebView \*\)webView didFailNavigation:\(null\_unspecified WKNavigation \*\)navigation withError:\(NSError \*\)error;

// 如果需要证书验证，与使用AFN进行HTTPS证书验证是一样的

- \(void\)webView:\(WKWebView \*\)webView didReceiveAuthenticationChallenge:\(NSURLAuthenticationChallenge \*\)challenge completionHandler:\(void \(^\)\(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential \*\_\_nullable credential\)\)completionHandler;

//9.0才能使用，web内容处理中断时会触发

- \(void\)webViewWebContentProcessDidTerminate:\(WKWebView \*\)webView NS\_AVAILABLE\(10\_11,9\_0\);

  


@end


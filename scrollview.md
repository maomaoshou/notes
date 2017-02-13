### scrollView的delegate

* \(void\)scrollViewDidScroll:\(UIScrollView \*\)scrollView
* \(void\)scrollViewWillBeginDragging:\(UIScrollView \*\)scrollView
* \(void\)scrollViewWillEndDragging:\(UIScrollView \*\)scrollView withVelocity:\(CGPoint\)velocity targetContentOffset:\(inout CGPoint \*\)targetContentOffset
* \(void\)scrollViewDidEndDragging:\(UIScrollView \*\)scrollView willDecelerate:\(BOOL\)decelerate
* \(void\)scrollViewWillBeginDecelerating:\(UIScrollView \*\)scrollView
* \(void\)scrollViewDidEndDecelerating:\(UIScrollView \*\)scrollView

### 连续快速滚动一个scrollView的时候，拖动相关的delegate调用的顺序为：

1. * \(void\)scrollViewDidScroll:\(UIScrollView \*\)scrollView;\/\/只要scrollView的contentOffset值发生改变就会调用

   * \(void\)scrollViewWillBeginDragging:\(UIScrollView \*\)scrollView;\/\/即将开始拖动

   * \(void\)scrollViewWillEndDragging:\(UIScrollView \*\)scrollView withVelocity:\(CGPoint\)velocity targetContentOffset:\(inout CGPoint \*\)targetContentOffset NS\_AVAILABLE\_IOS\(5\_0\);

   * \(void\)scrollViewDidEndDragging:\(UIScrollView \*\)scrollView willDecelerate:\(BOOL\)decelerate;\/\/结束拖动

   * \(void\)scrollViewWillEndDragging:\(UIScrollView \*\)scrollView withVelocity:\(CGPoint\)velocity targetContentOffset:\(inout CGPoint \*\)targetContentOffset;\/\/即将以velocity为初速度开始减速滑动

   * \(void\)scrollViewWillBeginDecelerating:\(UIScrollView \*\)scrollView;\/\/即将开始减速滑动

   * \(void\)scrollViewWillBeginDragging:\(UIScrollView \*\)scrollView;

   * \(void\)scrollViewWillEndDragging:\(UIScrollView \*\)scrollView withVelocity:\(CGPoint\)velocity targetContentOffset:\(inout CGPoint \*\)targetContentOffset;

   * \(void\)scrollViewWillBeginDecelerating:\(UIScrollView \*\)scrollView;

   * \(void\)scrollViewDidEndDecelerating:\(UIScrollView \*\)scrollView;
2. 



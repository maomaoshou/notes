### 重写UICollectionViewLayout需要实现的方法

//  返回CollectionView的内容尺寸

-\(CGSize\)collectionViewContentSize

// 返回rect中的所有的元素的布局属性, 返回的是包含UICollectionViewLayoutAttributes的NSArray, UICollectionViewLayoutAttributes可以是cell, Supplementary View和Decoration View

-\(NSArray \*\)layoutAttributesForElementsInRect:\(CGRect\)rect

// 返回对应于indexPath的位置的cell的布局属性

-\(UICollectionViewLayoutAttributes \_\)layoutAttributesForItemAtIndexPath:\(NSIndexPath \_\)indexPath

//  返回对应于indexPath的位置的追加视图的布局属性

-\(UICollectionViewLayoutAttributes \_\)layoutAttributesForSupplementaryViewOfKind:\(NSString \_\)kind atIndexPath:\(NSIndexPath \*\)indexPath

// 返回对应于indexPath的位置的装饰视图的布局属性

-\(UICollectionViewLayoutAttributes \* \)layoutAttributesForDecorationViewOfKind:\(NSString\_\)decorationViewKind atIndexPath:\(NSIndexPath \_\)indexPath: 

// 默认返回yes, 边界发生变化时, 重新进行布局.

- \(BOOL\)shouldInvalidateLayoutForBoundsChange:\(CGRect\)newBounds:


1. SEL作为IMP的key值存放在NSSet中，通过hash快速查询方法就可以根据SEL找到IMP的具体实现函数。同一个类中不能有两个相同方法，就算参数类型不同也不可以。不同类可以有相同方法。SEL本质是一个NSString。
2. IMP是一个指向函数实现的指针。
3. super仅仅作为一个编译标志符，告诉编译器去调用父类的方法。如果要获取到父类，使用\[self superclass\]
4. \[A isKindOfClass:B\]方法中，执行详情为取A的meta class和B比较，若不同，则取A的meta class的super class和B比较，依次循环
5. \[A isMemberOfClass:B\]是拿 A的meta class和B比较




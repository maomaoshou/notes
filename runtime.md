# Runtime

## **源码**

    struct objc_class {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

    #if !__OBJC2__
    Class _Nullable super_class    ///父类                          OBJC2_UNAVAILABLE;
    const char * _Nonnull name    ///类名                           OBJC2_UNAVAILABLE;
    long version             ///版本                             OBJC2_UNAVAILABLE;
    long info                        ///信息                        OBJC2_UNAVAILABLE;
    long instance_size                ///实例大小                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list * _Nullable ivars    ///成员变量列表              OBJC2_UNAVAILABLE;
    struct objc_method_list * _Nullable * _Nullable methodLists                ///方法列表    OBJC2_UNAVAILABLE;
    struct objc_cache * _Nonnull cache   ///缓存                    OBJC2_UNAVAILABLE;
    struct objc_protocol_list * _Nullable protocols    ///protocol列表      OBJC2_UNAVAILABLE;
    #endif

    } OBJC2_UNAVAILABLE;

> 所有OC类和对象在runtime层都是用struct表示的

## **基本概念**

1. SEL作为IMP的key值存放在NSSet中，通过hash快速查询方法就可以根据SEL找到IMP的具体实现函数。同一个类中不能有两个相同方法，就算参数类型不同也不可以。不同类可以有相同方法。SEL本质是一个NSString。

1. IMP是一个指向函数实现的函数指针

1. @selector选择只与SEL有关

## **isa和Class**

* 实例对象，父类，元类三者关系图

![](/Users/maomaoshou/Documents/notes/assets/metaClass.png)

* **super**仅仅作为一个编译标志符，告诉编译器去调用父类的方法。如果要获取到父类，使用 **[self superclass]**

* [A isKindOfClass:B]执行过程：

  取A的`meta class`和B比较，若不同，则取meta class的super class和B比较，依次循环(**类NSObject的元类的super class为NSObject类本身**)

* [A isMemberOfClass:B]执行过程：

  取**A**的`meta class`和**B**比较。

## **Category**

* Category作用

  1. 为已经存在的类添加方法

  1. 把类的实现分开放在不同文件中,这样做的好处：

      > 减少单个类文件体积

      > 把不同功能放在不同category中

      > 可以由多个开发者共同完成一个类

      > 可以按需加载category

      > 模拟多继承

      > 把framework的私有方法公开

  1. category和extension差异

      category在运行期决议，无法添加实例变量；extension在编译器决议,一般用来隐藏类的私有信息。

* Category实质

  1. Category实质是结构体category_t，

          typedef struct category_t {
            const char *name;
            classref_t cls;
            struct method_list_t *instanceMethods;
            struct mehtod_list_t *classMethods;
            struct protocol_list_t *protocols;
            struct property_list_t *instanceProperties;
         } category_t
    
  1. Category经过编译大致有以下几个产物

  * 实例方法列表和属性列表

  * category本身，并且用前面生成的实例方法列表来初始化本身

  * 编译器在**DATA段下的**objc_catlist section里保存了一个大小为1的category_t数组（多个category对应多个数组）

* Category加载

  加载过程主要进行的操作

  * 把category的实例方法，协议以及属性添加到类上

  * 把category的类方法和协议添加到类的metaclass上

   >需要注意的是category中的方法名如果和类本身的方法名重复，两个方法都会被加到类上，但是先加的是category中的方法，后加的是类本身的方法，所以会存在“category方法覆盖”（运行时在查找方法时是按照方法列表的顺序查找的）

* Category和load方法

    如果在category中实现了load方法，那么category和类本身的load方法都会被调用，调用顺序遵循编译顺序，即先调用Category中的load方法，再调用类本身的load方法

* 利用关联对象为Category添加属性

  MyClass+Category.h:
    
      #import "MyClass.h"

      @interface MyClass (Category)
     
      @property (nonatomic, copy) NSString *name;

      @end
  MyClass+Category.m

      #improt "MyClass+Category.h"
      #import <objc/runtime.h>

      @implementation MyClass (Category);

      + (void)load {
        NSLog (@"load in Category");
      }

      - (void)setName:(NSString *)name {
       objc_setAssociatedObject(self,"name",name,OBJC_ASSOCIATION_COPY);
      }

      - (NSString *)name {
        NSString *name = objc_getAssociatedObject(self,"name");
        return name;
      }

  >关联对象由AssociationsManager管理，AssociationsManager的定义为

          class AssociationsManager {
            static OSSpinLock _lock;
            static AssociationsHashMap * _map;
          public:
             AssociationsManager() { OSSpinLockLock(& _lock);}
             ~AssociationsManager() { OSSpinLockLock (& _lock);}

             AssociationsHashMap &associations () {
               if (_map == NULL) {
                  _map =  new AssocationsHashMap();
               }
               return * _map;
             }  
          };

  >associationsManager里面是由一个静态的AssociationsHashMap来存储所有的关联对象，map的key值是关联对象的指针地址，value则是另外一个AssociationsHashMap,，里面保存了关联对象的key-value对。关联对象的销毁逻辑则是判断一个对象是否有关联对象，如果有，则销毁
## **消息转发**

消息转发分为两个阶段

> objc_msgSend

该方法主要进行了四项操作

1. 检测对应的Selector()是不是需要忽略

1. 检查对应的target是否为nil。如果为nil则检查是否有target为nil的处理方法，有则执行，没有则清理现场返回

1. 如果target不为nil，则在该class的缓存中查找方法对应的IMP实现，有则执行，没有则在方法列表里寻找，一直找到NSObject为止。若没找到，则执行第二阶段

> 消息转发

该阶段可以进行方法调用对象的替换，通过实现协议更改方法的实现

* category实现：将category中的实例方法，协议以及属性添加到类上，将category的类方法，协议添加到类的`meta classs`上。调用一个category方法时，首先在类的方法列表中寻找方法的实现，若没有则在类的meta  class方法列表中寻找，再没有便在meta class的super class方法列表中寻找,直到找到NSObject的方法列表为止

## **runtime应用及注意事项**

### 应用

1. 实现多继承

1. Method Swizzling

1. Aspect Oriented Programming

1. isa Swizzling

1. Associated Objected管理对象

1. 动态增加方法

1. NSCoding的自动归档与解档

1. 字典和模型互相转换

* 利用消息转发实现“多继承”

### 注意事项

* 多继承

NSObject中的自省方法不会响应通过消息转发实现的多继承

* Method Swizzling

Method Swizzling应该在 +load方法中执行，Method Swizzling应该在dispath once中执行，Method Swizzling执行时不应该调用`[super load]`方法

**Method Swizzling模版**
  ![](/Users/maomaoshou/Documents/notes/assets/l.png)
# Runtime 

## 基本概念
1. SEL作为IMP的key值存放在NSSet中，通过hash快速查询方法就可以根据SEL找到IMP的具体实现函数。同一个类中不能有两个相同方法，就算参数类型不同也不可以。不同类可以有相同方法。SEL本质是一个NSString。
1. Cache用来缓存经常调用的方法，当调用方法时，优先在Cache中查找，如果没有找到，再在method list中查找
1. IMP是一个指向函数实现的函数指针
1. @selector选择只与SEL有关
1. 实例对象，父类，元类三者关系图
![](/Users/maomaoshou/Documents/notes/assets/metaClass.png)
## 源码
1. NSObject的定义
```
typedef struct objc_class *Class;

@interface NSObject <NSObject> {
  Class isa OBJC_ISA_AVAILABILITY
}
```
1. objc_class的定义
```
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
```
1. objc_class的定义
```
struct objc_object {
  private:
       isa_t isa
}

struct objc_class : objc_object {
  // Class ISA;
  Class superclass;
  cache_t cache;
  class_data_bits_t bits;
}

union isa_t
{
  isa_t() {}
  isa_t(uintptr_t value) : bits(value) { }
  Class cls;
  uintptr_t bits;

  Class cls;
  uintptr_t bits;


  # if __arm64__
#   define ISA_MASK        0x0000000ffffffff8ULL
#   define ISA_MAGIC_MASK  0x000003f000000001ULL
#   define ISA_MAGIC_VALUE 0x000001a000000001ULL
    struct {
        uintptr_t indexed           : 1;
        uintptr_t has_assoc         : 1;
        uintptr_t has_cxx_dtor      : 1;
        uintptr_t shiftcls          : 33; // MACH_VM_MAX_ADDRESS 0x1000000000
        uintptr_t magic             : 6;
        uintptr_t weakly_referenced : 1;
        uintptr_t deallocating      : 1;
        uintptr_t has_sidetable_rc  : 1;
        uintptr_t extra_rc          : 19;
#       define RC_ONE   (1ULL<<45)
#       define RC_HALF  (1ULL<<18)
    };

# elif __x86_64__
#   define ISA_MASK        0x00007ffffffffff8ULL
#   define ISA_MAGIC_MASK  0x001f800000000001ULL
#   define ISA_MAGIC_VALUE 0x001d800000000001ULL
    struct {
        uintptr_t indexed           : 1;
        uintptr_t has_assoc         : 1;
        uintptr_t has_cxx_dtor      : 1;
        uintptr_t shiftcls          : 44; // MACH_VM_MAX_ADDRESS 0x7fffffe00000
        uintptr_t magic             : 6;
        uintptr_t weakly_referenced : 1;
        uintptr_t deallocating      : 1;
        uintptr_t has_sidetable_rc  : 1;
        uintptr_t extra_rc          : 8;
#       define RC_ONE   (1ULL<<56)
#       define RC_HALF  (1ULL<<7)
    };
}
```
> 所有OC类和对象在runtime层都是用struct表示的

> 类结构体中包括父类指针superclass，isa(isa_t,因为objc_class继承自objc_object,所以也会有isa)，实例方法结构体class_data_bits_t

> 关于isa_t，可以通过[isa解析](https://github.com/draveness/analyze/blob/master/contents/objc/%E4%BB%8E%20NSObject%20%E7%9A%84%E5%88%9D%E5%A7%8B%E5%8C%96%E4%BA%86%E8%A7%A3%20isa.md)了解

> 关于class_data_bits_t,可以通过[ObjC中方法的结构](https://github.com/draveness/analyze/blob/master/contents/objc/%E6%B7%B1%E5%85%A5%E8%A7%A3%E6%9E%90%20ObjC%20%E4%B8%AD%E6%96%B9%E6%B3%95%E7%9A%84%E7%BB%93%E6%9E%84.md#%E6%B7%B1%E5%85%A5%E8%A7%A3%E6%9E%90-objc-%E4%B8%AD%E6%96%B9%E6%B3%95%E7%9A%84%E7%BB%93%E6%9E%84)
## 应用

### Class及isa指针
* **super**仅仅作为一个编译标志符，告诉编译器去调用父类的方法,但是最后的调用者是self本身，而不是super class。如果要获取到父类，使用`[self superclass]`,

* [A isKindOfClass:B]执行过程：

  取A的`meta class`和B比较，若不同，则取meta class的super class和B比较，依次循环(**类NSObject的元类的super class为NSObject类本身**)

* [A isMemberOfClass:B]执行过程：

  取**A**的`meta cla ss`和**B**比较。

* kvo实现原理，将被观察者的`isa`指针指向一个新类，该类继承了被观察者并且重写了被观察者的属性的set方法
### 消息发送

消息发送分为两个阶段

> Messaging阶段————objc_msgSend

该方法主要进行了四项操作

1. 检测对应的Selector()是不是需要忽略

1. 检查对应的target是否为nil。如果为nil则检查是否有target为nil的处理方法，有则执行，没有则清理现场返回

1. 如果target不为nil，则在该class的缓存中查找方法对应的IMP实现，有则将该方法加入方法缓存列表然后执行该方法，没有则沿着继承链在父类方法列表里寻找，一直找到NSObject为止(oc中NSObject为除NSProxy外所有类的父类),若依然没找到方法，则会尝试`_class_resolveMethod(类或者实例)`方法，如果该方法有实现，则继续开始方法寻找过程，若该方法没有实现，则执行消息转发阶段

> 消息转发————Message Forwarding

该阶段可以进行方法调用对象的替换，通过实现协议更改方法的实现，大致步骤有：
1. 调用`forwardingTargetForSelector`方法选择备援消息接收者
1. runtime向对象发送`methodSignatureForSelector`消息，取得返回的方法签名用于生成`NSInvocation`对象，
1. 开发者可以重写`forwardInvocation`来实现自己的转发逻辑
## **runtime应用及注意事项**

### 综合应用

1. Method Swizzling

1. Aspect Oriented Programming

1. isa Swizzling

1. Associated Objected管理对象

1. 动态增加方法

1. NSCoding的自动归档与解档

1. 字典和模型互相转换

1. 利用消息转发实现“多继承”

## 注意事项

* 多继承

NSObject中的自省方法不会响应通过消息转发实现的多继承，如果需要响应，需自己重写`respondToSelector`方法

* Method Swizzling

**Method Swizzling模版**
  ![](/Users/maomaoshou/Documents/notes/assets/l.png)
  注意：
  1. ![](/Users/maomaoshou/Documents/notes/assets/load&initialize.png)
   initialize方法有可能不会被调用，所以需要在load方法中进行method swizzling相关操作
   1. dispath_once,应该保证method swizzling方法只被调用一次
   1. `Selectors`,`Methods`and`Implementations`。三者之间的关系可以概括为：a class(`Class`)maintains a dispath table to resolve message sent at runtime;each entry in the table is a method(`Method`),which keys a particular name,the selector(`SEL`),to an implementation(`IMP`),which is a pointer to an underlying C function
   1. didAddMethod参数存在的意义:`class_getInstanceMethod`方法会沿着继承链寻找方法实现，如果子类没有实现方法，得到的是父类的实现，此时直接调用`class_exchageImplementations`方法后经过swizzle的方法去调用原方法会引发unrecognizer崩溃。例如

   ![](/Users/maomaoshou/Documents/notes/assets/swizzle_didaddmethod_example.png)
  ## Category

### Category作用

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

### Category实质

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

### Category加载

  加载过程主要进行的操作

  * 把category的实例方法，协议以及属性添加到类上

  * 把category的类方法和协议添加到类的metaclass上

   >需要注意的是category中的方法名如果和类本身的方法名重复，两个方法都会被加到类上，但是先加的是category中的方法，后加的是类本身的方法，所以会存在“category方法覆盖”（运行时在查找方法时是按照方法列表的顺序查找的）

### Category和load方法

    如果在category中实现了load方法，那么category和类本身的load方法都会被调用，调用顺序遵循编译顺序，即先调用Category中的load方法，再调用类本身的load方法

### 利用关联对象为Category添加属性

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
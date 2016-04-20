# 05 @property和@synthesize

Tags： Objective-C

----

## 5.1 @property和@synthesize规则

+ **@property声明属性a && 而无@synthesize实现**

> 这种情况会使用_a作为属性a的成员变量，如果没有定义_a则会自动生成一个private的_a（如果本来定义了成员变量a，那么此时_a跟a没有任何关系）。

+ **@property声明属性a && @synthesize a实现**

> 这种情况会使用a作为属性a的成员变量，如果没有定义a则会自动生成一个private的a（如果本来定义了成员变量_a，那么此时_a和a没有任何关系）。

+ **@property声明属性a && @synthesize=_a实现**

> 这种情况已经指定了属性a的成员变量是_a

一般情况下，实际开发中要么直接@property声明属性而无@synthesize实现，要么直接声明属性的同时指定使用的成员变量，也即第一种或第三种情况。


下面有个代码示例
**Person.h**
```objective-c
#import <Foundation/Foundation.h>

@interface Person : NSObject {
    @public
    NSString *birthday;
    NSString *_position;
    NSString *_degress;
}

@property NSString *birthday;
@property NSString *position;
@property NSString *degress;
@property NSString *education;
@property float weight;

- (void) printInfo;

@end
```

**Person.m**
```objective-c
#import "Person.h"

@implementation Person

@synthesize birthday;//此时会生成一个private birthday成员变量，如果有就使用已有的birthday变量
@synthesize position;//此时会生成一个private position成员变量，与本身定义的_position无关系
@synthesize degress = _degress;//此时使用已有的_degress成员变量
@synthesize  education;//此时会生成一个private education成员变量
//因为只有property声明而没有synthesize weight，此时会生成一个private _weight成员变量

- (void) printInfo {
    NSLog(@"_weight=%.2f", _weight);
    NSLog(@"education=%@", education);
    NSLog(@"_degress=%@", _degress);
}

@end
```

**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {
    Person *p = [[Person alloc] init];
    p->birthday = @"1987-08-20";
    p.birthday = @"1986-08-08";
    
    p->_position = @"developer";
    p.position = @"architect";
    
    p.degress =  @"undergraduate";
    
    p.education = @"university";
    
    p.weight = 60.0;
    
    //结果:p->birthday=1986-08-08,p.birthday=1986-08-08
    NSLog(@"p->birthday=%@, p.birthday=%@", p->birthday, p.birthday);
    
    //结果：p->_position=developer,p.position=architect
    NSLog(@"p->_position=%@, p.position=%@", p->_position, p.position);
    
    //结果：p.weight=60.00
    NSLog(@"p.weight=%.2f", p.weight);
    
    /*结果：
     _weight=60.00
     education=university
     _degress=undergraduate*/
    [p printInfo];
    
    return 0;
}
```

----

## 5.2 @property的修饰符

+ **getter和setter别名**：setter  getter

> 对类的getter和setter方法定义别名，这里用wawa代替了price方法，kaka:代替了setPrice:方法，price和setPrice:失效。如修改price可以用`[对象 kaka:新值]`修改。
`@property (nonatomic, assign, getter = wawa, setter = kaka:) int price;`

+ **原子性**：nonatomic | atomic(默认)

> atomic：表示操作是原子的，只有一个线程访问成员变量，是线程安全的，但是比较影响效率，较少使用。
nonatomic：表示操作是非原子的，可以被多个线程访问。效率比atomic快，一般都使用nonatomic。

+ **存取性控制**：readonly | readwrite(默认)

> readonly：表示只有getter而没有setter，只能访问不能修改成员变量。
> readwrite：表示既有getter又有setter，能访问同时也能修改成员变量。

+ **内存管理**：assign(默认）| copy | 非ARC下的retain 

`NSString *pt = [[NSString alloc] initWithString:@"abc"];` 
上面一段代码会执行以下两个动作：
1 在堆上分配一段内存用来存储@"abc"。比如：内存地址为：0x1111,内容为 "abc"。
2 在栈上分配一段内存用来存储pt。比如：地址为：0xAAAA内容自然为0x1111。

**assign**：`NSString *newPt = [pt assign];`
此时newPt和pt的地址完全相同都是0xAAAA，内容为0x1111。也即newPt只是pt的别名，对任何一个操作就等于对另一个操作，因此retainCount不需要增加。
![屏幕快照 2015-11-28 23.39.15.png-118.4kB][1]
**copy**：`NSString *newPt = [pt copy];`
此时会在堆上重新开辟一段内存存放@"abc"，比如0x1122，内容为@"abc，同时会在栈上为newPt分配空间，比如地址：0xAACC，内容为0x1122。因此retainCount增加1供newPt来管理0x1122这段内存。
![屏幕快照 2015-11-28 23.39.22.png-158.6kB][2]
**retain**：`NSString *newPt = [pt retain];`
此时newPt的地址不再为0xAAAA，可能为0xAABB但是内容依然为0x1111。因此newPt和pt都可以管理"abc"所在的内存。因此 retainCount需要增加1。
![屏幕快照 2015-11-28 23.39.28.png-193.5kB][3]


 assign：表示对属性进行简单赋值。一般适用于基础数据类型如NSInteger、int、float、double、结构体等类型的成员变量。
`@property (nonatomic, assign) int price;`

 copy：表示当调用setter方法对成员变量赋值时，会将被赋值的对象进行一次复制，得到一个副本对象，再将该副本对象赋值给成员变量。一般适用于NSString *成员变量。
`@property (nonatomic, copy) NSString *name;`
代码示例如下：

**GKHBook.h**
```objective-c
#import <Foundation/Foundation.h>

@interface GKHBook : NSObject

@property (nonatomic) NSString *name;//默认是assign的
@property (nonatomic, copy) NSString *year;
@end
```

**GKHBook.m**
```objective-c
#import "GKHBook.h"
@implementation GKHBook

@end
```

**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHBook.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        GKHBook *book = [[GKHBook alloc] init];
        NSMutableString *str1 = [NSMutableString stringWithString:@"《iOS编程》"];
        [book setName:str1];//在调用setName:方法时_name成员变量进行简单赋值，指向str1指向的对象
        NSLog(@"book的name为：%@", [book name]);//结果：book的name为：《iOS编程》
        [str1 appendString:@"是一本好书"];//修改被赋值的对象，同时修改的就是成员变量
        NSLog(@"book的name为：%@", [book name]);//结果：book的name为：《iOS编程》是一本好书
        
        NSMutableString *str2 = [NSMutableString stringWithString:@"2012年9月"];
        [book setYear:str2];//在调用setYear:方法时，将str2指向的对象进行复制拷贝，然后_year成员变量指向该副本对象，此时str2指向对象和_year指向的对象不是同一个
        NSLog(@"book的year为：%@", [book year]);//结果：book的year为：2012年9月
        [str2 appendString:@"有30天"];//修改str2指向的对象对book的year属性不起作用
        NSLog(@"book的year为：%@", [book year]);//结果：book的year为：2012年9月
    }
    return 0;
}
```
retain：在非ARC下使用，不能包含在@autoreleasepool块内使用。在setter方法时，会将成员变量指向的对象变成被赋值的对象，也即使被赋值的对象引用计数+1。一般适用于NSObject及其子类。

**GKHWin.h**
```objective-c
#import <Foundation/Foundation.h>

@interface GKHWin : NSObject
@property (nonatomic, retain) NSDate *date;
@end
```
**GKHWin.m**
```objective-c
#import "GKHWin.h"

@implementation GKHWin

@end
```
**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHWin.h"

int main(int argc, const char * argv[]) {
    /*必须关闭ARC，retain才起作用
    @autoreleasepool {
    }
    */
    
    GKHWin *win = [[GKHWin alloc] init];
    NSDate *date = [[NSDate alloc] init];
    //初始化date后，其引用计数为1
    NSLog(@"date的引用计数为：%ld", date.retainCount);
    //在setter方法时，赋值导致date的引用计数+1
    [win setDate:date];
    //下面输出的引用计数为2
    NSLog(@"[win date]的引用计数为：%ld", [win date].retainCount);
    //释放date的引用计数，-1，其实也是释放[win date]的引用计数
    [date release];
    //下面输出的引用计数为1
    NSLog(@"[win date]的引用计数为：%ld", [win date].retainCount);
    
    return 0;
}
```

+ **内存管理**：ARC下的(strong | weak | unsafe_unretained)

在ARC模式下，编译器会自动的在适当的地方插入合适的retain、release、autorelease语句来达到内存管理的效果，不需要我们去手动管理。

strong：只要被赋值的对象还有strong指针指向它，那么它就不会被销毁,strong指针是该对象的拥有者。当最后一个指向该对象的strong指针不再指向它时，那么该对象将被销毁，并且所有指向该对象的week指针都将被置为nil，防止野指针。
相当于retain的作用

weak：被赋值的对象有weak指针指向它，该对象也可能被释放，weak指针只是对象的使用者，没有拥有权。week指针引用的对象被销毁后，所有的week指针置为nil。
相当于assign的作用

unsafe_unretained：与week相似，对于被赋值的对象有unsafe_unretained指向它，该对象也可能被销毁；与week不同的地方在于，unsafe_unretained指针引用的对象被销毁后，unsafe_unretained指针不会置为nil.


  [1]: http://static.zybuluo.com/gkh178/a8ks0ufaxoe50z45lul0i9jo/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-11-28%2023.39.15.png
  [2]: http://static.zybuluo.com/gkh178/fffc4l7eq67t5po8f7zq5ls4/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-11-28%2023.39.22.png
  [3]: http://static.zybuluo.com/gkh178/t4mxdd10ysfrl2ckwgyazdm6/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-11-28%2023.39.28.png
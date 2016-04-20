# 06 KVC与KVO

Tags:Objective-C

---

## 6.1 KVC

KVC(Key Value Coding)：键值编码，是一种修改对象的属性和访问对象的属性的技术，当然我们也可以用 对象名->成员变量名 或者 对象名.属性名 来修改或者访问属性。

下面贴个代码例子：
**GKHBook.h**
```objective-c
#import <Foundation/Foundation.h>

@interface GKHBook : NSObject

@property (nonatomic, copy) NSString *bookName;//书名
@property (nonatomic, assign) int price;//价格
@end
```
**GKHBook.m**
```objective-c
#import "GKHBook.h"

@implementation GKHBook

@end
```

**GKHUser.h**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHBook.h"

@interface GKHUser : NSObject {
    @package
    NSString *habit;//爱好
    NSString *_habit;
    
}

@property (nonatomic, copy) NSString *name;//姓名
@property (nonatomic, copy) NSString *pass;//密码
@property (nonatomic, copy) NSDate *birth;//生日
@property (nonatomic, assign) int teethNumber;//牙齿数
@property (nonatomic, strong) GKHBook *book;//购买的书
@property (nonatomic, assign) int bookAmount;//购买的书的数量

@end
```
**GKHUser.m**
```objective-c
#import "GKHUser.h"

@implementation GKHUser {
    int age; //年龄
}

//重写setValue:forUndefinedKey:方法
- (void) setValue:(id)value forUndefinedKey:(NSString *)key {
    NSLog(@"你尝试设置的key：%@ 并不存在", key);
    NSLog(@"你尝试设置的value为：%@", value);
}

//重写valueForUndefinedKey:方法
 - (void) valueForUndefinedKey:(id)key {
 NSLog(@"你尝试访问的key：%@ 并不存在", key);
 }

//重写setNilValueForKey:方法，可以对不同的key做不同的处理
- (void) setNilValueForKey:(NSString *)key {
    //如果key为teethNumber的属性值设置为nil
    if ([key isEqualToString:@"teethNumber"]) {
        //nil代表0
        self.teethNumber = 0;
    }
    else {
        //调用父类的setNilValueForKey:方法
        [super setNilValueForKey:key];
    }
}

@end
```
**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHUser.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        GKHUser *user = [[GKHUser alloc] init];
        //使用KVC方式为name、pass、birth属性设置属性值
        [user setValue:@"孙悟空" forKey:@"name"];
        [user setValue:@"123456" forKey:@"pass"];
        [user setValue:[[NSDate alloc] init] forKey:@"birth"];
        
        //使用KVC方式访问name、pass、birth属性值
        NSLog(@"user的name属性为：%@", [user valueForKey:@"name"]);
        NSLog(@"user的pass属性为：%@", [user valueForKey:@"pass"]);
        NSLog(@"user的birth属性为：%@", [user valueForKey:@"birth"]);
        
        //使用KVC方式为为属性赋值，KVC对属性赋值的搜索顺序是：
        //①set属性名: 方法 ②_属性名 成员变量 ③属性名 成员变量  在②③中不管成员变量的修饰符是什么也不论其在interface部分还是implement部分
        //④如果①②③都没找到，系统将会调用对象的setVale:forUndefinedKey:方法，该方法将引起一NSUnknownKeyException异常导致程序结束
        [user setValue:@"football" forKey:@"habit"];
        [user setValue:[NSNumber numberWithInt:18] forKey:@"age"];
        
        //使用KVC方式访问属性值，搜索顺序是：
        //①属性名 方法 ②_属性名 成员变量 ③属性名 成员变量  在②③中不管成员变量的修饰符是什么也不论其在interface部分还是implement部分
        //④如果①②③都没找到，系统将会调用对象的valueforUndefinedKey:方法，该方法将引起一个NSUnknownKeyException异常导致程序结束
        NSLog(@"user->habit：%@", user->habit);//null
        NSLog(@"user->_habit：%@", user->_habit);//football
        NSLog(@"user的age为：%@", [user valueForKey:@"age"]);//18
        
        //当对一些属性设置为nil时，如对NSString类型的name是合法的，但是一些不接受nil值的属性，比如对int类型的teethNumber设置为nil
        //将会调用系统的setNilValueForKey:方法，该方法将引起一个NSInvalidArgumentException异常
        //重写setNilValueForKey:方法对一些不接受nil类型的key做处理，这里对对teethNumber设置为nil将变为0
        [user setValue:nil forKey:@"teethNumber"];
        NSLog(@"user的teethNumber属性为：%@", [user valueForKey:@"teethNumber"]);
        
        //使用KVC方式设置复合属性，这里对book属性下的price和bookName赋值
        [user setValue:[[GKHBook alloc] init]forKey:@"book"];
        [user setValue:@"20" forKeyPath:@"book.price"];
        [user setValue:@"《iOS编程》" forKeyPath:@"book.bookName"];
        [user setValue:@"30" forKey:@"bookAmount"];
        NSLog(@"user订了书名为：%@，价格为：%@，数量为：%@", [user valueForKeyPath:@"book.bookName"],
              [user valueForKeyPath:@"book.price"], [user valueForKey:@"bookAmount"]);
        
        //使用KVC方式为不存在的属性赋值，如果我们不重写继承自NSObject的setVale:forUndefinedKey:方法将导致程序异常
        //这里我们在GKHUser.m中重写了setVale:forUndefinedKey:方法
        [user setValue:@"red" forKey:@"color"];
        //使用KVC方式访问不存在的属性，如果我们不重写继承自NSObject的valueForUndefinedKey:方法将导致程序异常
        //这里我们在GKHUser.m中重写了valueForUndefinedKey:方法
        NSLog(@"user的color属性为：%@", [user valueForKey:@"color"]);
    }
    return 0;
}
```
运行结果：
> user的name属性为：孙悟空
user的pass属性为：123456
user的birth属性为：2015-11-30 09:59:38 +0000
user->habit：(null)
user->_habit：football
user的age为：18
user的teethNumber属性为：0
user订了书名为：《iOS编程》，价格为：20，数量为：30
你尝试设置的key：color 并不存在
你尝试设置的value为：red
你尝试访问的key：color 并不存在

## 6.2 KVO
KVO(Key Value Observing)：键值监听。它提供一种机制，当指定的对象的属性被修改后，则对象就会接受到通知。简单的说就是每次指定的被观察的对象的属性被修改后，KVO就会自动通知相应的观察者了。
一般实现步骤分为三步：
1. 添加监听器，并将被观察的对象中需要监视的属性注册到监听器，通过`addObserver:forKeyPath:options:context:`方法设置。
2. 实现回调方法，通过重写继承自NSObject对象的`observeValueForKeyPath:ofObject:change:context:`方法，来实现属性发生修改后要完成的功能。
3. 删除监听器，在观察者对象中`delloc`方法调用`removeObserver:forKeyPath`来删除每一个属性的监听器。

下面有个例子，代码中GKHBook类还是6.1中一样，其余代码如下：
**GKHBookView.h**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHBook.h"

@interface GKHBookView : NSObject

@property (nonatomic, weak) GKHBook *book;
- (void) showBookInfo;
@end
```
**GKHBookView.m**
```objective-c
/*
 GKHBook是数据模型组件，包含一些数据，具有属性
 GKHBookVeiw是视图模型组件，包含需要使用的数据模型，当数据模型组件发生属性变化时，能通知视图模型组件
 */
#import "GKHBookView.h"

@implementation GKHBookView

- (void) showBookInfo {
    NSLog(@"book名为：%@，价格为：%d", self.book.bookName, self.book.price);
}

//自定义setBook:方法
- (void) setBook:(GKHBook *)book {
    self->_book = book;
    //在self上为book添加监听器，监听book的bookName属性的改变
    [self.book addObserver:self forKeyPath:@"bookName" options:NSKeyValueObservingOptionNew context:nil];
    //在self上为book添加监听器，监听book的price属性的改变
    [self.book addObserver:self forKeyPath:@"price" options:NSKeyValueObservingOptionNew context:nil];
}

//重写observeValueForKeyPath:方法，当被监听的数据模型发生改变时，就会回调监听器的该方法
- (void) observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSString *,id> *)change context:(void *)context {
    NSLog(@"--observeValueForKeyPath:方法被调用--");
    //获取属性被修改时的数据
    NSLog(@"被修改的keyPath为：%@", keyPath);
    NSLog(@"被修改的对象为：%@", object);
    NSLog(@"新被修改的属性值为：%@", [change objectForKey:@"new"]);
    NSLog(@"被修改的上下文为：%@", context);
}

- (void) delloc {
    //删除self上的监听器
    [self.book removeObserver:self forKeyPath:@"bookName"];
    [self.book removeObserver:self forKeyPath:@"price"];
}
@end
```
**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHBookView.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建GKHBook对象
        GKHBook *book = [[GKHBook alloc] init];
        //设置book的属性bookName price
        book.bookName = @"《iOS编程》";
        book.price = 89;
        //创建GKHBookView对象
        GKHBookView *view = [[GKHBookView alloc] init];
        view.book = book;
        [view showBookInfo];
        //修改book对象的属性，将会激发view中的监听器方法
        book.bookName = @"《iOS编程第四版》";
        book.price = 99;
    }
    return 0;
}
```
运行结果为：
> book名为：《iOS编程》，价格为：89
--observeValueForKeyPath:方法被调用--
被修改的keyPath为：bookName
被修改的对象为：<GKHBook: 0x1001082b0>
新被修改的属性值为：《iOS编程第四版》
被修改的上下文为：(null)
--observeValueForKeyPath:方法被调用--
被修改的keyPath为：price
被修改的对象为：<GKHBook: 0x1001082b0>
新被修改的属性值为：99
被修改的上下文为：(null)




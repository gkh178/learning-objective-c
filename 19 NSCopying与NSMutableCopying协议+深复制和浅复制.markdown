# 19 NSCopying与NSMutableCopying协议+深复制和浅复制

Tags: Objective-C

---
## 19.1 NSCopying与NSMutableCopying协议
在018章，我们举例了NSString对象和NSMutableString对象的copy及mutableCopy方法，用copy方法能得到字符串的不可变副本，而mutableCopy方法能得到字符串的可变副本。
但是如果对自定义的类，我们不能直接使用copy和mutableCopy方法，需要让类遵守NSCopying与NSMutableCopying协议，然后实现继承自协议的copyWithZone:方法和mutableCopyWithZone:方法，这两个方法返回的是对象本身，得到一个对象的副本。

总之，为了保证一个自定义的类的对象使用copy方法产生一个不可变的副本，需要做两步：
1. 让该类继承NSCopying协议。
2. 在类的代码实现部分重写继承自NSCopying协议的CopyWithZone:方法，该方法返回一个该类的不可变对象副本。

为了保证一个自定义的类的对象使用mutableCopy方法产生一个可变的副本，需要做两步：
1. 让该类继承NSMutableCopying协议。
2. 在类的代码实现部分重写继承自NSMutableCopying协议的mutableCopyWithZone:方法，该方法返回一个该类的可变对象副本。


## 19.2 深复制和浅复制
代码示例如下：
**GKHUser.h**
```objective-c
#import <Foundation/Foundation.h>

//继承NSCopying和NSMutableCopying协议
@interface GKHUser : NSObject <NSCopying, NSMutableCopying>
@property (nonatomic, strong) NSMutableString *name;//类似retain类型，引用计数
@property (nonatomic, assign) int age;
@end
```
**GKHUser.m**
```objective-c
#import "GKHUser.h"

@implementation GKHUser

//重写copyWithZone:方法
- (id) copyWithZone:(NSZone *)zone {
    NSLog(@"执行copyWithZone:方法");
    //使用zone参数创建一个GKHUser对象
    GKHUser *user = [[[self class] allocWithZone:zone] init];
    
    user.name = self.name;//这里只是浅复制
    user.age = self.age;
    return user;
}

//重写mutableCopyWithZone:方法
- (id) mutableCopyWithZone:(NSZone *)zone {
    NSLog(@"执行mutableCopyWithZone:方法");
    //使用zone参数创建一个GKHUser对象
    GKHUser *user = [[[self class] allocWithZone:zone] init];
    
    user.name = self.name;//这里只是浅复制
    user.age = self.age;
    return user;
}
@end
```
**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHUser.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        GKHUser *user1 = [GKHUser new];
        user1.name = [NSMutableString stringWithString:@"李太"];
        user1.age = 10;
        
        GKHUser *user2 = [user1 copy];//复制不可变副本
        [user1.name appendString:@"白"];
        user2.age = 20;
        
        NSLog(@"user1的名字为：%@", user1.name);
        NSLog(@"user1的年龄为：%d", user1.age);
        NSLog(@"user2的名字为：%@", user2.name);
        NSLog(@"user2的年龄为：%d", user2.age);
        
        GKHUser *user3 = [GKHUser new];
        user3.name = [NSMutableString stringWithString:@"张大"];
        user3.age = 30;
        
        GKHUser *user4 = [user3 mutableCopy];//复制可变副本
        [user3.name appendString:@"彪"];
        user4.age = 40;
        
        NSLog(@"user3的名字为：%@", user3.name);
        NSLog(@"user3的年龄为：%d", user3.age);
        NSLog(@"user4的名字为：%@", user4.name);
        NSLog(@"user4的年龄为：%d", user4.age);
        
    }
    return 0;
}
```
运行main.m结果：
> 执行copyWithZone:方法
user1的名字为：李太白
user1的年龄为：10
user2的名字为：李太白
user2的年龄为：20
执行mutableCopyWithZone:方法
user3的名字为：张大彪
user3的年龄为：30
user4的名字为：张大彪
user4的年龄为：40

我们从结果中发现，不管是可变复制还是不可变复制，最后修改副本的name属性时候，会导致原始对象的属性也发生修改，为什么呢？因为这里的copyWithZone:和mutableWithZone:方法中对name属性的赋值是浅复制，如下图：

![屏幕快照 2015-12-02 23.35.12.png-460.1kB][1]

为了达到我们的效果，GKHUser.h和main.m代码不变，而**GKHUser.m**修改如下：
**GKHUser.m**
```objective-c
#import "GKHUser.h"

@implementation GKHUser

//重写copyWithZone:方法
- (id) copyWithZone:(NSZone *)zone {
    NSLog(@"执行copyWithZone:方法");
    //使用zone参数创建一个GKHUser对象
    GKHUser *user = [[[self class] allocWithZone:zone] init];
    
    user.name = [self.name copy];//这里对name指向的对象执行深复制，复制的副本为不可变的字符串
    user.age = self.age;
    return user;
}

//重写mutableCopyWithZone:方法
- (id) mutableCopyWithZone:(NSZone *)zone {
    NSLog(@"执行mutableCopyWithZone:方法");
    //使用zone参数创建一个GKHUser对象
    GKHUser *user = [[[self class] allocWithZone:zone] init];
    
    user.name = [self.name mutableCopy];//这里对name指向的对象执行深复制，复制的副本为可变的字符串
    user.age = self.age;
    return user;
}
@end
```
运行main.m结果：
> 执行copyWithZone:方法
user1的名字为：李太白
user1的年龄为：10
user2的名字为：李太
user2的年龄为：20
执行mutableCopyWithZone:方法
user3的名字为：张大彪
user3的年龄为：30
user4的名字为：张大
user4的年龄为：40

这样就达到了我们的目的，user2是user1的不可变副本，实现了copy方法，而user4是user3的可变副本，实现了mutableCopy方法。
其在内存中的示意图如下：

![屏幕快照 2015-12-02 23.52.59.png-609.7kB][2]


  [1]: http://static.zybuluo.com/gkh178/d0p3mgmrrhez77rpjwqmhq92/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-02%2023.35.12.png
  [2]: http://static.zybuluo.com/gkh178/foprc7vu4qhqr3tldn0dp3xj/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-02%2023.52.59.png
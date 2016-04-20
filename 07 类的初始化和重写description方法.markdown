# 07 类的初始化和重写description方法

Tags: Objective-C

-----

在定义一个类的时候，我们通常要定义构造方法，而一般来说构造方法分为两种，以init开头的实例构造方法以及类名开头的类构造方法，后者也称静态构造方法。下面是代码实例：

**Person.h**
```objective-c
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property NSString *name;
@property int age;

//自定义类的静态构造方法
+ (id) personWithName:(NSString *) name andAge:(int) age;
//自定义类的实例构造方法
- (id) initWithName:(NSString *) name andAge:(int) age;
//重写description方法，如果不重写会默认返回 Person:0x内存地址
- (NSString *) description;

@end
```
**Person.m**
```objective-c
#import "Person.h"

@implementation Person

- (id) initWithName:(NSString *)name andAge:(int)age {
    if (self = [super init]) {
        self.name = name;
        self.age = age;
    }
    return self;
}

+ (id) personWithName:(NSString *)name andAge:(int)age {
    Person *p = [[Person alloc] initWithName:name andAge:age];
    return p;
}

- (NSString *) description {
    return [NSString stringWithFormat:@"{name:%@, age:%i}", self.name, self.age];
}

@end
```

**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {
    Person *p = [[Person alloc] initWithName:@"Kin" andAge:28];
    /*结果：
     {name:Kin,age:28}
     */
    NSLog(@"%@", p);//此时会调用对象的description方法返回描述信息
    
    Person *p1 = [[Person alloc] initWithName:@"Kenshin" andAge:28];
    NSLog(@"name=%@,age=%i", p1.name, p1.age);
    //结果：name=Kenshin,age=28
    
    Person *p2 = [Person personWithName:@"Kaoru" andAge:27];
    NSLog(@"name=%@,age=%i", p2.name, p2.age);
    //结果：name=Kaoru,age=27
    
    return 0;
}
```




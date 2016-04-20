# 22 Foundation框架之NSDictionary和NSMutableDictionary

Tags: Objective-C

---
NSDictionary也是一种集合，跟NSSet不一样的地方在于它是key-value键值对，可以通过key索引来添加或者删除value。在一个NSDictionary对象中，每一个key必须是唯一的，但是不同的key对应的value可以相同，用key-value共同判定一个NSDictionary对象中是佛有跟它相同的元素。

## 22.1 NSDictionary的使用
代码实例如下：

**GKHUser.h**
```objective-c
#import <Foundation/Foundation.h>

@interface GKHUser : NSObject
@property (nonatomic, copy) NSString* name;
@property (nonatomic, copy) NSString* pass;

- (id) initWithName:(NSString *)aName pass:(NSString *)aPass;
- (void) say:(NSString *)content;
- (NSComparisonResult) compare:(GKHUser *)aUser;
@end
```

**GKHUser.m**
```objective-c
#import "GKHUser.h"

@implementation GKHUser

//类的初始化
- (id) initWithName:(NSString *)aName pass:(NSString *)aPass {
    if (self = [super init]) {
        self.name = aName;
        self.pass = aPass;
    }
    return self;
}

- (void) say:(NSString *)content {
    NSLog(@"%@说：%@", self.name, content);
}

//重写继承自NSObject的isEqual:方法
- (BOOL) isEqual:(id)object {
    if (self == object) {
        return YES;
    }
    if ([object class] == [self class]) {
        GKHUser *target = (GKHUser *) object;
        return [self.name isEqualToString:target.name] && [self.pass isEqualToString:target.pass];
        
    }
    return NO;
}

//重写继承自NSObject的description方法
- (NSString *) description {
    return [NSString stringWithFormat:@"<GKHUser[name=%@, pass=%@]>", self.name, self.pass];
}

//重写继承自NSObject的hash方法
- (NSInteger) hash {
    NSLog(@"====hash====");
    NSInteger nameHash = (self.name == nil ? 0 : [self.name hash]);
    NSInteger passHash = (self.pass == nil ? 0 : [self.pass hash]);
    return nameHash * 31 + passHash;
}

- (NSComparisonResult) compare:(GKHUser *)aUser {
    if ([aUser.name isGreaterThan:self.name]) {
        return NSOrderedDescending;
    }
    else if ([aUser.name isLessThan:self.name]) {
        return NSOrderedAscending;
    }
    else {
        return NSOrderedSame;
    }
}
@end
```
**NSDictionay+print.h**
```objective-c
#import <Foundation/Foundation.h>

//定义一个NSDictionary的category，里面包含一个print所有key-value的方法
@interface NSDictionary (print)
- (void) print;
@end
```
**NSDcitionary+print.m**
```objective-c
#import "NSDictionary+print.h"

@implementation NSDictionary (print)

- (void) print {
    NSMutableString *result = [NSMutableString stringWithString:@"{"];
    for (id key in self) {
        [result appendString:[key description]];
        [result appendString:@"="];
        [result appendString:[self[key] description]];
        [result appendString:@", "];
    }
    NSInteger len = [result length];//获取字符串的长度
    //去掉最后的,和空格
    [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
    [result appendString:@"}"];
    NSLog(@"%@", result);
}
@end
```
**NSDictionaryTest.m**
```objective-c
#import <Foundation/Foundation.h>
#import "NSDictionary+print.h"
#import "GKHUser.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //初始化一个NSDictionary,不同的key可以有相同的value，而key之间都必须不相同
        NSDictionary *d = [NSDictionary dictionaryWithObjectsAndKeys:
                             [[GKHUser alloc]initWithName:@"sun" pass:@"123"], @"one",
                             [[GKHUser alloc]initWithName:@"bai" pass:@"234"], @"two",
                             [[GKHUser alloc]initWithName:@"sun" pass:@"123"], @"three",
                             [[GKHUser alloc]initWithName:@"li" pass:@"456"], @"four",
                             [[GKHUser alloc]initWithName:@"zhao" pass:@"567"], @"five",
                             nil];
        NSDictionary *dict =[NSDictionary dictionaryWithDictionary:d];
        //输出dict
        [dict print];
        
        //dict包含了多少个key-value对
        NSLog(@"dict包含了%ld个key-value对", [dict count]);
        
        //返回dict包含的所有keys
        NSLog(@"dict包含的所有keys为：%@", [dict allKeys]);
        //返回dict的某个对象值所对应的所有的keys
        NSLog(@"dict中值为\"%@\"对应的所有keys为：%@", [[GKHUser alloc]initWithName:@"sun" pass:@"123"],
              [dict allKeysForObject:[[GKHUser alloc]initWithName:@"sun" pass:@"123"]]);
        
        //返回dict包含的所有values
        NSLog(@"dict包含的所有values为：%@", [dict allValues]);
        //返回dict中的某个key对应的value，使用vlueForKey:方法
        NSLog(@"dict中key为\"four\"所对应的value为：%@", [dict valueForKey:@"four"]);
        //返回dict中的某个key对应的value，使用objectForKey:方法
        NSLog(@"dict中key为\"four\"所对应的value为：%@", [dict objectForKey:@"four"]);
        
        //用NSDictionary的key枚举器枚举所有keys
        NSEnumerator *keyEnumerator = [dict keyEnumerator];
        id key;
        while (key= [keyEnumerator nextObject]) {
            NSLog(@"%@", key);
        }
        //用NSDictionary的value枚举器枚举所有values
        NSEnumerator *valueEnmuerator = [dict objectEnumerator];
        id value;
        while (value = [valueEnmuerator nextObject]) {
            NSLog(@"%@", value);
        }
        //使用Block函数对NSDictionary进行枚举
        [dict enumerateKeysAndObjectsUsingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
            NSLog(@"key的值为%@", key);
            [obj say:@"iOS编程"];
        }];
        NSLog(@"*****************************************");
        
        //根据value输出key的排序，使用匿名comparator方法
        NSArray *keyArr1 = [dict keysSortedByValueUsingComparator:^NSComparisonResult(id obj1, id obj2) {
            if ([[obj1 name ]length] > [[obj2 name]length]) {
                return NSOrderedDescending;
            }
            else if ([[obj1 name ]length] < [[obj2 name ]length]) {
                return NSOrderedAscending;
            }
            else {
                return NSOrderedSame;
            }
        }];
        NSLog(@"%@", keyArr1);
        //根据value输出key的排序，使用value自带的compare:方法做对比
        NSArray *keyArr2 = [dict keysSortedByValueUsingSelector:@selector(compare:)];
        NSLog(@"%@", keyArr2);
        NSLog(@"*****************************************");
        
        //根据value过滤满足条件的key
        NSSet *keySet = [dict keysOfEntriesPassingTest:^BOOL(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
            //返回value中name属性为sun的对应的key
            return (BOOL)([[obj name] isEqualToString:@"sun"]);
        }];
        NSLog(@"%@", keySet);
    }
    return 0;
}
```

运行结果为：
```
{one=<GKHUser[name=sun, pass=123]>, five=<GKHUser[name=zhao, pass=567]>, three=<GKHUser[name=sun, pass=123]>, two=<GKHUser[name=bai, pass=234]>, four=<GKHUser[name=li, pass=456]>}
dict包含了5个key-value对
dict包含的所有keys为：(
    one,
    five,
    three,
    two,
    four
)
dict中值为"<GKHUser[name=sun, pass=123]>"对应的所有keys为：(
    one,
    three
)
dict包含的所有values为：(
    "<GKHUser[name=sun, pass=123]>",
    "<GKHUser[name=zhao, pass=567]>",
    "<GKHUser[name=sun, pass=123]>",
    "<GKHUser[name=bai, pass=234]>",
    "<GKHUser[name=li, pass=456]>"
)
dict中key为"four"所对应的value为：<GKHUser[name=li, pass=456]> dict中key为"four"所对应的value为：<GKHUser[name=li, pass=456]>
one
five
three
two
four
<GKHUser[name=sun, pass=123]>
<GKHUser[name=zhao, pass=567]>
<GKHUser[name=sun, pass=123]>
<GKHUser[name=bai, pass=234]>
<GKHUser[name=li, pass=456]>
key的值为one
sun说：iOS编程
key的值为five
zhao说：iOS编程
key的值为three
sun说：iOS编程
key的值为two
bai说：iOS编程
key的值为four
li说：iOS编程
*****************************************
(
    four,
    one,
    three,
    two,
    five
)
(
    five,
    one,
    three,
    four,
    two
)
*****************************************
{(
    one,
    three
)}
```

------
## 22.2 NSMutableDictionary的使用
代码示例如下：
**NSMutableDictionary**
```objective-c
#import <Foundation/Foundation.h>
#import "NSDictionary+print.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个NSMUtableDictionary字典，因为是可变的，可以先指定容量
        NSMutableDictionary *dict =[NSMutableDictionary dictionaryWithCapacity:10];
        NSDictionary *d = [NSDictionary dictionaryWithObjectsAndKeys:
                           [NSNumber numberWithInt:78], @"iOS编程",
                           [NSNumber numberWithInt:58], @"Java编程",
                           [NSNumber numberWithInt:68], @"PHP编程",
                           [NSNumber numberWithInt:38], @"C编程",
                           [NSNumber numberWithInt:28], @"Python编程",nil];
        
        //把别的字典的实体都添加到NSMUtableDictionary
        [dict addEntriesFromDictionary:d];
        
        //用修改key对应的value，如果该key不存在，则添加该key-value
        [dict setObject:[NSNumber numberWithInt:59]forKey:@"Java编程"];
        dict[@"C++编程"] = [NSNumber numberWithInt:88];
        NSLog(@"dict为%@：",dict);
        
        //删除指定的value
        [dict removeObjectForKey:@"PHP编程"];
        //删除多个values
        [dict removeObjectsForKeys:[NSArray arrayWithObjects:@"C编程",@"Java编程",nil]];
        NSLog(@"dict为%@：",dict);
        //删除所有values
        [dict removeAllObjects];
        NSLog(@"dict为%@：",dict);
    }
    return 0;
}
```
运行结果如下：
```
dict为{
    "C++\U7f16\U7a0b" = 88;
    "C\U7f16\U7a0b" = 38;
    "Java\U7f16\U7a0b" = 59;
    "PHP\U7f16\U7a0b" = 68;
    "Python\U7f16\U7a0b" = 28;
    "iOS\U7f16\U7a0b" = 78;
}
dict为{
    "C++\U7f16\U7a0b" = 88;
    "Python\U7f16\U7a0b" = 28;
    "iOS\U7f16\U7a0b" = 78;
}
dict为{
}
```

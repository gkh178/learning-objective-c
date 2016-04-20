# 21 Foundation框架之NSSet、NSMutableSet、NSCountedSet、NSOrderedSet、NSMutableOrderedSet

Tags: Objective-C

---
+ `NSSet`：集合。是NSObject的子类，跟NSArray不一样在于，NSArray的元素是有序的，可以通过索引访问，而NSSet的元素是无序的，不能通过索引访问；NSArray的元素可以是重复的，而NSSet的元素不能重复，同一个元素只能有一个。
+ `NSMutableSet`：可变集合。是NSSet的子类，跟NSSet不一样的地方在于NSMutableSet的元素是可以修改的，可以增加删除替换等操作。
+ `NSCountedSet`：可计数集合。是NSMutableSet的子类，跟NSMutableSet不一样的地方在于，它的元素有个计数功能，添加同一个元素两次后该元素的计数为2，但是元素只能有一个不能重复，删除该元素的时候，当该元素的计数为0时删除它。
+ `NSOrderedSet`：有序集合。是NSObject的子类，跟NSSet不一样的地方在于，它的元素是有序的，可以通过索引访问，当然元素也不可重复出现。
+ `NSMutableOrderedSet`：有序可变集合。是NSOrderedSet的子类，跟NSOrderedSet不一样的地方在于，它的元素可以增删替换等操作。

下面有个图说明这几个集合的继承关系：

![屏幕快照 2015-12-03 18.39.57.png-547.5kB][1]

## 21.1 NSSet的使用   
 NSSet的常用使用方法代码如下：
 
 **NSSetTest.m**
```objective-c
#import <Foundation/Foundation.h>

//定义一个函数，把集合转换为NSString
NSString *NSCollectionToString(id collection) {
    NSMutableString *result = [NSMutableString stringWithString:@"["];
    for (id obj in collection) {
        [result appendString:[obj description]];
        [result appendString:@", "];
    }
    //如果collection是非空集合
    if ([collection count] > 0) {
        NSInteger len = [result length];//获取字符串的长度
        //去掉最后的,和空格
        [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
        [result appendString:@"]"];
        return result;
    }
    //collection是空集合
    else {
        [result appendString:@"]"];
        return result;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //使用对象初始化NSSet集合，重复的元素不会被添加进去
        NSSet *set1 = [NSSet setWithObjects:@"Java编程",@"C++编程",@"PHP编程",@"iOS编程",@"iOS编程",nil];
        
        //NSSet集合中的元素个数
        NSLog(@"set1集合的元素个数为：%ld", [set1 count]);
        NSLog(@"set1集合为：%@", NSCollectionToString(set1));
        
        //使用NSSet集合初始化NSSet集合
        NSSet *set2 = [NSSet setWithSet:[NSSet setWithObjects:@"张三",@"李四",@"王五",nil]];
        NSLog(@"set2集合的元素个数为：%ld", [set2 count]);
        NSLog(@"set2集合为：%@", NSCollectionToString(set2));
        
        //对NSSet集合添加元素，将添加元素后得到的新集合赋给set1
        set1 = [set1 setByAddingObject:@"Android编程"];
        //对NSSet集合添加NSSet，将添加得到的新集合赋给set1
        set1 = [set1 setByAddingObjectsFromSet:[NSSet setWithObjects:@"Python编程",@"Ruby编程",nil]];
        //对NSSet集合添加NSArray，将添加得到的新集合赋给set1
        set1 = [set1 setByAddingObjectsFromArray:[NSArray arrayWithObjects:@"C编程",@"C#编程",nil]];
        NSLog(@"添加元素后，set1集合为：%@", NSCollectionToString(set1));
        
        //判断两个NSSet是否有交集
        BOOL b = [set1 intersectsSet:set2];
        NSLog(@"set1和set2是否有交集：%d", b);
        
        //判断两个NSSet是否相等
        b = [set1 isEqualToSet:set2];
        NSLog(@"set1是否与set2相等：%d", b);
        
        //判断两个NSSet是否有包含关系
        b = [set1 isSubsetOfSet:set2];
        NSLog(@"set1是否是set2的子集：%d", b);
        
        //判断NSSet是否包含某个元素
        b= [set1 containsObject:@"Python编程"];
        NSLog(@"set1是否包含\"Python编程\"：%d", b);
        //判断NSSet是否包含某个元素，如果包含返回该元素，不包含返回nil
        NSSet *s1 = [set1 member:@"C编程"];
        NSLog(@"s1为：%@", s1);
        
        //返回NSSet中的随机元素，注意下面的返回的都是同一个元素
        NSLog(@"set1的一个随机元素为：%@", [set1 anyObject]);
        NSLog(@"set1的一个随机元素为：%@", [set1 anyObject]);
        
        //对NSSet中的所有元素用指定匿名块做处理，返回元素长度大于8的元素构成一个新NSSet
        NSSet *filterSet1 = [set1 objectsPassingTest:^BOOL(id  _Nonnull obj, BOOL * _Nonnull stop) {
            return (BOOL)([obj length] > 8);
        }];
        NSLog(@"set1中元素长度大于8的有：%@", NSCollectionToString(filterSet1));
    
    }
    return 0;
}
```
运行结果如下：
```
set1集合的元素个数为：4
set1集合为：[Java编程, PHP编程, iOS编程, C++编程]
set2集合的元素个数为：3
set2集合为：[李四, 张三, 王五]
添加元素后，set1集合为：[Java编程, iOS编程, C++编程, Android编程, Ruby编程, Python编程, C编程, C#编程, PHP编程]
set1和set2是否有交集：0
set1是否与set2相等：0
set1是否是set2的子集：0
set1是否包含"Python编程"：1
s1为：C编程
set1的一个随机元素为：Java编程
set1的一个随机元素为：Java编程
set1中元素长度大于8的有：[Android编程]
```

------
## 21.2 NSMutableSet的使用   
 NSMutableSet具有NSSet的常用方法之外，还添加了一些方法，代码如下：
 
 **NSMutableSetTest.m**

```objective-c
#import <Foundation/Foundation.h>

//定义一个函数，把集合转换为NSString
NSString *NSCollectionToString(id collection) {
    NSMutableString *result = [NSMutableString stringWithString:@"["];
    for (id obj in collection) {
        [result appendString:[obj description]];
        [result appendString:@", "];
    }
    //如果collection是非空集合
    if ([collection count] > 0) {
        NSInteger len = [result length];//获取字符串的长度
        //去掉最后的,和空格
        [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
        [result appendString:@"]"];
        return result;
    }
    //collection是空集合
    else {
        [result appendString:@"]"];
        return result;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个容量为10的可变集合，可以先不初始化，因为可变集合创建后可以对它修改
        NSMutableSet *set = [NSMutableSet setWithCapacity:10];
        
        //向NSMutableSet中添加元素
        [set addObject:@"PHP编程"];
        //向NSMutableSet中添加NSArray
        [set addObjectsFromArray:[NSArray arrayWithObjects:@"JAVA编程",@"C++编程",@"C++编程",nil]];
        NSLog(@"添加元素后，set为：%@", NSCollectionToString(set));
        
        //从NSMutableSet中删除元素
        [set removeObject:@"C++编程"];
        NSLog(@"删除元素后，set为：%@", NSCollectionToString(set));
        //从NSMutableSet中删除所有元素
        [set removeAllObjects];
        NSLog(@"删除所有元素后，set为：%@", NSCollectionToString(set));
        
        //用一个新的NSSet对NSMutableSet进行替换
        [set setSet:[NSSet setWithObjects:@"Python编程",@"Ruby编程",@"JavaScript编程",nil]];
        NSLog(@"用集合替换所有元素后，set为：%@", NSCollectionToString(set));
        
        NSSet *set2 = [NSSet setWithObjects:@"Ruby编程",@"Perl编程",@"Shell编程",nil];
        //计算两个集合的并集，调用者为NSMutableSet，而参数可以是NSSet/NSMutableSet，直接对NSMutableSet做修改
        [set unionSet:set2];
        //计算两个集合的差集，调用者为NSMutableSet，而参数可以是NSSet/NSMutableSet，直接对NSMutableSet做修改
        [set minusSet:set2];
        //计算两个集合的交集，调用者为NSMutableSet，而参数可以是NSSet/NSMutableSet，直接对NSMutableSet做修改
        [set intersectSet:set2];
        NSLog(@"进行并集差集交集处理后，set为：%@", NSCollectionToString(set));
    }
    return 0;
}
```
运行结果如下：
```
添加元素后，set为：[JAVA编程, C++编程, PHP编程]
删除元素后，set为：[JAVA编程, PHP编程]
删除所有元素后，set为：[]
用集合替换所有元素后，set为：[Ruby编程, JavaScript编程, Python编程]
进行并集差集交集处理后，set为：[]
```
---
## 21.3 NSCountedSet的使用
NSCountedSet具有NSMutableSet的方法之外，还有一些别的方法，实例代码如下：

**NSCountedSetTest.m**
```objective-c
#import <Foundation/Foundation.h>

//定义一个函数，把集合转换为NSString
NSString *NSCollectionToString(id collection) {
    NSMutableString *result = [NSMutableString stringWithString:@"["];
    for (id obj in collection) {
        [result appendString:[obj description]];
        [result appendString:@", "];
    }
    //如果collection是非空集合
    if ([collection count] > 0) {
        NSInteger len = [result length];//获取字符串的长度
        //去掉最后的,和空格
        [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
        [result appendString:@"]"];
        return result;
    }
    //collection是空集合
    else {
        [result appendString:@"]"];
        return result;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个NSCountedSet，因为可计数集合继承自可变集合，所以也可以对它进行修改
        NSCountedSet *set = [NSCountedSet setWithCapacity:10];
        
        //对NSCountedSet添加元素
        [set addObject:@"PHP编程"];
        [set addObjectsFromArray:[NSArray arrayWithObjects:@"PHP编程",@"JAVA编程",@"Perl编程",nil]];
        NSLog(@"set为：%@", NSCollectionToString(set));
        
        //获取指定元素在NSCountedSet中出现的次数
        NSLog(@"\"PHP编程\"在set中出现的次数为：%lu",[set countForObject:@"PHP编程"]);
        
        //删除NSCountedSet中的元素
        [set removeObject:@"PHP编程"];
        NSLog(@"删除\"PHP编程\"1次后，set为：%@", NSCollectionToString(set));
        NSLog(@"删除\"PHP编程\"1次后，它在set中出现的次数为：%lu",[set countForObject:@"PHP编程"]);
        [set removeObject:@"PHP编程"];
        NSLog(@"删除\"PHP编程\"2次后，set为：%@", NSCollectionToString(set));
        NSLog(@"删除\"PHP编程\"2次后，它在set中出现的次数为：%lu",[set countForObject:@"PHP编程"]);
    }
    return 0;
}
```
运行结果为：
```
set为：[PHP编程, JAVA编程, Perl编程]
"PHP编程"在set中出现的次数为：2
删除"PHP编程"1次后，set为：[PHP编程, JAVA编程, Perl编程]
删除"PHP编程"1次后，它在set中出现的次数为：1
删除"PHP编程"2次后，set为：[JAVA编程, Perl编程]
删除"PHP编程"2次后，它在set中出现的次数为：0
```
----
## 21.4 NSOrderedSet和NSMutableOrderedSet的使用
NSOrderedSet继承自NSObject，而NSMutableOrderedSet又继承自NSOrderedSet。
NSOrderedSet具有NSSet相似的功能，而NSMutableOrderedSet具有NSMutableSet相似的功能，有增加、删除、替换元素，排序，计算集合的交集、并集、差集等功能，同时有根据索引来对元素进行操作的功能。

**NSOrderedSetAndNSMutableOrderedSetTest**
```objective-c
#import <Foundation/Foundation.h>

//定义一个函数，把集合转换为NSString
NSString *NSCollectionToString(id collection) {
    NSMutableString *result = [NSMutableString stringWithString:@"["];
    for (id obj in collection) {
        [result appendString:[obj description]];
        [result appendString:@", "];
    }
    //如果collection是非空集合
    if ([collection count] > 0) {
        NSInteger len = [result length];//获取字符串的长度
        //去掉最后的,和空格
        [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
        [result appendString:@"]"];
        return result;
    }
    //collection是空集合
    else {
        [result appendString:@"]"];
        return result;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //初始化一个NSOrderedSet，它跟NSSet不一样的地方在于有索引访问和索引获取操作
        NSOrderedSet *orderedSet = [NSOrderedSet orderedSetWithObjects:@"PHP编程",@"PHP编程",@"JAVA编程",@"C编程",@"Ruby编程",nil];
        NSLog(@"orderedSet为：%@", NSCollectionToString(orderedSet));//有序
        
        //根据索引来访问NSOrderedSet中的元素
        NSLog(@"orderedSet的第一个元素为：%@", [orderedSet firstObject]);
        NSLog(@"orderedSet的第最后一个元素为：%@", [orderedSet lastObject]);
        NSLog(@"orderedSet的索引为2第一个元素为：%@", [orderedSet objectAtIndex:2]);
        NSLog(@"orderedSet的索引范围为[0,2)的元素为：%@",
              NSCollectionToString([orderedSet objectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(0, 2)]]));
        
        //根据NSOrderedSet中的元素来获取索引号
        NSLog(@"\"C编程\"在orderedSet中的索引为：%ld", [orderedSet indexOfObject:@"C编程"]);
        //根据NSOrderedSet中的所有元素符合匿名函数块调节的元素，返回这些元素的索引
        NSIndexSet *indexSet = [orderedSet indexesOfObjectsPassingTest:
                                ^BOOL(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
                                        return (BOOL)([obj length] > 4);
        }];
        NSLog(@"orderedSet中长度大于4的元素的索引为：%@", indexSet);
        
        //初始化一个NSMutableOrderedSet，它跟NSMutableSet不一样的地方在于可以用索引修改元素
        NSMutableOrderedSet *mutableOrderedSet = [NSMutableOrderedSet orderedSetWithCapacity:10];
        [mutableOrderedSet addObject:@"C#编程"];
        [mutableOrderedSet addObjectsFromArray:[NSArray arrayWithObjects:@"C#编程",@"C编程",@"C++编程",@"JAVA编程",@"PHP编程",@"Ruby编程",nil]];
        
        //根据索引来删除NSMutableOrderedSet中的元素
        [mutableOrderedSet removeObjectAtIndex:2];
        [mutableOrderedSet removeObjectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(0, 2)]];
        [mutableOrderedSet removeObjectsInRange:NSMakeRange(1, 2)];
        NSLog(@"mutableOrderedSet为：%@", NSCollectionToString(mutableOrderedSet));
    }
    return 0;
}
```
运行结果为：
```
[PHP编程, JAVA编程, C编程, Ruby编程]
orderedSet的第一个元素为：PHP编程
orderedSet的第最后一个元素为：Ruby编程
orderedSet的索引为2第一个元素为：C编程
orderedSet的索引范围为[0,2)的元素为：[PHP编程, JAVA编程]
"C编程"在orderedSet中的索引为：2
orderedSet中长度大于4的元素的索引为：<NSIndexSet: 0x1004008a0>[number of indexes: 3 (in 2 ranges), indexes: (0-1 3)]
mutableOrderedSet为：[JAVA编程]
```
----
## 21.5 判断NSSet元素是否相等的规则
NSSet在添加元素的时候是怎么判断元素是否相等呢？
假设有一个NSMutableSet *set，创建一个`NSString *s1`并初始化它，然后创建一个`NSString *s2 = [s1 copy]`，s2指向的堆中的字符串内容跟s1指向的一样，但是地址不一样，是两个字符串对象。然后我们`[set appendObject:s1]`后，再`[set appendObject:s2]`，这个时候会添加失败，因为set会认为`[s1 isEqual:s2]`为YES，并且NSString类继承自NSObject的hash方法被重写过，重写方法中认为只要`[s1 isEqualToString:s2]`为真，这个时候`[s1 hash]`的值等于`[s2 hash]`，也即具有相同的hashCode。

NSSet添加元素时判断元素相等的规则有两条：
1. 两个对象通过isEqual:方法必须返回YES；也即指针相等或者内容相等。
2. 两个对象的hash方法返回值相等。也即hashCode相等。
两个对象的内容相等，它们的hashCode不一定相等，如果要改成相等，那么就需要重写继承自NSObject的hash方法。

下面举个例子：
**GKHUser.h**
```objective-c
#import <Foundation/Foundation.h>

@interface GKHUser : NSObject
@property (nonatomic, copy) NSString* name;
@property (nonatomic, copy) NSString* pass;

- (id) initWithName:(NSString *)aName pass:(NSString *)aPass;
- (void) say:(NSString *)content;
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
@end
```
**main.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHUser.h"

//定义一个函数，把集合转换为NSString
NSString *NSCollectionToString(id collection) {
    NSMutableString *result = [NSMutableString stringWithString:@"["];
    for (id obj in collection) {
        [result appendString:[obj description]];
        [result appendString:@", "];
    }
    //如果collection是非空集合
    if ([collection count] > 0) {
        NSInteger len = [result length];//获取字符串的长度
        //去掉最后的,和空格
        [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
        [result appendString:@"]"];
        return result;
    }
    //collection是空集合
    else {
        [result appendString:@"]"];
        return result;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSSet *set = [NSSet setWithObjects:
                      [[GKHUser alloc] initWithName:@"sun" pass:@"123"],
                      [[GKHUser alloc] initWithName:@"bai" pass:@"234"],
                      [[GKHUser alloc] initWithName:@"sun" pass:@"123"],
                      [[GKHUser alloc] initWithName:@"hu" pass:@"456"],
                      [[GKHUser alloc] initWithName:@"gan" pass:@"567"],
                      nil];
        NSLog(@"set中的元素个数为：%ld", [set count]);
        NSLog(@"%@", NSCollectionToString(set));
    }
    return 0;
}
```
运行结果为：
```
set中的元素个数为：5
[<GKHUser[name=sun, pass=123]>, <GKHUser[name=bai, pass=234]>, <GKHUser[name=sun, pass=123]>, <GKHUser[name=gan, pass=567]>, <GKHUser[name=hu, pass=456]>]
```
我们发现，添加了重复元素进集合了。仔细分析代码会发现，虽然GKHUser类中重写了`isEqual:`方法，只要两个GKHUser对象的内容相等，那么isEqual:就为真，但此时GKHUser类中的hash方法是继承自NSObject的，两个对象得到的hashCode不一样，这样集合就会认为这两个元素是不相等的，可以被添加进去。

我们在**GKHUser.m**中添加如下代码，重写hash方法：
```objective-c```
//重写继承自NSObject的hash方法
- (NSInteger) hash {
    NSLog(@"====hash====");
    NSInteger nameHash = (self.name == nil ? 0 : [self.name hash]);
    NSInteger passHash = (self.pass == nil ? 0 : [self.pass hash]);
    return nameHash * 31 + passHash;
}
```
再次运行**main.m**得到如下结果：
```
====hash====
====hash====
====hash====
====hash====
====hash====
set中的元素个数为：4
[<GKHUser[name=bai, pass=234]>, <GKHUser[name=gan, pass=567]>, <GKHUser[name=sun, pass=123]>, <GKHUser[name=hu, pass=456]>]
```
此时能在添加对象的时候，会判定有重复元素，只添加一个元素进集合。

  [1]: http://static.zybuluo.com/gkh178/jpz2isgwbbyhtq21ukchuhxm/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-03%2018.39.57.png
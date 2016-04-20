# 20 Foundation框架之NSAarry和NSMutableArray

Tags: Objective-C

---

NSAaary：数组，可以通过索引访问，不可变。
NSMutable：可变数组，可以增添删操作，可变。

## 20.1 NSAarry的用法
下面是对NSAarry的一些方法使用示例，代码如：
**NSArrayTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个空的NSArray
        NSArray *a = [NSArray array];
        //使用对象元素初始化数组
        NSArray *b = [NSArray arrayWithObjects:@"iOS编程", @"Java编程", @"PHP编程", @"C++编程", @"Python编程", nil];
        //使用NSArray初始化数组
        NSArray *array = [NSArray arrayWithArray:b];
        
        //访问第一元素
        NSLog(@"array的第一个元素为：%@", [array firstObject]);
        //访问访问索引为2的元素
        NSLog(@"array的索引为2元素为：%@", [array objectAtIndex:2]);
        //访问最后一个元素
        NSLog(@"array的最后一个元素为：%@", [array lastObject]);
        
        //获取指定元素在NSAarry中的索引号
        NSLog(@"PHP编程在array中的索引号为：%ld", [array indexOfObject:@"PHP编程"]);
       
        //向NSArray最后追加一个元素，array指向的原来的数组对象的内容不变，改变的是array指向的新对象追加了元素
        array = [array arrayByAddingObject:@"张三"];
        NSLog(@"追加元素后，array数组为：");
        array = [array arrayByAddingObjectsFromArray:[NSArray arrayWithObjects:@"李四", @"王五", nil]];
        for (int i = 0; i < array.count; ++i) {
            NSLog(@"%@", [array objectAtIndex:i]);
        }
        
        //获取数组中指定索引范围内[2,5)的元素组成一个新的NSAarry数组
        NSArray *arr1 = [array objectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(2, 3)]];
        NSLog(@"数组arr1为：%@", arr1);
        //获取数组中指定索引范围内[5,8)的元素组成一个新的NSAarry数组
        NSArray *arr2 = [array subarrayWithRange:NSMakeRange(5, 3)];
        NSLog(@"数组arr2为：%@", arr2);
        
        //将NSAarry数组写入文件
        [array writeToFile:@"myFile.txt" atomically:YES];
        //使用文件初始化数组
        b = [NSArray arrayWithContentsOfFile:@"myFile.txt"];
        NSLog(@"数组b为：%@", b);
    }
    return 0;
}
```
运行结果为：
> array的第一个元素为：iOS编程
array的索引为2元素为：PHP编程
array的最后一个元素为：Python编程
PHP编程在array中的索引号为：2
追加元素后，array数组为：
iOS编程
Java编程
PHP编程
C++编程
Python编程
张三
李四
王五
数组arr1为：(
    "PHP\U7f16\U7a0b",
    "C++\U7f16\U7a0b",
    "Python\U7f16\U7a0b"
)
数组arr2为：(
    "\U5f20\U4e09",
    "\U674e\U56db",
    "\U738b\U4e94"
)
数组b为：(
    "iOS\U7f16\U7a0b",
    "Java\U7f16\U7a0b",
    "PHP\U7f16\U7a0b",
    "C++\U7f16\U7a0b",
    "Python\U7f16\U7a0b",
    "\U5f20\U4e09",
    "\U674e\U56db",
    "\U738b\U4e94"
)

---
## 20.2 NSMutableArray的用法
下面是对NSMutableAarry的一些方法使用示例，主要是增删改以及访问操作，代码如：
**NSMutableArrayTest.m**
```objective-c
#import <Foundation/Foundation.h>

//定义一个函数，把NSArray转换为NSString
NSString *NSArrayToString(NSArray *array) {
    NSMutableString *result = [NSMutableString stringWithString:@"["];
    for (id obj in array) {
        [result appendString:[obj description]];
        [result appendString:@", "];
    }
    //如果array是非空数组
    if ([array count] > 0) {
        NSInteger len = [result length];//获取字符串的长度
        //去掉最后的,和空格
        [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
        [result appendString:@"]"];
        return result;
    }
    //array是空数组
    else {
        [result appendString:@"]"];
        return result;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSMutableArray *array = [NSMutableArray arrayWithObjects:@"JAVA", @"C", @"C++", @"Matlab", @"Asm", @"Objective-C", @"Swift", nil];
        
        //对NSMutableArray数组添加一个元素，修改的是原始指向的对象，而不是拷贝的副本
        [array addObject:@"张三"];
        //对NSMutableArray添加一个数组
        [array addObjectsFromArray:[NSArray arrayWithObjects:@"李四", @"王五", nil]];
        NSLog(@"添加元素后，array为：%@", NSArrayToString(array));
        
        //对NSMutableArray数组中的指定位置插入一个元素
        [array insertObject:@"赵二" atIndex:7];
        //对NSMutableArray数组中的指定索引范围内的位置插入一些元素
        [array insertObjects:[NSArray arrayWithObjects:@"PHP", @"Python", nil]
                   atIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(3, 2)]];
        NSLog(@"插入元素后，array为：%@", NSArrayToString(array));
        
        //删除NSMutableArray数组的最后一个元素
        [array removeLastObject];//[JAVA, C, C++, PHP, Python, Matlab, Asm, Objective-C, Swift, 赵二, 张三, 李四]
        //删除NSMutableArray数组索引为5的元素
        [array removeObjectAtIndex:5];//[JAVA, C, C++, PHP, Python, Asm, Objective-C, Swift, 赵二, 张三, 李四]
        //删除NSMutableArray数组索引范围为[5,8)的元素
        //[JAVA, C, C++, PHP, Python, 赵二, 张三, 李四]
        [array removeObjectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(5, 3)]];
        //删除NSMutableArray数组中指定的对象
        [array removeObject:@"赵二"];//[JAVA, C, C++, PHP, Python, 张三, 李四]
        //删除NSMutableArray数组索引范围内的指定的某个对象
        [array removeObject:@"张三" inRange:NSMakeRange(5, 2)];//[JAVA, C, C++, PHP, Python, 李四]
        //删除NSMutableArray数组索引范围内的所有对象
        [array removeObjectsInRange:NSMakeRange(3, 2)];//[JAVA, C, C++, 李四]
        //删除NSMutableArray数组内某个数组
        [array removeObjectsInArray:[NSArray arrayWithObjects:@"JAVA", @"C", nil]];//[C++, 李四]
        NSLog(@"删除若干元素后，array为：%@", NSArrayToString(array));
        
        //替代NSMutableArray数组中指定索引号的某个元素
        [array replaceObjectAtIndex:1 withObject:@"刘秋"];//[C++, 刘秋]
        //替代NSMutableArray数组中指定索引范围内的所有元素，并且被替代的元素个数必须跟替代的元素个数相等
        [array replaceObjectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(0, 2)]
                           withObjects:[NSArray arrayWithObjects:@"HTML",@"CSS", nil]];//[HTML, CSS]
        //替代NSMutableArray数组中指定索引范围内的所有元素，被替代的元素个数可以跟替代的元素个数不等
        [array replaceObjectsInRange:NSMakeRange(0, 1)
                withObjectsFromArray:[NSArray arrayWithObjects:@"Ruby",@"Perl", nil]];//[Ruby, Perl, CSS]
        //替代NSMutableArray数组中指定索引范围内的所有元素，被替代的元素个数可以跟替代的元素个数不等，并且被替代的元素可以是某个NSArray的索引范围内的元素
        [array replaceObjectsInRange:NSMakeRange(1, 2) withObjectsFromArray:[NSArray arrayWithObjects:@"Shell", @"Lua", @"C#", @"Go", nil]
                               range:NSMakeRange(0, 3)];//[Ruby, Shell, Lua, C#]
        NSLog(@"替代若干元素后，array为：%@", NSArrayToString(array));
        
        //删除NSMutableArray数组中所有的对象
        [array removeAllObjects];
        NSLog(@"删除所有元素后，array为：%@", NSArrayToString(array));

    }
    return 0;
}
```
运行结果如下：
```
添加元素后，array为：[JAVA, C, C++, Matlab, Asm, Objective-C, Swift, 张三, 李四, 王五]
插入元素后，array为：[JAVA, C, C++, PHP, Python, Matlab, Asm, Objective-C, Swift, 赵二, 张三, 李四, 王五]
删除若干元素后，array为：[C++, 李四]
替代若干元素后，array为：[Ruby, Shell, Lua, C#]
删除所有元素后，array为：[]
```
-------
## 20.3 对NSArray中的元素进行逐个方法调用
如果我们需要对NSArray中的所有元素，按照顺序或者逆序依次调用某个方法，或者对NSArray中指定索引范围内的所有元素，按照顺序或者逆序依次调用某个方法。代码示例如下：
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

**NSArrayElementsInvokeSelector.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHUser.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //使用元素对象初始化NSArray
        NSArray *array = [NSArray arrayWithObjects:
                          [[GKHUser alloc] initWithName:@"sun" pass:@"123"],
                          [[GKHUser alloc] initWithName:@"bai" pass:@"234"],
                          [[GKHUser alloc] initWithName:@"li" pass:@"345"],
                          [[GKHUser alloc] initWithName:@"wang" pass:@"456"],
                          [[GKHUser alloc] initWithName:@"zhao" pass:@"567"], nil];
        
        //对NSArray数组中的所有元素调用say:方法
        [array makeObjectsPerformSelector:NSSelectorFromString(@"say:") withObject:@"NSArray真强大"];
        
        //对NSArray数组中的所有元素调用匿名块方法
        NSString *content = @"iOS";
        [array enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
            [obj say:content];
            NSLog(@"正在处理第%ld个元素：%@", idx, obj);
        }];
        NSLog(@"==============================");
        
        //对NSArray数组中的所有元素使用逆序的顺序调用匿名块方法
        [array enumerateObjectsWithOptions:NSEnumerationReverse usingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            [obj say:content];
            NSLog(@"正在处理第%ld个元素：%@", idx, obj);
        }];
        NSLog(@"==============================");
        
        //对NSArray数组中的指定索引范围内的元素使用逆序的顺序调用匿名块方法
        [array enumerateObjectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(2, 3)] options:NSEnumerationReverse
                              usingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
                                  [obj say:content];
                                  NSLog(@"正在处理第%ld个元素：%@", idx, obj);
                                 }];
    }
    return 0;
}
```
运行结果如下：
```
sun说：NSArray真强大
bai说：NSArray真强大
li说：NSArray真强大
wang说：NSArray真强大
zhao说：NSArray真强大
sun说：iOS
正在处理第0个元素：<GKHUser[name=sun, pass=123]>
bai说：iOS
正在处理第1个元素：<GKHUser[name=bai, pass=234]>
li说：iOS
正在处理第2个元素：<GKHUser[name=li, pass=345]>
wang说：iOS
正在处理第3个元素：<GKHUser[name=wang, pass=456]>
zhao说：iOS
正在处理第4个元素：<GKHUser[name=zhao, pass=567]>
==============================
zhao说：iOS
正在处理第4个元素：<GKHUser[name=zhao, pass=567]>
wang说：iOS
正在处理第3个元素：<GKHUser[name=wang, pass=456]>
li说：iOS
正在处理第2个元素：<GKHUser[name=li, pass=345]>
bai说：iOS
正在处理第1个元素：<GKHUser[name=bai, pass=234]>
sun说：iOS
正在处理第0个元素：<GKHUser[name=sun, pass=123]>
==============================
zhao说：iOS
正在处理第4个元素：<GKHUser[name=zhao, pass=567]>
wang说：iOS
正在处理第3个元素：<GKHUser[name=wang, pass=456]>
li说：iOS
正在处理第2个元素：<GKHUser[name=li, pass=345]>
```

----
## 20.4 对NSArray中的元素进行排序 
当我们需要对一个数组进行排序时，代码示例如下：
**NSArrayElementsSort**
```objective-c
#import <Foundation/Foundation.h>

//定义一个比较函数，根据两个对象的intValue来比较
NSInteger intSort(id num1, id num2, void *context) {
    int v1 = [num1 intValue];
    int v2 = [num2 intValue];
    if (v1 < v2) {
        return NSOrderedAscending;
    }
    else if (v1 == v2) {
        return NSOrderedSame;
    }
    else {
        return NSOrderedDescending;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSArray *array1 = [NSArray arrayWithObjects:@"Java", @"C++", @"C", @"Matlab", @"Objective-C", @"Asm", @"Swift", nil];
        
        //对NSArray中的元素使用元素自带的compare:方法进行排序
        array1 = [array1 sortedArrayUsingSelector:@selector(compare:)];
        NSLog(@"%@", array1);
        
        //对NSArray中的元素使用intSort函数进行排序
        NSArray *array2 = [NSArray arrayWithObjects:[NSNumber numberWithInt:18], [NSNumber numberWithInt:28],
                           [NSNumber numberWithInt:8], [NSNumber numberWithInt:38], [NSNumber numberWithInt:48],nil];
        array2 = [array2 sortedArrayUsingFunction:intSort context:nil];
        NSLog(@"%@", array2);
        
        //对NSArray中的元素使用自定义的匿名块函数进行排序
        NSArray *array3 = [NSArray arrayWithObjects:[NSNumber numberWithInt:18], [NSNumber numberWithInt:28],
                           [NSNumber numberWithInt:8], [NSNumber numberWithInt:38], [NSNumber numberWithInt:48],nil];
        array3 = [array3 sortedArrayUsingComparator:^NSComparisonResult(id  _Nonnull obj1, id  _Nonnull obj2) {
            if ([obj1 intValue] < [obj2 intValue]) {
                return NSOrderedAscending;
            }
            else if ([obj1 intValue] == [obj2 intValue]) {
                return NSOrderedSame;
            }
            else {
                return NSOrderedDescending;
            }
        }];
        NSLog(@"%@", array3);
    }
    return 0;
}
```
运行结果如下：
```
(
    Asm,
    C,
    "C++",
    Java,
    Matlab,
    "Objective-C",
    Swift
)
(
    8,
    18,
    28,
    38,
    48
)
(
    8,
    18,
    28,
    38,
    48
)
```

---
## 20.5 对NSArray中的元素进行遍历
需要对NSArray对象中的元素进行逐个遍历的时候，代码示例如下：
**NSEnumerator.m**
```objecitive-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
         NSArray *array = [NSArray arrayWithObjects:@"Java", @"C++", @"C", @"Matlab", @"Objective-C", @"Asm", @"Swift", nil];
        
        //获取NSArray的顺序枚举器
        NSEnumerator *en1 = [array objectEnumerator];
        id object;
        //使用nextObject方法获取一个元素，逐个循环遍历
        while (object = [en1 nextObject]) {
            NSLog(@"%@", object);
        }
        NSLog(@"===================");
    
        
        //获取NSArray的逆序枚举器
        NSEnumerator *en2 = [array reverseObjectEnumerator];
        //使用nextObject方法获取一个元素，逐个逆序循环遍历
        while (object = [en2 nextObject]) {
            NSLog(@"%@", object);
        }
        NSLog(@"===================");
        
        //使用(for...in)对NSArray对象进行快速枚举
        for (id obj in array) {
            NSLog(@"%@", obj);
        }
    }
    return 0;
}
```
运行结果如下：
```
Java
C++
C
Matlab
Objective-C
Asm
Swift
===================
Swift
Asm
Objective-C
Matlab
C
C++
Java
===================
Java
C++
C
Matlab
Objective-C
Asm
Swift
```

---
## 20.6 对NSArray中的复合元素使用KVC和KVO
当NSArray中的元素本身就是复合元素时，比如GKHUser，包含name属性和pass属性，当需要获取所有元素的某个属性的值组成一个新集合时，使用`id a = [NSArray对象 valueForKeyPath:@"属性"]`得到；当需要对所有元素的某个属性值修改为同一个新值时，使用`[NSArray setValue:@"属性的新值" forKeyPath:@"属性"]`方法得到；这叫做NSArray的KVC。

NSArray的KVO用来监听数组中的元素的属性值是否发生变化，通常有如下方式：
1. `addObserver:forKeyPath:options:context:`：为数组中的所有元素添加监听器。
2. `removeObserver:forKeyPath`：为数组中所有元素移除监听器。
3. `addObserver:toObjectsAtIndexes:forKeyPath:options:context:`：为数组中指定的元素的添加监听器。
4. `removeObserver:fromObjectsAtIndexes:forKeyPath:`：为数组中指定元素移除监听器。


代码例子如下：
**NSArrayKVCTest**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHUser.h"

//定义一个函数，把NSArray转换为NSString
NSString *NSArrayToString(NSArray *array) {
    NSMutableString *result = [NSMutableString stringWithString:@"["];
    for (id obj in array) {
        [result appendString:[obj description]];
        [result appendString:@", "];
    }
    //如果array是非空数组
    if ([array count] > 0) {
        NSInteger len = [result length];//获取字符串的长度
        //去掉最后的,和空格
        [result deleteCharactersInRange:NSMakeRange(len -2, 2)];
        [result appendString:@"]"];
        return result;
    }
    //array是空数组
    else {
        [result appendString:@"]"];
        return result;
    }
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //使用元素对象初始化NSArray
        NSArray *array = [NSArray arrayWithObjects:
                          [[GKHUser alloc] initWithName:@"sun" pass:@"123"],
                          [[GKHUser alloc] initWithName:@"bai" pass:@"234"],
                          [[GKHUser alloc] initWithName:@"li" pass:@"345"],
                          [[GKHUser alloc] initWithName:@"wang" pass:@"456"],
                          [[GKHUser alloc] initWithName:@"zhao" pass:@"567"], nil];
        
        //使用KVC方式获取NSArray中的所有元素的name属性组成一个新集合
        id newArr = [array valueForKeyPath:@"name"];
        NSLog(@"%@", NSArrayToString(newArr));
        
        //使用KVC方式对NSArray中的所有元素的name属性的值进行统一修改
        [array setValue:@"名字" forKeyPath:@"name"];
        NSLog(@"%@", NSArrayToString(array));
    }
    return 0;
}
```
运行结果如下：
```
[sun, bai, li, wang, zhao]
[<GKHUser[name=名字, pass=123]>, <GKHUser[name=名字, pass=234]>, <GKHUser[name=名字, pass=345]>, <GKHUser[name=名字, pass=456]>, <GKHUser[name=名字, pass=567]>]
```

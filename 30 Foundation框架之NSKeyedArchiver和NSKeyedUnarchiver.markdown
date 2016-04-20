# 30 Foundation框架之NSKeyedArchiver和NSKeyedUnarchiver

Tags: Objective-C

---
NSKeyedArchiver表示归档，表示把对象转换为可保存可传输的数据流，通常可以是文件或者URL。
NSKeyedUnarchiver表示恢复归档，表示从数据流中恢复对象。

NSKeyedArchiver和NSKeyedUnarchiver有两种使用方式，第一种是使用类方法创建将对象当成root进行归档和恢复，代码示例如下：
**test1.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSDictionary *dict1 = [NSDictionary dictionaryWithObjectsAndKeys:
                              [NSNumber numberWithInt:89], @"C++",
                              [NSNumber numberWithInt:79], @"C",
                              [NSNumber numberWithInt:69], @"Java",
                              [NSNumber numberWithInt:59], @"Swift",nil];
        //使用NSKeyedArchiver的类方法进行归档
        [NSKeyedArchiver archiveRootObject:dict1 toFile:@"/Users/Ralf/Desktop/myDict.archive"];//归档至文件中
        NSData *dataArchive = [NSKeyedArchiver archivedDataWithRootObject:dict1];//归档至Data中
        
        //使用NSKeyedUnarchiver的类方法恢复归档中的对象
        NSDictionary *dict20 = [NSKeyedUnarchiver unarchiveObjectWithFile:@"/Users/Ralf/Desktop/myDict.archive"];//从文件中恢复对象
        NSDictionary *dict21 = [NSKeyedUnarchiver unarchiveObjectWithData:dataArchive];//从NSData中恢复对象
        
        NSLog(@"C++对应的value为：%@", [dict20 valueForKey:@"C++"]);
        NSLog(@"C对应的value为：%@", [dict20 valueForKey:@"C"]);
        NSLog(@"Java对应的value为：%@", [dict20 valueForKey:@"Java"]);
        NSLog(@"Swift对应的value为：%@", [dict20 valueForKey:@"Swift"]);
        NSLog(@"C++对应的value为：%@", [dict21 valueForKey:@"C++"]);
        NSLog(@"C对应的value为：%@", [dict21 valueForKey:@"C"]);
        NSLog(@"Java对应的value为：%@", [dict21 valueForKey:@"Java"]);
        NSLog(@"Swift对应的value为：%@", [dict21 valueForKey:@"Swift"]);
    }
    return 0;
}
```
运行结果：
```
C++对应的value为：89
C对应的value为：79
Java对应的value为：69
Swift对应的value为：59
C++对应的value为：89
C对应的value为：79
Java对应的value为：69
Swift对应的value为：59
```
------
当需要归档、恢复某个自定义类的对象的实例时，那么该类必须实现`<NSCoding>`协议，也即在类的实现部分必须实现该协议的如下两个方法：
1. `initWithCoder:`：该方法负责恢复对象，在该方法中要恢复该对象的所有实例变量
2. `encodeWithCoder:`：该方法负责归档该对象，在该方法中要归档该对象的所有实例变量

如果针对一些基础数据类型时，该怎么归档和恢复呢？OC提供了如下方法：
![屏幕快照 2015-12-08 02.30.52.png-101kB][1]

代码示例如下：
**GKHApple.h**
```objective-c
#import <Foundation/Foundation.h>

@interface GKHApple : NSObject <NSCoding>
@property (nonatomic, copy) NSString *color;
@property (nonatomic, assign) double weight;
@property (nonatomic, assign) int size;
- (id) initWithColor:(NSString *)color weight:(double)weight size:(int)size;
@end
```
**GKHApple.m**
```objective-c
#import "GKHApple.h"

@implementation GKHApple
- (id) initWithColor:(NSString *)color weight:(double)weight size:(int)size {
    if (self = [super init]) {
        self.color = [color copy];
        self.weight = weight;
        self.size = size;
    }
    return self;
}

- (NSString *) description {
    return [NSString stringWithFormat:@"<GKHApple[_color=%@, _weight=%g, _size=%d]>", self.color, self.weight, self.size];
}

//实现该类的对象的归档方法
- (void) encodeWithCoder:(NSCoder *)aCoder {
    //调用NSCoder的方法归档对象的每个实例变量
    [aCoder encodeObject:_color forKey:@"color"];
    [aCoder encodeDouble:_weight forKey:@"weight"];
    [aCoder encodeInt:_size forKey:@"size"];
}

//实现该类的对象的恢复归档的方法
- (id) initWithCoder:(NSCoder *)aDecoder {
    //调用NSCoder的方法恢复归档到对象的每个实例变量
    [aDecoder decodeObjectForKey:@"color"];
    [aDecoder decodeDoubleForKey:@"weight"];
    [aDecoder decodeIntForKey:@"size"];
    return self;
}
@end
```
**test2.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHApple.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSDictionary *dict = [NSDictionary dictionaryWithObjectsAndKeys:
                               [NSNumber numberWithInt:89], @"C++",
                               [NSNumber numberWithInt:79], @"C",
                               [NSNumber numberWithInt:69], @"Java",
                               [NSNumber numberWithInt:59], @"Swift",nil];
        NSSet *set = [NSSet setWithObjects:@"C++编程", @"C编程", @"Java编程", @"Swift编程", nil];
        GKHApple *apple = [[GKHApple alloc] initWithColor:@"red" weight:3.4 size:20];
        
        //创建一个NSMutableData用来保存归档的数据
        NSMutableData *data = [NSMutableData data];
        //①使用NSKeyedArchiver的实例方法创建归档
        NSKeyedArchiver *arch = [[NSKeyedArchiver alloc]initForWritingWithMutableData:data];
        //②重复调用encodeObject:forKey:方法归档所有需要归档的对象
        [arch encodeObject:dict forKey:@"myDict"];
        [arch encodeObject:set forKey:@"mySet"];
        [arch encodeObject:apple forKey:@"myApple"];
        //③结束归档
        [arch finishEncoding];
        //将归档的NSMutableData的数据写入文件
        if ([data writeToFile:@"multi.archive" atomically:YES] == NO) {
            NSLog(@"归档失败");
        }
        
        //创建一个NSData用来读取指定文件的归档数据
        NSData *data1 = [NSData dataWithContentsOfFile:@"multi.archive"];
        //①使用NSKeyedUnarchiver的实例方法创建解归档对象
        NSKeyedUnarchiver *unarch = [[NSKeyedUnarchiver alloc]initForReadingWithData:data1];
        //②重复调用decodeObjectForKey:方法恢复归档中的对象
        [unarch decodeObjectForKey:@"myDict"];
        [unarch decodeObjectForKey:@"mySet"];
        [unarch decodeObjectForKey:@"myApple"];
        //③结束解归档
        [unarch finishDecoding];
        NSLog(@"%@", dict);
        NSLog(@"%@", set);
        NSLog(@"%@", apple);
    }
    return 0;
}
```
运行结果为：
```
{
    C = 79;
    "C++" = 89;
    Java = 69;
    Swift = 59;
}
{(
    "Java\U7f16\U7a0b",
    "Swift\U7f16\U7a0b",
    "C++\U7f16\U7a0b",
    "C\U7f16\U7a0b"
)}
<GKHApple[_color=red, _weight=3.4, _size=20]>
```

  [1]: http://static.zybuluo.com/gkh178/8fsue53evark13q29h1xjadb/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-08%2002.30.52.png
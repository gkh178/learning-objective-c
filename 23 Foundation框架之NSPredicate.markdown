# 23 Foundation框架之NSPredicate

Tags: Objective-C

---

`NSPredicate`对象：也称作谓词对象，用来定义一个逻辑条件，通过该条件可以执行搜索或者过滤操作。

谓词表达式中支持的语法：

+ 关系运算符：
1  =或==：等于
2  >=或=>：大于等于
3  <=或=<：小于等于
4  >：大于
5  <：小于
6  !=或<>：不等于
7  BETWEEN：“表达式 BETWEEN {下限，上限}”
+ 逻辑运算符
1  AND或&&：逻辑与
2  OR或||：逻辑或
3  NOT或！：逻辑非
+ 字符串比较的运算符
1  BEGINSWITH：以指定的子串开头
2  ENDSWTIEH：以指定的子串结束
3  CONTAINS：字符串是否包含子字符串
4  LIKE：字符串是否匹配指定的字符串模板。字符串模板可以用通配符\*和？，\*表示多个字符，?表示一个字符。
5  MATCHES：匹配正则表达式。
+ 操作集合的运算符
1  ANY或SOME：ANY/SOME children.age < 18。表示children中有元素的age小于18就返回YES
2  ALL：ALL children.age < 18。表示childre中所有age都小于18才返回YES
3  NONE：如 NONE children.age < 18。表示children中所有age都不小于18才返回YES
4  IN：表达式的出现在右边的集合中才返回YES。如name IN {"Aa","Bb","Cc"}
+ 直接量
1  SELF：代表正在被判断的本身对象
2  TURE或YES：代表YES
3  FALSE或NO：代表NO
4  NULL或NIL：代表空值

代码示例
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

//重写继承自NSObject的hash方法
- (NSInteger) hash {
    NSLog(@"====hash====");
    NSInteger nameHash = (self.name == nil ? 0 : [self.name hash]);
    NSInteger passHash = (self.pass == nil ? 0 : [self.pass hash]);
    return nameHash * 31 + passHash;
}
@end
```
**NSPredicateTest.m**
```objective-c
#import <Foundation/Foundation.h>
#import "GKHUser.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个Predicate对象，要求name属性的值以s字母开头
        NSPredicate *pred = [NSPredicate predicateWithFormat:@"name like 's*'"];
        GKHUser *user1 = [[GKHUser alloc]initWithName:@"sun" pass:@"123"];
        //使用evaluateWithObject:方法对目标对象用谓词对象处理
        BOOL result1 = [pred evaluateWithObject:user1];
        NSLog(@"user1的name属性是否以字母s开头：%d", result1);
        
        
        //在对可变集合比如NSMutableSet、NSMutableArray、NSMutableOrderedSet、NSMutableDictionary进行谓词过滤时
        //将满足谓词对象的元素返回给本身。
        NSMutableArray *array = [NSMutableArray arrayWithObjects:
                                 [NSNumber numberWithInt:50],
                                 [NSNumber numberWithInt:50],
                                 [NSNumber numberWithInt:42],
                                 [NSNumber numberWithInt:20],
                                 [NSNumber numberWithInt:64],
                                 [NSNumber numberWithInt:56], nil];
        NSPredicate *pred1 = [NSPredicate predicateWithFormat:@"SELF > 50"];//大于50的满足谓词对象
        [array filterUsingPredicate:pred1];
        NSLog(@"值大于50的元素：%@", array);
        
        //在对不可变集合比如NSSet、NSArray、NSOrderedSet、NSDictionary进行谓词过滤时，返回满足谓词对象的元素并组成新集合。
        NSSet *set = [NSSet setWithObjects:[[GKHUser alloc]initWithName:@"孙悟空" pass:@"343"],
                      [[GKHUser alloc]initWithName:@"金角大王" pass:@"231"],
                      [[GKHUser alloc]initWithName:@"猪八戒" pass:@"659"],
                      [[GKHUser alloc]initWithName:@"太上老君" pass:@"743"],
                      [[GKHUser alloc]initWithName:@"银角大王" pass:@"985"],
                      nil];
        NSString *nameValue = @"大王";//%@代表的属性值
        NSString *namePath = @"name";//%K代表的属性名
        NSPredicate *pred2 = [NSPredicate predicateWithFormat:@"%K CONTAINS %@", namePath, nameValue];
        NSSet *newSet = [set filteredSetUsingPredicate:pred2];
        NSLog(@"%@", newSet);
        
        //创建一个谓词，表示pass属性包含$SUBSTR子串
        NSPredicate *predTemplate = [NSPredicate predicateWithFormat:@"%K CONTAINS $SUBSTR", @"pass"];
        //使用NSDictionary指定SUBSTR的值为"43"
        NSPredicate *pred3 = [predTemplate predicateWithSubstitutionVariables:
                              [NSDictionary dictionaryWithObjectsAndKeys:@"43", @"SUBSTR", nil]];
        NSSet *newSet1 = [set filteredSetUsingPredicate:pred3];
        NSLog(@"%@", newSet1);
        
    }
    return 0;
}
```
运行结果如下：
```
user1的name属性是否以字母s开头：1
值大于50的元素：(
    64,
    56
)
====hash====
====hash====
====hash====
====hash====
====hash====
====hash====
====hash====
{(
    <GKHUser[name=金角大王, pass=231]>,
    <GKHUser[name=银角大王, pass=985]>
)}
====hash====
====hash====
{(
    <GKHUser[name=孙悟空, pass=343]>,
    <GKHUser[name=太上老君, pass=743]>
)}

```


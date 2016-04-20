# 18 对象的copy与mutableCopy方法

Tags: Objective-C

---

+ copy方法：copy方法用来复制对象，产生一个不可变副本，不论对象本身是可变的还是不可变的，用copy方法产生的副本都是不可变的副本。比如调用NSMutableString对象的copy方法产生的对象是NSString对象。
+ mutableCopy方法：mutableCopy方法用来复制对象，产生一个可变副本，不论对象本身是可变的还是不可变的，用mutableCopy方法产生的副本都是可变的副本。比如调用NSString对象的mutableCopy方法产生的对象是NSMutableString对象。

下面是代码示例：
**CopyTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSMutableString *book = [NSMutableString stringWithString:@"iOS编程"];
        //复制book的可变副本
        NSMutableString *bookCopy = [book mutableCopy];
        //修改可变副本，原始book不受影响
        [bookCopy replaceCharactersInRange:NSMakeRange(0, 3) withString:@"Android"];
        NSLog(@"book的值为：%@", book);
        NSLog(@"bookCopy的值为：%@", bookCopy);
        
        NSString *str = @"gkh";
        //复制不可变字符串的可变副本
        NSMutableString *strCopy = [str mutableCopy];
        //向可变副本追加字符串
        [strCopy appendString:@".org"];
        NSLog(@"str的值为：%@", str);
        NSLog(@"strCopy的值为：%@", strCopy);
        
        //复制可变字符串的的不可变副本
        NSMutableString *bookCopy2 = [book copy];
        [bookCopy2 appendString:@"abcde"];//运行会产生异常，因为bookCopy2是不可变的
    }
    return 0;
}
```
运行结果如下：
> book的值为：iOS编程
bookCopy的值为：Android编程
str的值为：gkh
strCopy的值为：gkh.org
\*** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'Attempt to mutate immutable object with appendString:'









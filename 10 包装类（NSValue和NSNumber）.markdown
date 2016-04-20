# 10 包装类（NSValue和NSNumber）

Tags: Objective-C

---

NSValue和NSNumber都是包装类，NSValue是NSNumber的父类。
NSValue是一个更通用的包装类，可以包装short、int、long、float、char、指针、对象id等数据项，将这些包装进去后就可以把short、int、long、float、char、指针等添加到NSArray、NSSet等集合中。
NSNumber是一个更为具体的包装类，主要包装C语言的各种数据类型，有如下三种方法，其中xxx表示类型，可以是各种基础类型：

+ `+ numberWithXxx:`：该类方法将基本类型的值包装成NSNumber对象。
+ `- initWithXxx:`：该实例方法先alloc一个NSNumber对象，再用基本类型的值包装成NSNumber对象。
+ `- xxxValue`：该实例方法返回NSNumber对象包装的基本类型的值。

上代码，如下：

**main.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //调用NSNumber类的numberWithXxx:类方法将int型的值包装成NSNumber对象
        NSNumber *num =[NSNumber numberWithInt:20];
        //调用NSNumber类的numberWithXxx:类方法将double型的值包装成NSNumber对象
        NSNumber *de = [NSNumber numberWithDouble:3.4];
        //调用NSNumber对象的xxxValue实例方法获取NSNumber包装对象包装的值
        NSLog(@"%d", [num intValue]);
        NSLog(@"%g", [de doubleValue]);
        
        //调用NSNumber对象的initWithXxx:实例方法，将实际类型包装成NSNumber对象
        NSNumber *ch = [[NSNumber alloc] initWithChar:'A'];
        NSLog(@"%@", ch);
    }
    return 0;
}
```




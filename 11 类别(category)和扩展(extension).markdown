# 11 类别(category)和扩展(extension)

Tags: Objective-C

---

类别(category)：当定义一个类时，需要额外动态添加一些方法，这个时候就要对它进行分类。比如有一个类Person，头文件为Person.h，实现文件为Person.m，在不更改Person.h和Person.m的情况下，需要对Person添加两个分类：study和work，study分类中包含了Person在学习上的所有方法，work分类中包含了Person在工作上的所有方法。最后生成的文件有Person.h、Person.m、Person+study.h、Person+study.m、Person+work.h、Person+work.m。

扩展(extension)：扩展相当于匿名的类别，类别有单独的.h和.m文件，而扩展则用于临时的对类进行扩展，只有一个.h文件表示扩展的头文件，且头文件命名规则为：类名_扩展名.h，没有对应的.m文件，扩展中定义的方法都放在原始类的.m文件中。

下面部分是代码示例：

**Person.h**
```objective-c
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property (nonatomic, assign) int age;
@property (nonatomic, copy) NSString* name;

- (void) showInfo;
@end
```
**Person.m**
```objective-c
#import "Person.h"

@implementation Person

- (void) showInfo {
    NSLog(@"%@的年龄为%d", self.name, self.age);
}

- (void) loveSomebody {
    NSLog(@"%@最近喜欢上一个女孩", self.name);
}
@end
```
**Person+work.h**
```objctive-c
#import "Person.h"

//为Person类添加一个名为work的category
@interface Person (work)
- (void) workForMoney;
- (void) workForDream;
@end
```
**Person+work.m**
```objctive-c
#import "Person+work.h"

//名为work的category的实现
@implementation Person (work)
- (void) workForMoney {
    NSLog(@"%@为了金钱而工作", self.name);
}

- (void) workForDream {
    NSLog(@"%@为了梦想而工作", self.name);
}
@end
```
**Person+study.h**
```objctive-c
#import "Person.h"

//为Person类添加一个名为study的category
@interface Person (study)
- (void) studyEnglish;
- (void) studyMath;
@end
```
**Person+study.m**
```objctive-c
#import "Person+study.h"

//名为study的category的实现
@implementation Person (study)
- (void) studyEnglish {
    NSLog(@"%@喜欢学英语", self.name);
}

- (void) studyMath {
    NSLog(@"%@喜欢学数学", self.name);
}
@end
```
**Person_love.h**
```objctive-c
#import "Person.h"

//为Person类添加一个extension，也可以叫做匿名category
//只有.h文件，实现部分在Person.m中添加
@interface Person ()

- (void) loveSomebody;
@end
```





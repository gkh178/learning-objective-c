# 04 @package访问控制符

Tags: Objective-C

---

访问控制符列表如下：

![屏幕快照 2015-11-28 17.58.14.png-58.8kB][1]

从图中可以看出，package跟protected区别在于protected修饰的成员变量可以被子类访问，而package修饰的成员变量不能被子类访问，但是能被跟该类处于同一映像中的其它文件对其访问。

同一映像：我们可以理解为将若干个文件编译后得到的一个执行文件，而这些文件就处于同一映像下。

以如下代码为例，编译后会生成.out执行文件，Apple.h、Apple.m、AppleTest.m处于同一映像下，因此main()函数可以访问Apple类的@protected成员变量。

**Apple.h**
```objective-c
#import <Foundation/Foundation.h>

@interface Apple : NSObject {
    //被package修饰的成员变量能被跟该类处于同一映像下的程序访问
    //而被protected修饰的成员变量只能被子类访问
    @package
    double _weight;
    
    @protected
    NSString *_color;
}
@end
```

**Apple.m**
```objective-c
#import "Apple.h"

@implementation Apple

@end
```

**AppleTest.m**
```objective-c
#import <Foundation/Foundation.h>
#import "Apple.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Apple *apple = [[Apple alloc]init];
        
        //直接访问处于同一映像下的类的package成员变量
        apple->_weight = 31;
        NSLog(@"苹果的重量为：%f", apple->_weight);
        
        //下面会编译出错
        // apple->_color = [NSString stringWithFormat:@"red"];
        // NSLog(@"苹果的颜色为：%@", apple->_color);
    }
    return 0;
}
```

[1]: http://static.zybuluo.com/gkh178/akjx1r6jx88uww8yk9u9jcfh/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-11-28%2017.58.14.png
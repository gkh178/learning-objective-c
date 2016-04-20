# 01 block的用法

Tags: Objective-C

----

## 1.1 块的定义、块变量的定义
块的定义的基本格式如下：

     ^(形参类型1 形参1, 形参类型2 形参2, ...)
     {
         //块执行体
     }
     
块变量的定义的基本格式如下：

     块返回类型 (^块变量名) (形参类型1, 形参类型2, ...);
     
下面有个代码示例，有关块的用法，可以发现块很像C++中的匿名函数，而块变量则像函数指针。

**BlockTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //定义不带参数、无返回值的块
        void (^printStr) (void) = ^(void) {
            NSLog(@"我正在学习Objective-C的块");
        };
        printStr();
        
        //定义带参数、有返回值的快
        double (^hypot) (double, double) = ^(double num1, double num2) {
            return sqrt(num1 * num1 + num2 * num2);
        };
        NSLog(@"%g", hypot(3, 4));
        
        //定义带参数、无返回值的块
        //这里换个方法表达，先定义块变量
        void (^print) (NSString *);
        //再将块赋值给块变量
        print = ^(NSString *info) {
            NSLog(@"info参数为：%@", info);
        };
        print(@"我是快乐的参数！");
    }
    return 0;
}
```
-----

## 1.2 块与局部变量
代码示例如下：

**BlockAndVar.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
     @autoreleasepool {
         int my = 20; //局部变量
         void (^printMy) (void) = ^(void) {
             //在块内对块外的局部变量赋值会报错，只有访问权限而没有修改权限
             //my = 30;
             NSLog(@"%d", my);
         };
         
         my = 45;//修改局部变量的值
         //程序使用块访问局部变量的时候，系统在定义块时就会将局部变量的值存在块中，
         //而不是在执行时候才去访问变量的值。
         printMy();//结果：20。
     }
    
    return 0;
}
```

**BlockVar.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, char * argv[]) {
    @autoreleasepool {
        //使用__block修饰的局部变量，不仅可以被块访问，同时也可以被块修改
        //而且等到执行块时才去访问、获取局部变量的值，而不是在定义块的时就将值保存到块中
        __block int my = 20;
        
        void (^printMy) (void) = ^(void) {
            NSLog(@"%d", my);
            my = 30;
            NSLog(@"%d", my);
        };
        my = 45;
        printMy();//结果：45 30     运行时my为45,块内再修改为30
        NSLog(@"块执行完后，my的值为：%d", my);//结果：30
    }
    
    return 0;
}
```

------
## 1.3 使用typedef定义块变量类型

使用typedef定义块变量类型的格式如下：
     
     typedef 块返回类型 (^块变量名) (形参类型1, 形参类型2, ...);

**DefBlockType.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, char * argv[]) {
    @autoreleasepool {
        //使用typedef定义块变量类型
        typedef void (^FKPrintBlock) (NSString *);
        //使用块变量类型定义块变量，并将指定块赋值给该块变量
        FKPrintBlock print = ^(NSString *info) {
            NSLog(@"%@", info);
        };
        
        FKPrintBlock loopPrint = ^(NSString *info) {
            for (int i = 0; i < 3; i++) {
                NSLog(@"%@", info);
            }
        };
        
        //调用两个块变量
        print(@"Objective-C");
        loopPrint(@"iOS");
    }
    
    return 0;
}
```
-----
## 1.4 使用块变量类型作为函数的形参

**BlockArgument.m**
```objective-c
#import <Foundation/Foundation.h>

//使用typedef定义一个块变量类型
typedef void (^FKProcessBlock) (int);

//使用该块变量类型对应的块变量作为形参
//一个方法最多只能有一个块变量形参，而且必须作为方法的最后一个形参
void processArray(int array[], unsigned int len, FKProcessBlock process) {
    for (int i = 0; i < len; ++i) {
        process(array[i]);//将数组元素作为参数调用块
    }
}

int main(int argc, char * argv[]) {
    @autoreleasepool {
        int arr[] = {2, 4, 6};
        //使用具体的块定义作为实参，进行方法调用
        processArray(arr, 3, ^(int num) {
            NSLog(@"元素平方为：%d", num * num);//结果：4 16 36
        });
    }
    
    return 0;
}
```




# 16 Foudation框架之NSString和NSMutableString

Tags: Objective-C

---

NSString是NSMutableString的父类，前者代表不可变字符串，后者代表可变字符串，前者初始化后就不可修改，后者可以修改内容。

这两个类的API就不列出来了，代码示例如下：

**main.m**
```objective-c
//
//  main.m
//  07.1
//
//  Created by gankaihua on 15/12/2.
//  Copyright © 2015年 gankaihua. All rights reserved.
//

#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        unichar data[6] = {85, 86, 87, 88, 89, 90};//OC中unsigned short被typedef为unichar
        //使用指定长度的Unicode字符数组初始化NSString对象
        NSString *str = [NSString stringWithCharacters:data length:6];
        NSLog(@"%@", str);
        //使用C风格字符串初始化NSString对象
        char *cstr = "Hello, iOS!";//C风格字符串
        NSString *str2 = [NSString stringWithUTF8String:cstr];
        NSLog(@"%@", str2);
        //使用常量字符串初始化NSString对象
        NSString *str3 = @"iOS编程很有趣3";
        NSLog(@"%@", str3);
        //使用格式化字符串初始化NSString对象
        NSString *temp = @"iOS";
        NSString *str4 = [NSString stringWithFormat:@"%@编程很有趣4", temp];
        NSLog(@"%@", str4);
        //将NSString写入指定文件myFile.txt
        [str4 writeToFile:@"myFile.txt" atomically:YES encoding:NSUTF8StringEncoding error:nil];
        //使用指定文件初始化NSString对象
        NSString *str5 = [NSString stringWithContentsOfFile:@"myFile.txt" encoding:NSUTF8StringEncoding error:nil];
        NSLog(@"%@", str5);
        
        NSString *str6 = @"Hello";
        NSString *book = @"《iOS编程》";
        //在str6后面追加字符串，原来的字符串对象不变，变化的只是拷贝过去的副本
        str6 = [str6 stringByAppendingString:@",iOS!"];
        NSLog(@"%@", str6);
        //获取字符串对应的C风格字符串
        const char *cstr2 = [str6 UTF8String];
        NSLog(@"获取的C风格字符串：%s", cstr2);
        //在str6后面追加格式化的字符串，原来的字符串对象不变，变化的只是拷贝过去的副本
        str6 = [str6 stringByAppendingFormat:@"%@是一本非常不错的书.", book];
        NSLog(@"%@", str6);
        //获取字符串的字符个数
        NSLog(@"str6的字符个数为：%lu", [str6 length]);
        //字符串按照UTF-8字符集解码后字节数
        NSLog(@"str6按UTF-8字符集解码后字节数为：%lu", [str6 lengthOfBytesUsingEncoding:NSUTF8StringEncoding]);
        //获取str6的前10个字符组成的字符串
        NSString *s1 = [str6 substringToIndex:10];
        NSLog(@"%@", s1);
        //获取str6的从索引6开始组成的字符串
        NSString *s2 = [str6 substringFromIndex:6];
        NSLog(@"%@", s2);
        //获取str6从索引6到后面11个字符组成的字符串
        NSString *s3 = [str6 substringWithRange:NSMakeRange(6, 11)];
        NSLog(@"%@", s3);
        //获取iOS再str6中的位置
        NSRange pos = [str6 rangeOfString:@"iOS"];
        NSLog(@"iOS出现在str6的开始位置为：%ld，长度为：%ld", pos.location, pos.length);
        //将str6的所有字符转为大写
        str6 = [str6 uppercaseString];
        NSLog(@"%@", str6);
        
        NSString *book1 = @"《iOS编程》";
        //创建一个NSMutableString对象
        NSMutableString *mstr = [NSMutableString stringWithString:@"Hello"];
        NSLog(@"%@", mstr);
        //追加字符串，原来的字符串对象发生改变
        [mstr appendString:@",iOS!"];
        NSLog(@"%@", mstr);
        //追加格式化字符串，原来的字符串对象发生改变
        [mstr appendFormat:@"%@是一本非常不错的书.", book1];
        NSLog(@"%@", mstr);
        //在字符串指定索引处插入字符串
        [mstr insertString:@"gkh.org" atIndex:6];
        NSLog(@"%@", mstr);
        //删除字符串中指定位置范围的字符串
        [mstr deleteCharactersInRange:NSMakeRange(6, 11)];
        NSLog(@"%@", mstr);
        //将字符串指定位置的字符串替换
        [mstr replaceCharactersInRange:NSMakeRange(6, 7) withString:@"《Java编程思想》"];
        NSLog(@"%@", mstr);
    }
    return 0;
}
```
结果为：
> UVWXYZ
Hello, iOS!
iOS编程很有趣3
iOS编程很有趣4
iOS编程很有趣4
Hello,iOS!
获取的C风格字符串：Hello,iOS!
Hello,iOS!《iOS编程》是一本非常不错的书.
str6的字符个数为：27
str6按UTF-8字符集解码后字节数为：53
Hello,iOS!
iOS!《iOS编程》是一本非常不错的书.
iOS!《iOS编程》
iOS出现在str6的开始位置为：6，长度为：3
HELLO,IOS!《IOS编程》是一本非常不错的书.
Hello
Hello,iOS!
Hello,iOS!《iOS编程》是一本非常不错的书.
Hello,gkh.orgiOS!《iOS编程》是一本非常不错的书.
Hello,《iOS编程》是一本非常不错的书.
Hello,《Java编程思想》是一本非常不错的书.





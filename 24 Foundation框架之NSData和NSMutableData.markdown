# 24 Foundation框架之NSData和NSMutableData

Tags: Objective-C

---

NSData代表了数据缓冲区，主要作用用来封装数据。代码示例如下：

**NSDataTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //使用NSURL指向的URL的内容初始化NSData对象
        NSData *data = [NSData dataWithContentsOfURL:[NSURL URLWithString:@"http://www.crazyit.org/ethos.php"]];
        NSLog(@"%ld", [data length]);
        
        //将NSData中的数据赋给C字符数组
        char buffer[100];
        [data getBytes:buffer range:NSMakeRange(103, 100)];
        NSLog(@"%s", buffer);
        
        //将NSData对象的数据用来初始化NSString
        NSString *content = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"-----输出网页内容-----");
        NSLog(@"%@", content);
    }
    return 0;
}
```

运行结果如下：
```
12802
transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-T…
-----输出网页内容-----
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
...后面部分省略
```



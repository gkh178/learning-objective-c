# 29 Foundation框架之NSURL

Tags: Objective-C

---
NSURL代表了互联网上的一个文件的统一资源定位符。其格式如下：
> schema://host:port/path

如http://www.abc.com:8080/index.php中,scheme为http，host为www.abc.com，port为8080，而path为/index.php。

**NSURLTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建NSURL对象
        NSURL *url = [NSURL URLWithString:@"http://www.crazyit.org/index.php"];
        //获取NSURL对象的scheme
        NSLog(@"url的schema为：%@", [url scheme]);
        //获取NSURL对象的host
        NSLog(@"url的host为：%@", [url host]);
        //获取NSURL对象的port
        NSLog(@"url的port为：%@", [url port]);
        //获取NSURL对象的path
        NSLog(@"url的path为：%@", [url path]);
    }
    return 0;
}
```
运行结果为：
```
url的schema为：http
url的host为：www.crazyit.org
url的port为：(null)
url的path为：/index.php
```





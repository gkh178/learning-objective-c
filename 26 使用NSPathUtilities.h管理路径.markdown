# 26 使用NSPathUtilities.h管理路径

Tags: Objective-C

---
NSPathUtilities.h包含了对NSString的扩展，添加了一些专门用来操作路径的方法。

下面是代码示例:
**NSPathUtilitiesTest.m**
```objective-c
#import <Foundation/Foundation.h>


int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //获取当前用户名
        NSString *userName = NSUserName();
        NSLog(@"当前用户的用户名为：%@", userName);
        //获取当前用户的完整用户名
        NSString *fullUserName = NSFullUserName();
        NSLog(@"当前用户的完整用户名为：%@", fullUserName);
        //获取当前用户的home目录
        NSLog(@"当前用户的home目录为：%@", NSHomeDirectory());
        //获取指定用户的home目录
        NSLog(@"root用户的home目录为：%@", NSHomeDirectoryForUser(@"root"));
        //获取系统的临时目录
        NSLog(@"当前系统的临时目录为：%@", NSTemporaryDirectory());
        
        //将带~符号的路径名扩展成正常的路径名
        NSString *path = @"~root";
        NSLog(@"解析~root的详细路径为：%@", [path stringByExpandingTildeInPath]);
        //将正常的路径名解析为带~符号的路径名
        NSString *path2 = @"/Users/Ralf/Desktop";
        NSLog(@"解析path2为带~形式的结果为：%@", [path2 stringByAbbreviatingWithTildeInPath]);
        
        //使用NSString的pathWithComponents:类方法创建一个路径
        NSString *path3 = [NSString pathWithComponents:[NSArray arrayWithObjects:@"/",@"Users",@"Ralf",@"Desktop",nil]];
        //判断路径名是否是绝对路径
        NSLog(@"path3是否是绝对路径：%d", [path3 isAbsolutePath]);
        
        //对路径名追加一个组件部分构成新路径
        path3 = [path3 stringByAppendingPathComponent:@"1"];
        //对路径名追加一个后缀
        path3 = [path3 stringByAppendingPathExtension:@"m"];
        NSLog(@"添加最后的组件和后缀后，path3的路径为：%@", path3);
        
        //分拆路径，将每个组件作为NSArray的组成部分
        NSArray *array = [path3 pathComponents];
        NSLog(@"path3的组件列表为：%@", array);
        
        //获取路径名的最后组件部分
        NSLog(@"path3的最后组件部分为：%@", [path3 lastPathComponent]);
        //获取路径名的最后组件部分的后缀
        NSLog(@"path3的最后组件部分的后缀名为：%@", [path3 pathExtension]);
        
        //删除路径名最后的后缀
        path3 = [path3 stringByDeletingPathExtension];
        //删除路径名最后的组件部分
        path3 = [path3 stringByDeletingLastPathComponent];
        NSLog(@"删除最后的后缀和组件后，path3的路径为：%@", path3);
    }
    return 0;
}
```
运行结果为：
```
当前用户的用户名为：Ralf
当前用户的完整用户名为：gankaihua
当前用户的home目录为：/Users/Ralf
root用户的home目录为：/var/root
当前系统的临时目录为：/var/folders/6s/xnnpd20547bcrg6fg36zkpnw0000gn/T/
解析~root的详细路径为：/var/root
解析path2为带~形式的结果为：~/Desktop
path3是否是绝对路径：1
添加最后的组件和后缀后，path3的路径为：/Users/Ralf/Desktop/1.m
path3的组件列表为：(
    "/",
    Users,
    Ralf,
    Desktop,
    "1.m"
)
path3的最后组件部分为：1.m
path3的最后组件部分的后缀名为：m
删除最后的后缀和组件后，path3的路径为：/Users/Ralf/Desktop
```

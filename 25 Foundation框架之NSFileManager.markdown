# 25 Foundation框架之NSFileManager

Tags: Objective-C

---

`NSFileManager`表示文件管理器对象，通过该对象可以完成一些文件管理。比如访问指定路径的文件属性及内容，创建、删除、移动、复制文件或目录，查看指定目录下的包含的内容等。

代码测试如下：
**NSFileManagerTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建默认NSFileManager对象
        NSFileManager *fm = [NSFileManager defaultManager];
        NSString *filePath = @"/Users/Ralf/Desktop/Learn/08.2/08.2/NSFileManagerTest.m";//用来测试的文件的路径
        
        
        /*
         *访问指定路径的属性及内容*
         */
        //判断路径是否存在
        NSLog(@"NSFileManagerTest.m是否存在：%d", [fm fileExistsAtPath:filePath]);
        //判断路径是否是目录
        BOOL isDir;
        NSLog(@"NSFileManagerTest.m是否存在：%d", [fm fileExistsAtPath:filePath isDirectory:&isDir]);
        NSLog(@"NSFileManagerTest.m是否为目录：%d", isDir);
        
        //判断路径是否是可读文件
        NSLog(@"NSFileManagerTest.m是否可读：%d", [fm isReadableFileAtPath:filePath]);
        //判断路径是否是可写文件
        NSLog(@"NSFileManagerTest.m是否可写：%d", [fm isWritableFileAtPath:filePath]);
        //判断路径是否是可执行文件
        NSLog(@"NSFileManagerTest.m是否可执行：%d", [fm isExecutableFileAtPath:filePath]);
        //判断路径是否是可删除文件
        NSLog(@"NSFileManagerTest.m是否可删除：%d", [fm isDeletableFileAtPath:filePath]);
        
        //显示路径所在的文件的简单名
        NSString *name = [fm displayNameAtPath:filePath];
        NSLog(@"NSFileManagertTest.m文件所在的路径的简单名为：%@", name);
        //显示路径所在的文件的完整名
        NSArray *array = [fm componentsToDisplayForPath:filePath];
        NSLog(@"NSFileManagertTest.m文件所在的路径的完整名为：%@", array);
        
        //获取路径指定的文件的属性，如创建时间、属主账户、大小
        NSDictionary *attr = [fm attributesOfItemAtPath:filePath error:nil];
        NSLog(@"NSFileManagerTest.m的创建时间为：%@", [attr fileCreationDate]);//文件的创建时间
        NSLog(@"NSFileManagerTest.m的属主账户为：%@", [attr fileOwnerAccountName]);//文件的属主账户
        NSLog(@"NSFileManagerTest.m的文件大小为：%lld", [attr fileSize]);//文件的大小
        
        //获取路径指定的文件的内容并显示
        NSData *data = [fm contentsAtPath:filePath];
        NSString *content = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"----输出文件内容----");
        NSLog(@"%@", content);
        
        
        
        /*
         *文件或目录的创建、复制、移动和删除
         */
        //创建文件./abc.txt
        NSString *parentPath = @"/Users/Ralf/Desktop/Learn/08.2/08.2/";
        NSString *content1 = @"iOS编程很好玩！";
        [fm createFileAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc.txt"]
                    contents:[content1 dataUsingEncoding:NSUTF8StringEncoding] attributes:nil];
        //复制文件./abc.txt到./abcCopy.txt
        [fm copyItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc.txt"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcCopy.txt"]error:nil];
        //移动文件./abcCopy.txt为./abcMove.txt
        [fm moveItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcCopy.txt"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcMove.txt"] error:nil];
        //删除文件./abcMove.txt
        [fm removeItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcMove.txt"] error:nil];
        
        //创建目录./abc/xyz
        [fm createDirectoryAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyz"]withIntermediateDirectories:YES
                       attributes:nil error:nil];
        //复制目录./abc/xyz到./xyzCopy
        [fm copyItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyz"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"xyzCopy"] error:nil];
        //移动目录./xyzCopy到./abc/xyzMove
        [fm moveItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"xyzCopy"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyzMove"] error:nil];
        //删除目录./abc/xyzMove
        [fm removeItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyzMove"] error:nil];
        
        
        /*
         *查看目录下的子目录及文件
         */
        //非递归获取指定目录下的所有文件和子目录
        NSArray *arr = [fm contentsOfDirectoryAtPath:parentPath error:nil];
        NSLog(@"----非递归方式下子目录及文件有----");
        for (id ele in arr) {
            NSLog(@"%@", ele);
        }
        
        //递归获取指定目录下的所有文件和子目录
        NSDirectoryEnumerator *dirEnum = [fm enumeratorAtPath:parentPath];
        NSString *file;
        NSLog(@"----递归方式下子目录及文件有----");
        while (file = [dirEnum nextObject]) {
            NSLog(@"%@", file);
        }
        
        //递归获取指定目录下的所有文件和子目录
        NSArray *arr2 = [fm subpathsAtPath:parentPath];
        NSLog(@"----递归方式下子目录及文件有----");
        for (id ele in arr2) {
            NSLog(@"%@", ele);
        }
        NSLog(@"----递归方式下子目录及文件有----");
        arr2 = [fm subpathsOfDirectoryAtPath:parentPath error:nil];
        for (id ele in arr2) {
            NSLog(@"%@", ele);
        }
    }
    return 0;
}
```
运行结果为：
```
NSFileManagerTest.m是否存在：1
NSFileManagerTest.m是否存在：1
NSFileManagerTest.m是否为目录：0
NSFileManagerTest.m是否可读：1
NSFileManagerTest.m是否可写：1
NSFileManagerTest.m是否可执行：0
NSFileManagerTest.m是否可删除：1
NSFileManagertTest.m文件所在的路径的简单名为：NSFileManagerTest.m
NSFileManagertTest.m文件所在的路径的完整名为：(
    "Macintosh HD",
    "\U7528\U6237",
    Ralf,
    "\U684c\U9762",
    Learn,
    "08.2",
    "08.2",
    "NSFileManagerTest.m"
)
NSFileManagerTest.m的创建时间为：2015-12-07 09:30:23 +0000
NSFileManagerTest.m的属主账户为：Ralf
NSFileManagerTest.m的文件大小为：5725
----输出文件内容----
//
//  main.m
//  08.2
//
//  Created by gankaihua on 15/12/7.
//  Copyright © 2015年 gankaihua. All rights reserved.
//

#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建默认NSFileManager对象
        NSFileManager *fm = [NSFileManager defaultManager];
        NSString *filePath = @"/Users/Ralf/Desktop/Learn/08.2/08.2/NSFileManagerTest.m";//用来测试的文件的路径
        
        
        /*
         *访问指定路径的属性及内容*
         */
        //判断路径是否存在
        NSLog(@"NSFileManagerTest.m是否存在：%d", [fm fileExistsAtPath:filePath]);
        //判断路径是否是目录
        BOOL isDir;
        NSLog(@"NSFileManagerTest.m是否存在：%d", [fm fileExistsAtPath:filePath isDirectory:&isDir]);
        NSLog(@"NSFileManagerTest.m是否为目录：%d", isDir);
        
        //判断路径是否是可读文件
        NSLog(@"NSFileManagerTest.m是否可读：%d", [fm isReadableFileAtPath:filePath]);
        //判断路径是否是可写文件
        NSLog(@"NSFileManagerTest.m是否可写：%d", [fm isWritableFileAtPath:filePath]);
        //判断路径是否是可执行文件
        NSLog(@"NSFileManagerTest.m是否可执行：%d", [fm isExecutableFileAtPath:filePath]);
        //判断路径是否是可删除文件
        NSLog(@"NSFileManagerTest.m是否可删除：%d", [fm isDeletableFileAtPath:filePath]);
        
        //显示路径所在的文件的简单名
        NSString *name = [fm displayNameAtPath:filePath];
        NSLog(@"NSFileManagertTest.m文件所在的路径的简单名为：%@", name);
        //显示路径所在的文件的完整名
        NSArray *array = [fm componentsToDisplayForPath:filePath];
        NSLog(@"NSFileManagertTest.m文件所在的路径的完整名为：%@", array);
        
        //获取路径指定的文件的属性，如创建时间、属主账户、大小
        NSDictionary *attr = [fm attributesOfItemAtPath:filePath error:nil];
        NSLog(@"NSFileManagerTest.m的创建时间为：%@", [attr fileCreationDate]);//文件的创建时间
        NSLog(@"NSFileManagerTest.m的属主账户为：%@", [attr fileOwnerAccountName]);//文件的属主账户
        NSLog(@"NSFileManagerTest.m的文件大小为：%lld", [attr fileSize]);//文件的大小
        
        //获取路径指定的文件的内容并显示
        NSData *data = [fm contentsAtPath:filePath];
        NSString *content = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"----输出文件内容----");
        NSLog(@"%@", content);
        
        
        
        /*
         *文件或目录的创建、复制、移动和删除
         */
        //创建文件./abc.txt
        NSString *parentPath = @"/Users/Ralf/Desktop/Learn/08.2/08.2/";
        NSString *content1 = @"iOS编程很好玩！";
        [fm createFileAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc.txt"]
                    contents:[content1 dataUsingEncoding:NSUTF8StringEncoding] attributes:nil];
        //复制文件./abc.txt到./abcCopy.txt
        [fm copyItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc.txt"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcCopy.txt"]error:nil];
        //移动文件./abcCopy.txt为./abcMove.txt
        [fm moveItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcCopy.txt"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcMove.txt"] error:nil];
        //删除文件./abcMove.txt
        [fm removeItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abcMove.txt"] error:nil];
        
        //创建目录./abc/xyz
        [fm createDirectoryAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyz"]withIntermediateDirectories:YES
                       attributes:nil error:nil];
        //复制目录./abc/xyz到./xyzCopy
        [fm copyItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyz"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"xyzCopy"] error:nil];
        //移动目录./xyzCopy到./abc/xyzMove
        [fm moveItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"xyzCopy"]
                    toPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyzMove"] error:nil];
        //删除目录./abc/xyzMove
        [fm removeItemAtPath:[NSString stringWithFormat:@"%@%@",parentPath,@"abc/xyzMove"] error:nil];
        
        
        /*
         *查看目录下的子目录及文件
         */
        //非递归获取指定目录下的所有文件和子目录
        NSArray *arr = [fm contentsOfDirectoryAtPath:parentPath error:nil];
        NSLog(@"----非递归方式下子目录及文件有----");
        for (id ele in arr) {
            NSLog(@"%@", ele);
        }
        
        //递归获取指定目录下的所有文件和子目录
        NSDirectoryEnumerator *dirEnum = [fm enumeratorAtPath:parentPath];
        NSString *file;
        NSLog(@"----递归方式下子目录及文件有----");
        while (file = [dirEnum nextObject]) {
            NSLog(@"%@", file);
        }
        
        //递归获取指定目录下的所有文件和子目录
        NSArray *arr2 = [fm subpathsAtPath:parentPath];
        NSLog(@"----递归方式下子目录及文件有----");
        for (id ele in arr2) {
            NSLog(@"%@", ele);
        }
        NSLog(@"----递归方式下子目录及文件有----");
        arr2 = [fm subpathsOfDirectoryAtPath:parentPath error:nil];
        for (id ele in arr2) {
            NSLog(@"%@", ele);
        }   
    }
    return 0;
}
----非递归方式下子目录及文件有----
.DS_Store
abc
abc.txt
NSFileManagerTest.m
----递归方式下子目录及文件有----
.DS_Store
abc
abc/xyz
abc.txt
NSFileManagerTest.m
----递归方式下子目录及文件有----
.DS_Store
abc
abc/xyz
abc.txt
NSFileManagerTest.m
----递归方式下子目录及文件有----
.DS_Store
abc
abc/xyz
abc.txt
NSFileManagerTest.m
```





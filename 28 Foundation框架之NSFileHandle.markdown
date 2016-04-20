# 28 Foundation框架之NSFileHandle

Tags: Objective-C

---

前面我们讲过NSFileManager对象，主要用来创建、删除、复制、移动文件或者目录。那么如果需要对文件进行写入和读取操作时候，我们应该使用NSFileHandle，通常来说，使用NSFileHandle的基本步骤如下：

+ 创建一个NSFileHandle用来打开指定的文件（这个文件如果不存在的话会返回nil，必须先用NSFileManager创建文件）
+ 对打开的文件进行IO操作
+ 关闭文件


代码实例如下：
**NSFileHandleTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个NSFileHandle用来打开一个文件，只能读
        NSFileHandle *fh = [NSFileHandle fileHandleForReadingAtPath:@"/Users/Ralf/desktop/Learn/08.5/08.5/NSFileHandleTest.m"];
        if (fh == nil) {
            NSLog(@"Open of NSFileHandleTest.m for reading failed!!");
            return 1;
        }
        
        NSData *data;
        //读取文件的256字节
        data = [fh readDataOfLength:256];
        NSString *content = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"------输出NSFileHandleTest.m的前256字节------");
        NSLog(@"%@", content);
        //读取文件的所有内容
        data = [fh readDataToEndOfFile];
        content = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"------输出NSFileHandleTest.m的所有内容------");
        NSLog(@"%@", content);
        //关闭NSFileHandle打开的文件
        [fh closeFile];
        
        //创建一个NSFileHandle用来打开一个文件abc.txt，可写可读
        NSFileHandle *fh2 = [NSFileHandle fileHandleForUpdatingAtPath:@"/Users/Ralf/desktop/Learn/08.5/08.5/abc.txt"];
        if (fh2 == nil) {
            NSFileManager *fm = [NSFileManager defaultManager];
            [fm createFileAtPath:@"/Users/Ralf/desktop/Learn/08.5/08.5/abc.txt" contents:nil attributes:nil];
            fh2 = [NSFileHandle fileHandleForUpdatingAtPath:@"/Users/Ralf/desktop/Learn/08.5/08.5/abc.txt"];
        }
        
        NSString *str = @"I love iOS programming!!!";
        NSString *str2 = @"PHP";
        NSString *str3 = @"Wonderful!!!";
        
        //向文件写入数据，写完数据后文件指针指向最后
        [fh2 writeData:[str dataUsingEncoding:NSUTF8StringEncoding]];
        //获取文件指针的偏移位置
        NSLog(@"文件指针的偏移位置为：%d",[fh2 offsetInFile]);
        //将文件指针指向指定偏移位置
        [fh2 seekToFileOffset:7];
        //在指定文件指针后追加数据，追加的数据会修改当前位置上已经存在的数据
        [fh2 writeData:[str2 dataUsingEncoding:NSUTF8StringEncoding]];
        //讲文件指针指向文件最后位置
        [fh2 seekToEndOfFile];
        //在文件最后部分追加数据
        [fh2 writeData:[str3 dataUsingEncoding:NSUTF8StringEncoding]];
        
        //在指定偏移量的位置将文件截断，修改文件本身
        [fh2 truncateFileAtOffset:25];
        [fh2 closeFile];
    }
    return 0;
}
```
运行结果：
最后文件abc.txt的内容为
> I love PHP programming!!!



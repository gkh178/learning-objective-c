# 27 Foundation框架之NSProcessInfo

Tags: Objective-C

---

NSProcessInfo对象是用来获取进程的相关信息，包括进程的参数、PID、进程所在的系统的主机名、操作系统名、操作系统版本等等。

**NSProcessInfoTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个NSProcessInfo对象，表示当前进程
        NSProcessInfo *processInfo = [NSProcessInfo processInfo];
        
        //获取运行该进程的参数
        NSArray *arr = [processInfo arguments];
        NSLog(@"运行该程序的参数为：%@", arr);
        //获取该进程的进程标示符
        NSLog(@"该程序的进程标示符(PID)为：%d", [processInfo processIdentifier]);
        //获取该进程的进程名
        NSLog(@"该程序的进程名为：%@", [processInfo processName]);
        //设置该进程的新进程名
        [processInfo setProcessName:@"test"];
        NSLog(@"该程序的新进程名为：%@", [processInfo processName]);
    
        //获取运行该进程的系统的环境变量
        NSLog(@"运行该进程的系统的所有环境变量为：%@", [processInfo environment]);
        //获取运行该进程的主机名
        NSLog(@"运行该进程的主机名为：%@", [processInfo hostName]);
        //获取运行该进程的操作系统
        NSLog(@"运行该进程所在的操作系统为：%ld", [processInfo operatingSystem]);
        //获取运行该进程的操作系统的版本
        NSLog(@"运行该进程所在的操作系统名为：%@", [processInfo operatingSystemName]);
        //获取运行该进程的操作系统的版本
        NSLog(@"运行该进程所在的操作系统的版本为：%@", [processInfo operatingSystemVersionString]);
        
        //获取运行该进程的系统的物理内存
        NSLog(@"运行该进程的系统的物理内存为：%lld", [processInfo physicalMemory]);
        //获取运行该进程的系统的处理器数量
        NSLog(@"运行该进程的系统的处理器数量为：%ld", [processInfo processorCount]);
        //获取运行该进程的系统的处于激活状态的处理器数量
        NSLog(@"运行该进程的系统的处于激活状态的处理器数量为：%ld", [processInfo activeProcessorCount]);
        //获取运行该进程的系统已运行的时间
        NSLog(@"运行该进程的系统的已运行时间为：%f", [processInfo systemUptime]);
    }
    return 0;
}
```
运行结果为：
```
运行该程序的参数为：(
    "/Users/Ralf/Library/Developer/Xcode/DerivedData/08.4-cxiwwsqmttmgtigvhkqmvwotyair/Build/Products/Debug/08.4"
)
该程序的进程标示符(PID)为：1380
该程序的进程名为：08.4
该程序的新进程名为：test
运行该进程的系统的所有环境变量为：{
    "Apple_PubSub_Socket_Render" = "/private/tmp/com.apple.launchd.TCivWAnmdQ/Render";
    "COMMAND_MODE" = unix2003;
    "DYLD_FRAMEWORK_PATH" = "/Users/Ralf/Library/Developer/Xcode/DerivedData/08.4-cxiwwsqmttmgtigvhkqmvwotyair/Build/Products/Debug";
    "DYLD_LIBRARY_PATH" = "/Users/Ralf/Library/Developer/Xcode/DerivedData/08.4-cxiwwsqmttmgtigvhkqmvwotyair/Build/Products/Debug:/usr/lib/system/introspection";
    HOME = "/Users/Ralf";
    LOGNAME = Ralf;
    MallocNanoZone = 0;
    NSUnbufferedIO = YES;
    PATH = "/Applications/Xcode.app/Contents/Developer/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin";
    PWD = "/Users/Ralf/Library/Developer/Xcode/DerivedData/08.4-cxiwwsqmttmgtigvhkqmvwotyair/Build/Products/Debug";
    SECURITYSESSIONID = 186a7;
    SHELL = "/bin/bash";
    "SSH_AUTH_SOCK" = "/private/tmp/com.apple.launchd.foLsEVrdOL/Listeners";
    TMPDIR = "/var/folders/6s/xnnpd20547bcrg6fg36zkpnw0000gn/T/";
    USER = Ralf;
    "XPC_FLAGS" = 0x0;
    "XPC_SERVICE_NAME" = "com.apple.xpc.launchd.oneshot.0x10000003.Xcode";
    "__CF_USER_TEXT_ENCODING" = "0x1F5:0x19:0x34";
    "__XCODE_BUILT_PRODUCTS_DIR_PATHS" = "/Users/Ralf/Library/Developer/Xcode/DerivedData/08.4-cxiwwsqmttmgtigvhkqmvwotyair/Build/Products/Debug";
    "__XPC_DYLD_FRAMEWORK_PATH" = "/Users/Ralf/Library/Developer/Xcode/DerivedData/08.4-cxiwwsqmttmgtigvhkqmvwotyair/Build/Products/Debug";
    "__XPC_DYLD_LIBRARY_PATH" = "/Users/Ralf/Library/Developer/Xcode/DerivedData/08.4-cxiwwsqmttmgtigvhkqmvwotyair/Build/Products/Debug";
}
运行该进程的主机名为：gankaihuademac-mini.local
运行该进程所在的操作系统为：5
运行该进程所在的操作系统名为：NSMACHOperatingSystem
运行该进程所在的操作系统的版本为：Version 10.11.1 (Build 15B42)
运行该进程的系统的物理内存为：17179869184
运行该进程的系统的处理器数量为：4
运行该进程的系统的处于激活状态的处理器数量为：4
运行该进程的系统的已运行时间为：7140.280119
```





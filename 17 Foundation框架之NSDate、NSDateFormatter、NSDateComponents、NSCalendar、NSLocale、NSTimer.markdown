# 17 Foundation框架之NSDate、NSDateFormatter、NSDateComponents、NSCalendar、NSLocale、NSTimer

Tags: Objective-C

---

`NSDate`：日期与时间。
`NSDateFormatter`：日期格式器。用来转换NSDate和NSString。
`NSDateComponents`：日期组件。包含了对NSDate中的year、month、day、day、hour、minute、second、week、weedday等各字段的setter和getter方法。
`NSCalendar`：日历。用来转换NSDate和NSDateComponents。
`NSLocale`：区域。表示区域信息。
`NSTimer`：定时器。用来表示每隔多少秒执行某个对象的方法。

下面有个类的关系图：

![屏幕快照 2015-12-02 16.59.17.png-606kB][1]

代码示例如下：
**NSDateTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //获取当前日期时间
        NSDate *date1 = [NSDate date];
        NSLog(@"%@", date1);
        //获取当前日期开始，一天之后的日期
        NSDate *date2 = [NSDate dateWithTimeIntervalSinceNow:3600*24];
        NSLog(@"%@", date2);
        //获取当前日期开始，3天之前的日期
        NSDate *date3 = [[NSDate alloc] initWithTimeIntervalSinceNow:-3*3600*24];
        NSLog(@"%@", date3);
        //获取从1970年开始，30年后的日期
        NSDate *date4 = [NSDate dateWithTimeIntervalSince1970:30*3600*366*24];
        NSLog(@"%@", date4);
        
        //获取系统当前的Locale
        NSLocale *cn = [NSLocale currentLocale];
        //获取在当前Locale下的日期时间的对应字符串
        NSLog(@"%@", [date1 descriptionWithLocale:cn]);
        //获取两个日期之间较早的那个
        NSDate *earlier = [date1 earlierDate:date2];
        NSLog(@"date1与date2之间较早的是：%@", earlier);
        //获取两个日期之间较晚的那个
        NSDate *later = [date1 laterDate:date2];
        NSLog(@"date1与date2之间较晚的是：%@", later);
        //比较两个日期，compare:方法会返回一个NSComparisionResult枚举值
        //该值有三种：NSOrderedAscending、NSOrderedSame、NSOrderedDescending分别表示递增、相等、递减
        switch ([date1 compare:date3]) {
            case NSOrderedAscending:
                NSLog(@"date1在date3之前");
                break;
            case NSOrderedSame:
                NSLog(@"date1和date3相等");
                break;
            case NSOrderedDescending:
                NSLog(@"date1在date3之后");
                break;
        }
        
        //获取两个日期之间的时间差
        NSLog(@"date1与date3之间差%g秒", [date1 timeIntervalSinceDate:date3]);
        //获取指定时间跟现在时间的时间差
        NSLog(@"date2与现在的时间差%g秒", [date2 timeIntervalSinceNow]);
    }
    return 0;
}
```
运行结果为：
> 2015-12-02 09:20:36 +0000
2015-12-03 09:20:36 +0000
2015-11-29 09:20:36 +0000
2000-01-24 00:00:00 +0000
2015年12月2日 星期三 中国标准时间 17:20:36
date1与date2之间较早的是：2015-12-02 09:20:36 +0000
date1与date2之间较晚的是：2015-12-03 09:20:36 +0000
date1在date3之后
date1与date3之间差259200秒
date2与现在的时间差86400秒

**NSDateFormatterTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个自1970年后30年后的日期时间
        NSDate *dt = [NSDate dateWithTimeIntervalSince1970:24*3600*366*30];
        //创建两个Locale分表代表中国和美国
        NSLocale *locales[] = {[[NSLocale alloc] initWithLocaleIdentifier:@"zh_CN"],[[NSLocale alloc] initWithLocaleIdentifier:@"en_US"]};
        //创建一个NSDateFormatter数组，前4个表示中国下的四种格式的NSDateFormatter，后4个表示美国下的四种格式NS的DateFormatter
        NSDateFormatter *df[8];
        for (int i = 0; i < 2; ++i) {
            df[i * 4] = [[NSDateFormatter alloc] init];
            //设置NSDateFormatter的日期风格
            [df[i * 4] setDateStyle:NSDateFormatterShortStyle];
            //设置NSDateFormatter的时间风格
            [df[i * 4] setTimeStyle:NSDateFormatterShortStyle];
            //设置NSDateFormatter的Locale
            [df[i * 4] setLocale:locales[i]];
            
            df[i * 4 + 1] = [[NSDateFormatter alloc] init];
            //设置NSDateFormatter的日期风格
            [df[i * 4 + 1] setDateStyle:NSDateFormatterMediumStyle];
            //设置NSDateFormatter的时间风格
            [df[i * 4 + 1] setTimeStyle:NSDateFormatterMediumStyle];
            //设置NSDateFormatter的Locale
            [df[i * 4 + 1] setLocale:locales[i]];
            
            df[i * 4 + 2] = [[NSDateFormatter alloc] init];
            //设置NSDateFormatter的日期风格
            [df[i * 4 + 2] setDateStyle:NSDateFormatterLongStyle];
            //设置NSDateFormatter的时间风格
            [df[i * 4 + 2] setTimeStyle:NSDateFormatterLongStyle];
            //设置NSDateFormatter的Locale
            [df[i * 4 + 2] setLocale:locales[i]];
            
            df[i * 4 + 3] = [[NSDateFormatter alloc] init];
            //设置NSDateFormatter的日期风格
            [df[i * 4 + 3] setDateStyle:NSDateFormatterFullStyle];
            //设置NSDateFormatter的时间风格
            [df[i * 4 + 3] setTimeStyle:NSDateFormatterFullStyle];
            //设置NSDateFormatter的Locale
            [df[i * 4 + 3] setLocale:locales[i]];
        }
        
        for (int i = 0; i < 2; ++i) {
            switch (i) {
                case 0:
                    NSLog(@"-----中国日期格式-----");
                    break;
                case 1:
                    NSLog(@"-----美国日期格式-----");
                    break;
            }
            NSLog(@"SHORT格式的日期格式为：%@", [df[i * 4] stringFromDate:dt]);
            NSLog(@"MEDIUM格式的日期格式为：%@", [df[i * 4 + 1] stringFromDate:dt]);
            NSLog(@"LONG格式的日期格式为：%@", [df[i * 4 + 2] stringFromDate:dt]);
            NSLog(@"FULL格式的日期格式为：%@", [df[i * 4 + 3] stringFromDate:dt]);
        }
        
        NSDateFormatter *df2 = [[NSDateFormatter alloc] init];
        //不使用自带的系统提供的格式，自定义NSDateFormatter的格式
       [df2 setDateFormat:@"公元yyyy年MM月DD日HH时mm分"];
        //输出自定义的格式化的日期
        NSLog(@"%@", [df2 stringFromDate:dt]);
        
        NSString *dateStr = @"2015-05-05";
        NSDateFormatter *df3 = [[NSDateFormatter alloc] init];
        //自定义NSDateFormatter的格式
        [df3 setDateFormat:@"yyyy-MM-dd"];
        //将NSString转换为NSDate
        NSDate *date2 = [df3 dateFromString:dateStr];
        NSLog(@"%@", date2);
    }
    return 0;
}
```
运行结果为：
> -----中国日期格式-----
SHORT格式的日期格式为：00/1/24 上午8:00
MEDIUM格式的日期格式为：2000年1月24日 上午8:00:00
LONG格式的日期格式为：2000年1月24日 GMT+8 上午8:00:00
FULL格式的日期格式为：2000年1月24日 星期一 中国标准时间 上午8:00:00
-----美国日期格式-----
SHORT格式的日期格式为：1/24/00, 8:00 AM
MEDIUM格式的日期格式为：Jan 24, 2000, 8:00:00 AM
LONG格式的日期格式为：January 24, 2000 at 8:00:00 AM GMT+8
FULL格式的日期格式为：Monday, January 24, 2000 at 8:00:00 AM China Standard Time
公元2000年01月24日08时00分
2015-05-04 16:00:00 +0000

**NSCalendarTest.m**
```objective-c
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //创建一个代表公历的NSCalendar对象
        NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSCalendarIdentifierGregorian];
        //获取当前的日期
        NSDate *dt = [NSDate date];
        //定义一个时间字段的flag，指定NSDateComponents将会获取NSDate对象的哪些部分
        unsigned unitFlags = NSCalendarUnitYear | NSCalendarUnitMonth | NSCalendarUnitDay
        | NSCalendarUnitHour | NSCalendarUnitMinute | NSCalendarUnitSecond | NSCalendarUnitWeekday;
        
        //获取NSDate的指定时间字段形成的NSDateComponents对象
        NSDateComponents *comp = [gregorian components:unitFlags fromDate:dt];
        //输出NSDateComponents对象的各个字段信息
        NSLog(@"现在是%ld年", comp.year);
        NSLog(@"现在是%ld月", comp.month);
        NSLog(@"现在是%ld日", comp.day);
        NSLog(@"现在是%ld时", comp.hour);
        NSLog(@"现在是%ld分", comp.minute);
        NSLog(@"现在是%ld秒 ", comp.second);
        NSLog(@"现在是星期%ld", comp.weekday);
        
        //再创建一个NSDateComponents对象
        NSDateComponents *comp2 = [[NSDateComponents alloc] init];
        //设置NSDateComponents对象的各字段信息
        comp2.year = 2020;
        comp2.month = 4;
        comp2.day = 22;
        comp2.hour = 18;
        comp2.minute = 8;
        comp2.second = 28;
        //使用NSDateComponents对象来获取对应的NSDate对象
        NSDate *date = [gregorian dateFromComponents:comp2];
        NSLog(@"获取的日期为：%@", date);
    }
    return 0;
}
```
运行结果为：
> 现在是2015年
现在是12月
现在是2日
现在是20时
现在是32分
现在是55秒 
现在是星期4
获取的日期为：2020-04-22 10:08:28 +0000

程序需要让某个方法重复执行，需要借助NSTimer定时器来实现，调用如下代码来启动一个定时器：
1. `[NSTimer scheduledTimerWithTimeInterval:timeInterval invocation:NSInvocation对象 repeats:YES/NO]`，其中timeInterval表示每隔多少秒执行一次任务，repeats如果为YES表示重复执行，NO表示只执行一次，而NSInvocation对象封装了target和target的selector方法，表示调用某个对象的某个方法。整体起来就是定义一个定时器，每隔多少秒调用某个对象的某个方法。
2. `[NSTimer scheduledTimerWithTimeInterval:timeInterval target:targetObject selector:SEL对象 userInfo:aUserInfo repeats:YES/NO]`，其中targetObject表示目标对象，SEL对象是目标对象的某个selector。

调用如下代码来销毁一个定时器：
`[NSTimer对象 invalidate]`


  [1]: http://static.zybuluo.com/gkh178/xm248ch0y460g236dbobgyzn/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-02%2016.59.17.png
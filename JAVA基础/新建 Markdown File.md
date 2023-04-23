# [Java 日期时间（LocalDate、LocalTime、LocalDateTime）](https://blog.csdn.net/yy139926/article/details/124298574)


前言
1、LocalDate API
2、LocalTime API
3、LocalDateTime API
4、转换关系
4.1、LocalDateTime 与 LocalDate 之间的转换
4.2、LocalDateTime 与 Date 之间的转换
4.3、LocalDate 与 Date 之间的转换
4.4、LocalDate 与 String 之间的转换
4.5、LocalTime 与 String 之间的转换
4.6、LocalDateTime 与 String 之间的转换
前言
Java 中 1.8 之前有 date 类，date 类到了 1.8 大部分的方法被弃而且 date 类如果不格式化可读性十分差，而 simpledateformat 方法中 format 和 parse 方法都是线程不安全的。1.8 之后出现了 localdate、localdatetime、localtime 这些类，而这些类使用了 final 来修饰，使得这些类是不可变的，一旦实例化，值就固定了，有点类似于 String 类，所以这些类都是线程安全的。

localdate 年月日、localtime 时分秒、localdatetime 年月日时分秒

SimpleDateFormat 为什么是线程不安全

SimpleDateFormat 类主要负责日期的转换与格式化等操作，在多线程的环境中，使用此类容易造成数据转换及处理的不正确，因为 SimpleDateFormat 类并不是线程安全的，但在单线程环境下是没有问题的。

SimpleDateFormat 在类注释中也提醒大家不适用于多线程场景：


说的很清楚，SimpleDateFormat 不是线程安全的，多线程下需要为每个线程创建不同的实例。不安全的原因是因为使用了 Calendar 这个全局变量


format 方法在执行中，会操作成员变量 calendar 来保存时间 calendar.setTime(date) 。如果 SimpleDateFormat 是一个共享变量，SimpleDateFormat 中的 calendar 也就可以被多个线程访问到，所以问题就出现了。除了 format 方法以外，SimpleDateFormat 的 parse 方法也有同样的问题。至此，我们发现了 SimpleDateFormat 的弊端，所以为了解决这个问题就是不要把 SimpleDateFormat 当做一个共享变量来使用。

解决 SimpleDateFormat 线程不安全的方法

每次使用就创建一个新的 SimpleDateFormat
class DateUtils {
    public static Date parse(String formatPattern, String datestring) throws ParseException {
        return new SimpleDateFormat(formatPattern).parse(datestring);
    }

    public static String format(String formatPattern, Date date) {
        return new SimpleDateFormat(formatPattern).format(date);
    }
}
1
2
3
4
5
6
7
8
9
加锁
class DateUtils {
    private static SimpleDateFormat simpleDateFormat = new SimpleDateFormat("mm:ss");

    public static Date parse(String formatPattern, String datestring) throws ParseException {
        synchronized (simpleDateFormat) {
            return simpleDateFormat.parse(datestring);
        }
    
    }
    
    public static String format(String formatPattern, Date date) {
        synchronized (simpleDateFormat) {
            return simpleDateFormat.format(date);
        }
    }
}
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
使用 ThreadLocal
class DateUtils {
    private static ThreadLocal<DateFormat> threadLocal = new ThreadLocal<DateFormat>() {
        @Override
        protected DateFormat initialValue() {
            return new SimpleDateFormat("mm:ss");
        }
    };

    public static Date parse(String formatPattern, String datestring) throws ParseException {
        return threadLocal.get().parse(datestring);
    }
    
    public static String format(String formatPattern, Date date) {
        return threadLocal.get().format(date);
    }
}
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
Java 8 引入了新的日期时间 API，并引入了线程安全的日期类
Instant：瞬时实例。
LocalDate：本地日期，不包含具体时间 例如：2014-01-14 可以用来记录生日、纪念日、加盟日等。
LocalTime：本地时间，不包含日期。
LocalDateTime：组合了日期和时间，但不包含时差和时区信息。
ZonedDateTime：最完整的日期时间，包含时区和相对UTC或格林威治的时差。
新 API 还引入了 ZoneOffSet 和 ZoneId 类，使得解决时区问题更为简便。文章下面为常用 API 使用

1、LocalDate API
LocalDate类的实例是一个不可变对象，它只提供了简单的日期，并不含当天的时间信息，这个类是不可变的和线程安全的。

方法	描述
now	根据当前时间创建LocalDate对象
of	根据指定年月日创建LocalDate对象
getYear	获得年份
getMonthValue	获得月份
getMonth	获得月份枚举值
getDayOfMonth	获得月份天数（1-31）
getDayOfWeek	获得星期几
getDayOfYear	获得年份中的第几天（1-366）
lengthOfYear	获得当年总天数
lengthOfMonth	获得当月总天数
toEpochDay	与时间纪元（1970年1月1日）相差的天数
plusDays	加天
plusWeeks	加周
plusMonths	加月
plusYears	加年
minusDays	减年
minusWeeks	减周
minusMonths	减月
minusYears	减年
withYear	替换年份
withYear	替换年份
withDayOfMonth	替换日期
withDayOfYear	替换日期
isBefore	是否日期在之前
isAfter	是否日期在之后
isEqual	是否是当前日期
isleapYear	是否是闰年
代码如下（示例）：

// A.获取
//（1）获取当前日期 2022-04-20
System.out.println(LocalDate.now());
//（2）获取指定日期 2014-03-18
System.out.println(LocalDate.of(2014, 3, 18));
//（3）获取日期的年份 2022
System.out.println(LocalDate.now().getYear());
//（4）获取日期的月份 4
System.out.println(LocalDate.now().getMonthValue());
//（5）获取日期的日子 20
System.out.println(LocalDate.now().getDayOfMonth());
//（6）获取日期的星期 WEDNESDAY
System.out.println(LocalDate.now().getDayOfWeek());
//（7）当天所在这一年的第几天 110
System.out.println(LocalDate.now().getDayOfYear());
//（8）获取当年天数 365
System.out.println(LocalDate.now().lengthOfYear());
//（9）获取当月天数 30
System.out.println(LocalDate.now().lengthOfMonth());
//（10）与时间纪元（1970年1月1日）相差的天数，负数表示在时间纪元之前多少天 19102
System.out.println(LocalDate.now().toEpochDay());

// B.运算
//（1）加一天
System.out.println("加1天：" + LocalDate.now().plusDays(1));
//（2）加一周
System.out.println("加1周：" + LocalDate.now().plusWeeks(1));
//（3）加一月
System.out.println("加1月：" + LocalDate.now().plusMonths(1));
//（4）加一年
System.out.println("加1年：" + LocalDate.now().plusYears(1));
//（5）减一天
System.out.println("减1天：" + LocalDate.now().minusDays(1));
//（6）减一周
System.out.println("减1周：" + LocalDate.now().minusWeeks(1));
//（7）减一月
System.out.println("减1月：" + LocalDate.now().minusMonths(1));
//（8）减一年
System.out.println("减1年：" + LocalDate.now().minusYears(1));

// C.替换
//（1）替换年份
System.out.println("替换年份为1：" + LocalDate.now().withYear(1));
//（2）替换月份
System.out.println("替换月份为1：" + LocalDate.now().withMonth(1));
//（3）替换日子
System.out.println("替换日期为1：" + LocalDate.now().withDayOfMonth(1));
//（4）替换天数
System.out.println("替换天数为1：" + LocalDate.now().withDayOfYear(1));

// D.比较
//（1）是否在当天之前
System.out.println("是否在当天之前：" + LocalDate.now().minusDays(1).isBefore(LocalDate.now()));
//（2）是否在当天之后
System.out.println("是否在当天之后：" + LocalDate.now().plusDays(1).isAfter(LocalDate.now()));
//（3）是否在当天
System.out.println("是否在当天：" + LocalDate.now().isEqual(LocalDate.now()));
//（4）是否是闰年
System.out.println("今年是否是闰年：" + LocalDate.now().isLeapYear());
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
2、LocalTime API
LocalTime是一个不可变的时间对象，代表一个时间，格为 时 - 分 - 秒，时间表示为纳秒精度，这个类是不可变的和线程安全的。

方法	描述
static LocalTime now()	获取默认时区的当前时间
static LocalTime now(ZoneId zone)	获取指定时区的当前时间
static LocalTime now(Clock clock)	从指定时钟获取当前时间
of	根据指定的时、分、秒获取LocalTime 实例
getHour	获取小时字段
getMinute	获取分钟字段
getSecond	获取秒字段
getNano	获取纳秒字段
plusHours	增加小时数
plusMinutes	增加分钟数
plusSeconds	增加秒数
plusNanos	增加纳秒数
minusHours	减少小时数
minusMinutes	减少分钟数
minusSeconds	减少秒数
minusNanos	减少纳秒数
compareTo	时间与另一个时间比较
isAfter	检查时间是否在指定时间之后
isBefore	检查时间是否在指定时间之前
代码如下（示例）：

// A.获取
//（1）获取默认时区的当前时间 14:11:31.294
System.out.println(LocalTime.now());
//（2）获取指定时区的当前时间 14:11:31.392
System.out.println(LocalTime.now(ZoneId.of("Asia/Shanghai")));
//（3）从指定时钟获取当前时间 14:11:31.392
System.out.println(LocalTime.now(Clock.systemDefaultZone()));
//（4）指定获取时分秒
System.out.println(LocalTime.of(12, 30, 30));
//（5）指定获取时分
System.out.println(LocalTime.of(12, 30));
//（6）指定获取时分秒纳秒
System.out.println(LocalTime.of(12, 30, 30, 123));
//（7）获取小时字段
System.out.println("时: " + LocalTime.now().getHour());
//（8）获取分钟字段
System.out.println("时: " + LocalTime.now().getMinute());
//（9）获取秒字段
System.out.println("时: " + LocalTime.now().getSecond());
//（10）获取纳秒字段
System.out.println("时: " + LocalTime.now().getNano());

// B.计算
//（1）增加一小时
System.out.println("增加1小时: " + LocalTime.now().plusHours(1));
//（2）增加三十分钟
System.out.println("增加30分钟: " + LocalTime.now().plusMinutes(30));
//（3）增加三十秒
System.out.println("增加30秒: " + LocalTime.now().plusSeconds(30));
//（4）增加一万纳秒
System.out.println("增加10000纳秒:" + LocalTime.now().plusNanos(10000));
//（5）减少一小时
System.out.println("减少1小时: " + LocalTime.now().minusHours(1));
//（6）减少三十分钟
System.out.println("减少30分钟: " + LocalTime.now().minusMinutes(30));
//（7）减少三十秒
System.out.println("减少30秒: " + LocalTime.now().minusSeconds(30));
//（8）减少一万纳秒
System.out.println("减少10000纳秒:" + LocalTime.now().minusNanos(10000));

// C.比较
//（1）时间与另一个时间比较 0（相等）正数（大）负数（小）
System.out.println(LocalTime.now().compareTo(LocalTime.now()));
//（2）检查时间是否在指定时间之后
System.out.println(LocalTime.now().isAfter(LocalTime.now()));
//（3）检查时间是否在指定时间之前
System.out.println(LocalTime.now().isBefore(LocalTime.now()));
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
3、LocalDateTime API
LocalDateTime是一个不可变的日期时间对象，代表日期时间，格式为 年 - 月 - 日 - 时 - 分 - 秒，这个类是不可变的和线程安全的。

方法	描述
static LocalDateTime now()	获取默认时区的当前日期时间
static LocalDateTime now(Clock clock)	从指定时钟获取当前日期时间
static LocalDateTime now(ZoneId zone)	获取指定时区的当前日期时间
static LocalDateTime of(LocalDate date, LocalTime time)	根据日期和时间对象获取LocalDateTime 实例
static LocalDateTime of(int year, Month month, int dayOfMonth, int hour, int minute, int second)	second) 根据指定的年、月、日、时、分、秒获取LocalDateTime 实例
getYear	获取年份
getMonth	使用月份枚举类获取月份
getDayOfMonth	获取日期在该月是第几天
getDayOfWeek	获取日期是星期几
getDayOfYear	获取日期在该年是第几天
getHour	获取小时
getMinute	获取分钟
getSecond	获取秒
getNano	获取纳秒
plusYears	增加年
plusMonths	增加月
plusWeeks	增加周
plusDays	增加天
plusHours	增加小时
plusMinutes	增加分
plusSeconds	增加秒
plusNanos	增加纳秒
minusYears	减少年
minusMonths	减少月
meminusWeeks	减少周
minusDays	减少天
minusHours	减少小时
minusMinutes	减少分
minusSeconds	减少秒
minusNanos	减少纳秒
isEqual	判断日期时间是否相等
isBefore	检查是否在指定日期时间之前
isAfter	检查是否在指定日期时间之后
toLocalDate	获取日期部分
toLocalTime	获取时间部分
代码如下（示例）：

// A.获取
//（1）获取默认时区的当前日期时间
System.out.println(LocalDateTime.now());
//（2）获取指定时区的当前日期时间
System.out.println(LocalDateTime.now(ZoneId.of("Asia/Shanghai")));
//（3）从指定时钟获取当前日期时间
System.out.println(LocalDateTime.now(Clock.systemDefaultZone()));
//（4）根据日期和时间对象获取LocalDateTime实例
System.out.println(LocalDateTime.of(LocalDate.now(), LocalTime.now()));
//（5）根据指定的年、月、日、时、分、秒获取LocalDateTime实例
System.out.println(LocalDateTime.of(2019, 12, 7, 21, 48, 50));
//（6）获取年份
System.out.println("年 : " + LocalDateTime.now().getYear());
//（7）使用月份枚举类获取月份
System.out.println("月（英文） : " + LocalDateTime.now().getMonth());
//（8) 使用月份数字类获取月份
System.out.println(" 月（数字英文）: " + LocalDateTime.now().getMonth().getValue());
//（9）获取日期在该月是第几天
System.out.println("天 : " + LocalDateTime.now().getDayOfMonth());
//（10）获取日期是星期几（英文）
System.out.println("星期几（英文） : " + LocalDateTime.now().getDayOfWeek());
//（11）获取日期是星期几（数字英文）
System.out.println("星期几（数字英文） : " + LocalDateTime.now().getDayOfWeek().getValue());
//（12）获取日期在该年是第几天
System.out.println("本年的第几天 : " + LocalDateTime.now().getDayOfYear());
//（13）获取小时
System.out.println("时: " + LocalDateTime.now().getHour());
//（14）获取分钟
System.out.println("分: " + LocalDateTime.now().getMinute());
//（15）获取秒
System.out.println("秒: " + LocalDateTime.now().getSecond());
//（16）获取纳秒
System.out.println("纳秒: " + LocalDateTime.now().getNano());
//（17）获取日期部分
System.out.println(LocalDateTime.now().toLocalDate());
//（18）获取时间部分
System.out.println(LocalDateTime.now().toLocalTime());

// B.计算
//（1）增加天数
System.out.println("增加天数 : " + LocalDateTime.now().plusDays(1));
//（2）增加周数
System.out.println("增加周数 : " + LocalDateTime.now().plusWeeks(1));
//（3）增加月数
System.out.println("增加月数 : " + LocalDateTime.now().plusMonths(1));
//（4）增加年数
System.out.println("增加年数 : " + LocalDateTime.now().plusYears(1));
//（5）减少天数
System.out.println("减少天数 : " + LocalDateTime.now().minusDays(1));
//（6）减少月数
System.out.println("减少月数 : " + LocalDateTime.now().minusMonths(1));
//（7）减少周数
System.out.println("减少周数 : " + LocalDateTime.now().minusWeeks(1));
//（8）减少年数
System.out.println("减少年数 : " + LocalDateTime.now().minusYears(1));
//（9）增加小时
System.out.println("增加1小时: " + LocalDateTime.now().plusHours(1));
//（10）增加分钟
System.out.println("增加30分钟: " + LocalDateTime.now().plusMinutes(30));
//（11）增加秒数
System.out.println("增加30秒: " + LocalDateTime.now().plusSeconds(30));
//（12）增加纳秒
System.out.println("增加10000纳秒:" + LocalDateTime.now().plusNanos(10000));
//（13）减少小时
System.out.println("减少1小时:" + LocalDateTime.now().minusHours(1));
//（14）减少分钟
System.out.println("减少30分钟:" + LocalDateTime.now().minusMinutes(30));
//（15）减少秒数
System.out.println("减少30秒: " + LocalDateTime.now().minusSeconds(30));
//（16）减少纳秒
System.out.println("减少10000纳秒:" + LocalDateTime.now().minusNanos(10000));

// C.比较
//（1）判断日期时间是否相等
System.out.println(LocalDateTime.now().isEqual(LocalDateTime.now()));
//（2）检查是否在指定日期时间之前
System.out.println(LocalDateTime.now().isBefore(LocalDateTime.now()));
//（3）检查是否在指定日期时间之后
System.out.println(LocalDateTime.now().isAfter(LocalDateTime.now()));
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
4、转换关系
4.1、LocalDateTime 与 LocalDate 之间的转换
//（1）LocalDateTime转化为LocalDate
LocalDateTime localDateTime = LocalDateTime.now();
LocalDate localDate = localDateTime.toLocalDate();
System.out.println(localDate);

//（2）LocalDate转化为LocalDateTime
LocalDate localDate = LocalDate.now();
LocalDateTime localDateTime1 = localDate.atStartOfDay();
LocalDateTime localDateTime2 = localDate.atTime(8,20,33);
LocalDateTime localDateTime3 = localDate.atTime(LocalTime.now());

System.out.println(localDateTime1);
System.out.println(localDateTime2);
System.out.println(localDateTime3);
1
2
3
4
5
6
7
8
9
10
11
12
13
14
4.2、LocalDateTime 与 Date 之间的转换
//（1）LocalDateTime转化为Date
LocalDateTime localDateTime = LocalDateTime.now();
Date date = Date.from(localDateTime.atZone(ZoneId.systemDefault()).toInstant());
System.out.println(date);

//（2）Date转化为LocalDateTime
Date todayDate = new Date();
LocalDateTime ldt = todayDate.toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime();
System.out.println(ldt);
1
2
3
4
5
6
7
8
9
4.3、LocalDate 与 Date 之间的转换
//（1）localDate转化为Date
LocalDate localDate = LocalDate.now();
ZoneId zoneId = ZoneId.systemDefault();
Date date = Date.from(localDate.atStartOfDay().atZone(zoneId).toInstant());
System.out.println(date);

//（2）Date转化为localDate
Date date1 = new Date();
ZoneId zoneId1 = ZoneId.systemDefault();
LocalDate localDate1 = date1.toInstant().atZone(zoneId1).toLocalDate();
System.out.println(localDate1);
1
2
3
4
5
6
7
8
9
10
11
4.4、LocalDate 与 String 之间的转换
//（1）从文本字符串获取LocalDate实例
LocalDate localdate = LocalDate.parse("2022-04-21");
System.out.println(localdate);

//（2）使用特定格式化形式从文本字符串获取LocalDate实例
String str = "2019-03-03";
DateTimeFormatter fmt1 = DateTimeFormatter.ofPattern("yyyy-MM-dd");
LocalDate date = LocalDate.parse(str, fmt1);
System.out.println(date);

//（3）使用特定格式化形式将LocalDate转为字符串
LocalDate today = LocalDate.now();
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("yyyy/MM/dd");
String dateStr = today.format(fmt);
System.out.println(dateStr);
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
4.5、LocalTime 与 String 之间的转换
// （1）字符串转为LocalTime
LocalTime localdate = LocalTime.parse("12:01:02");
System.out.println(localdate);

// （2）使用特定格式化将字符串转为LocalTime
DateTimeFormatter fmt1 = DateTimeFormatter.ofPattern("HH:mm:ss");
LocalTime date = LocalTime.parse("12:01:02", fmt1);
System.out.println(date);

// （3）LocalTime转为字符串
LocalTime toTime = LocalTime.now();
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("HH:mm:ss");
String dateStr = toTime.format(fmt);
System.out.println(dateStr);
1
2
3
4
5
6
7
8
9
10
11
12
13
14
4.6、LocalDateTime 与 String 之间的转换
//（1）字符串转为LocalDateTime
LocalDateTime ldt2 = LocalDateTime.parse("2019-12-07T21:20:06.303995200");
System.out.println(ldt2);

//（2）使用特定格式化将字符串转为LocalDateTime
DateTimeFormatter df1 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
LocalDateTime ldt3 = LocalDateTime.parse("2019-12-07 21:20:06", df1);
System.out.println(ldt3);

//（3）LocalDateTime转为字符串
LocalDateTime today = LocalDateTime.now();
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("yyyy/MM/dd");
String dateStr = today.format(fmt);
System.out.println(dateStr);

```java
package com.hundsun.demo.pojo;
import javafx.util.converter.LocalDateStringConverter;
import java.time.DayOfWeek;
import java.time.LocalDate;
import java.time.Month;
import java.time.Period;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.time.temporal.ChronoField;
import java.time.temporal.ChronoUnit;
import java.time.temporal.TemporalAdjusters;

public class C {
    public static void main(String[] args) {

        // 当前日期
        LocalDate date = LocalDate.now();
        System.out.println("当前日期 = " + date);


        // 创建指定的日期/时间
        LocalDate date1 = LocalDate.of(2020, 6, 4);
        System.out.println("创建指定的日期/时间 = " + date1);


        // 当前日期减去n天
        LocalDate date2 = LocalDate.now();
        LocalDate date3 = date2.minusDays(5);
        System.out.println("当前日期减去n天 = " + date3);

        // 当前日期减去n周
        LocalDate date4 = date2.minusWeeks(1);
        System.out.println("当前日期减去n周 = " + date4);

        // 当前日期减去n月
        LocalDate date5 = date2.minusMonths(1);
        System.out.println("当前日期减去n月 = " + date5);

        // 当前日期减去n年
        LocalDate date6 = date2.minusYears(1);
        System.out.println("当前日期减去n年 = " + date6);

        // 当前日期减去n天
        LocalDate date7 = date2.minus(1, ChronoUnit.DAYS);
        System.out.println("当前日期减去n天 = " + date7);

        // 当前日期减去n周
        LocalDate date8 = date2.minus(1, ChronoUnit.WEEKS);
        System.out.println("当前日期减去n周 = " + date8);

        // 当前日期减去n月
        LocalDate date9 = date2.minus(1, ChronoUnit.MONTHS);
        System.out.println("当前日期减去n月 = " + date9);

        // 当前日期减去n年
        LocalDate date10 = date2.minus(1, ChronoUnit.YEARS);
        System.out.println("当前日期减去n年 = " + date10);

        // 当前日期减去n个世纪 - 1世纪 = 100年
        LocalDate date11 = date2.minus(1, ChronoUnit.CENTURIES);
        System.out.println("当前日期减去n世纪 = " + date11);

        // 当前日期减去n个年代 - 1年代 = 10年
        LocalDate date12 = date2.minus(1, ChronoUnit.DECADES);
        System.out.println("当前日期减去n个10年 = " + date12);

        // 当前日期减去n个时代
        LocalDate date13 = date2.minus(1, ChronoUnit.ERAS);
        System.out.println("当前日期减去n个时代 = " + date13);


        // 当前日期加n天
        LocalDate date15 = date2.plus(1, ChronoUnit.DAYS);
        System.out.println("当前日期加n天 = " + date15);

        // 当前日期加n周
        LocalDate date14 = date2.plus(1, ChronoUnit.WEEKS);
        System.out.println("当前日期加n周 = " + date14);

        // 当前日期加n月
        LocalDate date16 = date2.plus(1, ChronoUnit.MONTHS);
        System.out.println("当前日期加n月 = " + date16);

        // 当前日期加n年
        LocalDate date17 = date2.plus(1, ChronoUnit.YEARS);
        System.out.println("当前日期加n年 = " + date17);


        // 当前日期加n个世纪 - 1世纪 = 100年
        LocalDate date18 = date2.plus(1, ChronoUnit.CENTURIES);
        System.out.println("当前日期加n个世纪 = " + date18);

        // 当前日期加n个年代 - 1年代 = 10年
        LocalDate date19 = date2.plus(1, ChronoUnit.DECADES);
        System.out.println("当前日期加n个年代 = " + date19);


        // 当前日期加n天
        LocalDate date20 = date2.plus(Period.ofDays(1));
        System.out.println("当前日期加n天 = " + date20);

        // 当前日期加n周
        LocalDate date21 = date2.plus(Period.ofWeeks(1));
        System.out.println("当前日期加n周 = " + date21);

        // 当前日期加n月
        LocalDate date22 = date2.plus(Period.ofMonths(1));
        System.out.println("当前日期加n月 = " + date22);

        // 当前日期加n年
        LocalDate date23 = date2.plus(Period.ofYears(1));
        System.out.println("当前日期加n年 = " + date23);

        // 当前日期加n年-n月-n天
        LocalDate date24 = date2.plus(Period.of(1, 1, 1));
        System.out.println("当前日期加n年-n月-n天 = " + date24);

        // 当前日期加n天 - n天 = 结束日期 - 开始日期
        LocalDate date25 = date2.plus(Period.between(LocalDate.of(2022, 1, 1), LocalDate.of(2022, 2, 1)));
        System.out.println("当前日期加n天 - n天 = 结束日期 - 开始日期 = " + date25);


        // 字符串转日期格式化
        LocalDate date27 = new LocalDateStringConverter().fromString("2022-02-01");
        System.out.println("字符串转日期格式化 = " + date27);

        // 日期转字符串格式化
        String dateToString = new LocalDateStringConverter().toString(date2);
        System.out.println("日期转字符串格式化 = " + dateToString);

        // 日期转字符串格式化 - 指定格式
        LocalDateStringConverter converter = new LocalDateStringConverter(DateTimeFormatter.ofPattern("yyyy-MM-dd"), DateTimeFormatter.ofPattern("yyyy/MM/dd"));
        String dateToString2 = converter.toString(date2);
        System.out.println("字符串转日期格式化 - 指定格式 = " + dateToString2);

        // 字符串转日期格式化 - 指定格式
        LocalDate date26 = converter.fromString("2022/06/04");
        System.out.println("日期转字符串格式化 - 指定格式 = " + date26);

        // 日期转字符串格式化 - 指定样式
        LocalDateStringConverter converter1 = new LocalDateStringConverter(FormatStyle.FULL);
        String dateToString3 = converter1.toString(date2);
        System.out.println("日期转字符串格式化 - 指定样式 = " + dateToString3);

        // 日期转字符串格式化 - 指定样式
        LocalDateStringConverter converter2 = new LocalDateStringConverter(FormatStyle.LONG);
        String dateToString4 = converter2.toString(date2);
        System.out.println("日期转字符串格式化 - 指定样式 = " + dateToString4);


        // 日期转字符串格式化 - 指定样式
        LocalDateStringConverter converter3 = new LocalDateStringConverter(FormatStyle.MEDIUM);
        String dateToString5 = converter3.toString(date2);
        System.out.println("日期转字符串格式化 - 指定样式 = " + dateToString5);

        // 日期转字符串格式化 - 指定样式
        LocalDateStringConverter converter4 = new LocalDateStringConverter(FormatStyle.SHORT);
        String dateToString6 = converter4.toString(date2);
        System.out.println("日期转字符串格式化 - 指定样式 = " + dateToString6);


        // 字符串转日期格式化 - 指定格式
        LocalDate date28 = LocalDate.parse("2022/06/01", DateTimeFormatter.ofPattern("yyyy/MM/dd"));
        System.out.println("字符串转日期格式化 - 指定格式 = " + date28);

        // 日期转字符串格式化
        String format = DateTimeFormatter.ofPattern("yyyyMMdd").format(date2);
        System.out.println("日期转字符串格式化 = " + format);


        // 创建指定的日期/时间
        LocalDate date29 = LocalDate.of(2022, Month.APRIL, 1);
        System.out.println("date29 = " + date29);


        // 将当前日期 转为 指定年份的日期
        LocalDate date30 = date2.withYear(2021);
        System.out.println("将当前日期 转为 指定年份的日期  = " + date30);

        // 将当前日期 转为 指定月份的日期
        LocalDate date31 = date2.withMonth(10);
        System.out.println("将当前日期 转为 指定月份的日期 = " + date31);

        // 将当前日期 转为 当年的第n天
        LocalDate date32 = date2.withDayOfYear(10);
        System.out.println("将当前日期 转为 当年的第n天 = " + date32);

        // 将当前日期 转为 当月的第n天
        LocalDate date33 = date2.withDayOfMonth(1);
        System.out.println("将当前日期 转为 当月的第n天 = " + date33);


        // 将当前日期 转为 当年的第n天
        LocalDate date34 = date2.with(ChronoField.DAY_OF_YEAR, 1);
        System.out.println("将当前日期 转为 当年的第n天 = " + date34);

        // 将当前日期 转为 当月的第n天
        LocalDate date35 = date2.with(ChronoField.DAY_OF_MONTH, 1);
        System.out.println("将当前日期 转为 当月的第n天 = " + date35);

        // 当前日期的所在年份
        int year = date2.getYear();
        System.out.println("当前日期的所在年份 = " + year);

        // 当前日期的所在月份
        int monthValue = date2.getMonthValue();
        System.out.println("当前日期的所在月份 = " + monthValue);

        // 当前日期的 在当年的第n天
        int day = date2.getDayOfYear();
        System.out.println("当前日期的 在当年的第n天 = " + day);

        // 当前日期的 在当月的第n天
        int day1 = date2.getDayOfMonth();
        System.out.println("当前日期的 在当月的第n天 = " + day1);

        // 当前日期的 在本周的星期几
        DayOfWeek day2 = date2.getDayOfWeek();
        System.out.println("当前日期的 在本周的星期几 = " + day2);

        // 当前日期的 在本周的第n天
        int value = date2.getDayOfWeek().getValue();
        System.out.println("当前日期的 在本周的第n天 = " + value);

        // 当前日期的 在当年的第n天
        int i = date2.get(ChronoField.DAY_OF_YEAR);
        System.out.println("当前日期的 在当年的第n天 = " + i);

        // 是否是闰年
        boolean leapYear = date2.isLeapYear();
        System.out.println("是否是闰年 = " + leapYear);

        // 判断当前日期 是否在指定日期之后
        boolean after = date2.isAfter(date2.minusYears(1));
        System.out.println("判断当前日期 是否在指定日期之后 = " + after);

        // 判断当前日期 是否在指定日期之前
        boolean before = date2.isBefore(date2.minusYears(1));
        System.out.println("判断当前日期 是否在指定日期之前 = " + before);

        // 判断当前日期 是否和指定日期 相等
        boolean equal = date2.isEqual(date2.minusYears(1));
        System.out.println("判断当前日期 是否和指定日期 相等 = " + equal);


        // 计算两个日期相差的天数
        Period period = Period.between(date2.minusDays(1), date2.plusDays(1));
        long l = period.get(ChronoUnit.DAYS);
        System.out.println("计算两个日期相差的天数 = " + l);

        // 计算两个日期相差的天数
        int days = period.getDays();
        System.out.println("计算两个日期相差的天数 = " + days);

        // 计算两个日期相差的月数
        int months = period.getMonths();
        System.out.println("计算两个日期相差的月数 = " + months);

        // 计算两个日期相差的年数
        int years = period.getYears();
        System.out.println("计算两个日期相差的年数 = " + years);


        // 当前日期的 下个周日
        LocalDate date36 = date2.with(TemporalAdjusters.next(DayOfWeek.SUNDAY));
        System.out.println("当前日期的 下个周日 = " + date36);

        // 当前日期的 当月第一天的日期
        LocalDate date37 = date2.with(TemporalAdjusters.firstDayOfMonth());
        System.out.println("当前日期的 当月第一天的日期 = " + date37);
    }
}
```
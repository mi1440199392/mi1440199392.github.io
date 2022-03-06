```java

package com.hundsun.hswealth.mkm;

import java.time.*;
import java.time.format.DateTimeFormatter;

public class MainApp {
    public static void main(String[] args) {

        LocalDate localDate = LocalDate.now();
        System.out.println(localDate); // 当前日期

        LocalDate date = LocalDate.of(2021, Month.NOVEMBER, 20);
        System.out.println(date); // 指定日期

        LocalDate localDate1 = LocalDate.of(2021, 11, 24);
        System.out.println(localDate1); // 指定日期

        System.out.println(localDate1.getDayOfMonth()); // 当前日期所在月份的 第多少天
        System.out.println(localDate1.getDayOfWeek().getValue()); // 当前日期所在星期的 星期几
        System.out.println(localDate1.getDayOfYear()); // 当前日期所在年份的 第多少天

        System.out.println("---------------------------------------------------------------------------");

        YearMonth yearMonth = YearMonth.now();
        System.out.println(yearMonth); // 当前月份

        YearMonth yearMonth1 = YearMonth.of(2021, Month.JULY);
        System.out.println(yearMonth1); // 指定月份

        YearMonth yearMonth2 = YearMonth.of(2021, 11);
        System.out.println(yearMonth2); // 指定月份

        MonthDay monthDay = MonthDay.now();
        System.out.println(monthDay);

        MonthDay monthDay1 = MonthDay.of(2, 29);
        boolean validYear = monthDay1.isValidYear(2021);
        System.out.println(validYear); // 判断当年是不是闰年, 在2021年有没有2月29

        boolean validLeapYear = Year.of(2012).isLeap();
        System.out.println(validLeapYear); // 判断年份是不是闰年


        System.out.println("---------------------------------------------------------------------------");

        LocalTime localTime = LocalTime.now();
        System.out.println(localTime); // 当前时间 [时/分/秒]

        System.out.println(localTime.getHour()); // 时
        System.out.println(localTime.getMinute()); // 分
        System.out.println(localTime.getSecond()); // 秒


        String dateString = "20211124";
        LocalDate localDate2 = LocalDate.parse(dateString, DateTimeFormatter.BASIC_ISO_DATE);
        System.out.println(localDate2);

        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyyMMdd");
        System.out.println(dateTimeFormatter.parse(dateString));
    }
}

```
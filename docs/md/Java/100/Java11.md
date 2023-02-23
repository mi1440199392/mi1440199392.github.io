```java

package com.hundsun.hswealth.platform.pub.util;
import com.hundsun.broker.base.exception.UfBaseException;
import com.hundsun.hswealth.platform.pub.constant.HSErrorConstants;
import java.text.SimpleDateFormat;
import java.time.Instant;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.ZoneId;
import java.time.ZoneOffset;
import java.time.format.DateTimeFormatter;
import java.util.Calendar;
import java.util.Date;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import org.apache.commons.lang3.StringUtils;

public class DateTimeUtils {
    public static final long MAX_DATE_TIME = 21991231235959L;
    public static final long MILLIS_PER_SECOND = 1000L;
    public static final int HOURS_PER_DAY = 24;
    public static final int MINUTES_PER_HOUR = 60;
    public static final int MINUTES_PER_DAY = 1440;
    public static final int SECONDS_PER_MINUTE = 60;
    public static final int SECONDS_PER_HOUR = 3600;
    public static final int SECONDS_PER_DAY = 86400;
    public static final long MILLIS_PER_DAY = 86400000L;
    public static final long MICROS_PER_DAY = 86400000000L;
    public static final long NANOS_PER_SECOND = 1000000000L;
    public static final long NANOS_PER_MINUTE = 60000000000L;
    public static final long NANOS_PER_HOUR = 3600000000000L;
    public static final long NANOS_PER_DAY = 86400000000000L;
    public static final int NANOS_PER_MILLIS = 1000000;
    public static final long MILLIS_PER_MINUTE = 60000L;
    public static final long INT_DATETIME_MULTIPLIER_SECS_MINUTE = 100L;
    public static final long INT_DATETIME_MULTIPLIER_SECS_HOUR = 10000L;
    public static final long INT_DATETIME_MULTIPLIER_SECS_DAY = 1000000L;
    public static final long INT_DATETIME_MULTIPLIER_SECS_MONTH = 100000000L;
    public static final long INT_DATETIME_MULTIPLIER_SECS_YEAR = 10000000000L;
    public static final int INT_TIME_MULTIPLIER_SECS_MINUTE = 100;
    public static final int INT_TIME_MULTIPLIER_SECS_HOUR = 10000;
    public static final int INT_DATE_MULTIPLIER_SECS_MONTH = 100;
    public static final int INT_DATE_MULTIPLIER_SECS_YEAR = 10000;
    public static final long INT_DATETIME_WITH_MILLIS_MULTIPLIER_SECS_SECOND = 1000L;
    public static final long INT_DATETIME_WITH_MILLIS_MULTIPLIER_SECS_MINUTE = 100000L;
    public static final long INT_DATETIME_WITH_MILLIS_MULTIPLIER_SECS_HOUR = 10000000L;
    public static final long INT_DATETIME_WITH_MILLIS_MULTIPLIER_SECS_DAY = 1000000000L;
    public static final long INT_DATETIME_WITH_MILLIS_MULTIPLIER_SECS_MONTH = 100000000000L;
    public static final long INT_DATETIME_WITH_MILLIS_MULTIPLIER_SECS_YEAR = 10000000000000L;
    private static final ConcurrentMap<String, DateTimeFormatter> DATE_TIME_FORMATTER_CACHE = new ConcurrentHashMap(16);
    public static final String DATETIME_PATTERN = "yyyy-MM-dd HH:mm:ss";
    public static final DateTimeFormatter DATETIME_FORMATTER = getDateTimeFormatterWithDefaultZone("yyyy-MM-dd HH:mm:ss");
    public static final String DATE_PATTERN = "yyyy-MM-dd";
    public static final DateTimeFormatter DATE_FORMATTER = getDateTimeFormatterWithDefaultZone("yyyy-MM-dd");
    public static final String TIME_PATTERN = "HH:mm:ss";
    public static final DateTimeFormatter TIME_FORMATTER = getDateTimeFormatterWithDefaultZone("HH:mm:ss");
    public static final String DATETIME_INT_PATTERN = "yyyyMMddHHmmss";
    public static final DateTimeFormatter DATETIME_INT_FORMATTER = getDateTimeFormatterWithDefaultZone("yyyyMMddHHmmss");
    public static final String DATETIME_WITH_MILLIS_INT_PATTERN = "yyyyMMddHHmmssSSS";
    public static final DateTimeFormatter DATETIME_WITH_MILLIS_INT_FORMATTER = getDateTimeFormatterWithDefaultZone("yyyyMMddHHmmssSSS");
    public static final String DATE_INT_PATTERN = "yyyyMMdd";
    public static final DateTimeFormatter DATE_INT_FORMATTER = getDateTimeFormatterWithDefaultZone("yyyyMMdd");
    public static final String TIME_INT_PATTERN = "HHmmss";
    public static final DateTimeFormatter TIME_INT_FORMATTER = getDateTimeFormatterWithDefaultZone("HHmmss");
    public static final String MILL_TIME_INT_PATTERN = "ssSSS";
    public static final DateTimeFormatter MILL_TIME_INT_FORMATTER = getDateTimeFormatterWithDefaultZone("ssSSS");
    public static final int EPOCH_INT_STR_LEN_RANGE_MIN = 12;
    public static final int EPOCH_INT_STR_LEN_RANGE_MAX = 13;
    public static final long EPOCH_INT_RANGE_MIN = (long)Math.pow(10.0D, 11.0D);
    public static final long EPOCH_INT_RANGE_MAX = (long)Math.pow(10.0D, 13.0D) - 1L;
    private static final String DATE_TIME_REG_EXP = "^(\\d{2,4})([\\-/])(\\d{1,2})\\2(\\d{1,2}) (\\d{1,2}):(\\d{1,2}):(\\d{1,2})$";
    private static final Pattern DATE_TIME_REG_PATTERN = Pattern.compile("^(\\d{2,4})([\\-/])(\\d{1,2})\\2(\\d{1,2}) (\\d{1,2}):(\\d{1,2}):(\\d{1,2})$");
    private static final int DATE_TIME_REG_GROUP_YEAR = 1;
    private static final int DATE_TIME_REG_GROUP_MONTH = 3;
    private static final int DATE_TIME_REG_GROUP_DAY = 4;
    private static final int DATE_TIME_REG_GROUP_HOUR = 5;
    private static final int DATE_TIME_REG_GROUP_MINUTE = 6;
    private static final int DATE_TIME_REG_GROUP_SECOND = 7;
    private static final int DATE_TIME_STR_LEN_MAX = 19;
    private static final String DATE_REG_EXP = "^(\\d{2,4})([\\-/])(\\d{1,2})\\2(\\d{1,2})$";
    private static final Pattern DATE_REG_PATTERN = Pattern.compile("^(\\d{2,4})([\\-/])(\\d{1,2})\\2(\\d{1,2})$");
    private static final int DATE_REG_GROUP_YEAR = 1;
    private static final int DATE_REG_GROUP_MONTH = 3;
    private static final int DATE_REG_GROUP_DAY = 4;
    private static final int DATE_STR_LEN_MAX = 10;
    private static final String DATE_TIME_WITH_MILLIS_REG_EXP = "^(\\d{2,4})([\\-/])(\\d{1,2})\\2(\\d{1,2}) (\\d{1,2}):(\\d{1,2}):(\\d{1,2})\\.(\\d{1,3})$";
    private static final Pattern DATE_TIME_WITH_MILLIS_REG_PATTERN = Pattern.compile("^(\\d{2,4})([\\-/])(\\d{1,2})\\2(\\d{1,2}) (\\d{1,2}):(\\d{1,2}):(\\d{1,2})\\.(\\d{1,3})$");
    private static final int DATE_TIME_WITH_MILLIS_REG_GROUP_YEAR = 1;
    private static final int DATE_TIME_WITH_MILLIS_REG_GROUP_MONTH = 3;
    private static final int DATE_TIME_WITH_MILLIS_REG_GROUP_DAY = 4;
    private static final int DATE_TIME_WITH_MILLIS_REG_GROUP_HOUR = 5;
    private static final int DATE_TIME_WITH_MILLIS_REG_GROUP_MINUTE = 6;
    private static final int DATE_TIME_WITH_MILLIS_REG_GROUP_SECOND = 7;
    private static final int DATE_TIME_WITH_MILLIS_REG_GROUP_MILLI_SEC = 8;
    private static final int DATE_TIME_WITH_MILLIS_STR_LEN_MAX = 23;
    private static final int DATETIME_INT_STR_MONTH_IDX = 4;
    private static final int DATETIME_INT_STR_DAY_IDX = 6;
    private static final int DATETIME_INT_STR_HOUR_IDX = 8;
    private static final int DATETIME_INT_STR_MINUTE_IDX = 10;
    private static final int DATETIME_INT_STR_SECOND_IDX = 12;
    public static final int DATE_TIME_INT_STR_LEN = 14;
    public static final long DATE_TIME_INT_RANGE_MIN = 19000101000000L;
    public static final long DATE_TIME_INT_RANGE_MAX = 29991231235959L;
    private static final int DATE_INT_STR_MONTH_IDX = 4;
    private static final int DATE_INT_STR_DAY_IDX = 6;
    public static final int DATE_INT_STR_LEN = 8;
    public static final int DATE_INT_RANGE_MIN = 19000101;
    public static final int DATE_INT_RANGE_MAX = 29991231;
    public static final int DATETIME_WITH_MILLIS_INT_STR_LEN = 17;
    public static final long DATETIME_WITH_MILLIS_INT_RANGE_MIN = 19000101000000000L;
    public static final long DATETIME_WITH_MILLIS_INT_RANGE_MAX = 29991231235959999L;
    private static final String TIME_REG_EXP = "^(\\d{1,2}):(\\d{1,2}):(\\d{1,2})$";
    private static final Pattern TIME_REG_PATTERN = Pattern.compile("^(\\d{1,2}):(\\d{1,2}):(\\d{1,2})$");
    private static final int TIME_REG_GROUP_HOUR = 1;
    private static final int TIME_REG_GROUP_MINUTE = 2;
    private static final int TIME_REG_GROUP_SECOND = 3;
    private static final int TIME_STR_LEN_MAX = 8;
    private static final String TIME_WITH_MILLIS_REG_EXP = "^(\\d{1,2}):(\\d{1,2}):(\\d{1,2})\\.(\\d{1,3})$";
    private static final Pattern TIME_WITH_MILLIS_REG_PATTERN = Pattern.compile("^(\\d{1,2}):(\\d{1,2}):(\\d{1,2})\\.(\\d{1,3})$");
    private static final int TIME_WITH_MILLIS_REG_GROUP_HOUR = 1;
    private static final int TIME_WITH_MILLIS_REG_GROUP_MINUTE = 2;
    private static final int TIME_WITH_MILLIS_REG_GROUP_SECOND = 3;
    private static final int TIME_WITH_MILLIS_REG_GROUP_MILLI_SEC = 4;
    private static final int TIME_WITH_MILLIS_STR_LEN_MAX = 12;
    public static final int TIME_INT_STR_LEN = 6;
    public static final int TIME_INT_RANGE_MIN = 0;
    public static final int TIME_INT_RANGE_MAX = 235959;
    private static final char MILLIS_DOT = '.';
    private static final int LAST_2_IDX = 2;
    private static final int LAST_3_IDX = 3;
    private static final int LAST_4_IDX = 4;
    private static final String DURATION_FORMAT_REG_PATTERN_STR = "[^Hms]*(([Hms]{1,4})([^Hms]*)){1,3}?";
    private static final Pattern DURATION_FORMAT_REG_PATTERN = Pattern.compile("[^Hms]*(([Hms]{1,4})([^Hms]*)){1,3}?");

    public DateTimeUtils() {
    }

    public static DateTimeFormatter getDateTimeFormatterWithDefaultZone(String pattern) {
        return (DateTimeFormatter)DATE_TIME_FORMATTER_CACHE.computeIfAbsent(pattern, (p) -> {
            return DateTimeFormatter.ofPattern(pattern).withZone(ZoneId.systemDefault());
        });
    }

    public static Date getCurrentDate() {
        return new Date();
    }

    public static LocalDate getCurrentLocalDate() {
        return dateToLocalDate(getCurrentDate());
    }

    public static LocalDateTime getCurrentLocalDateTime() {
        return dateToLocalDateTime(getCurrentDate());
    }

    public static Integer getCurrentDateInt() {
        return formatToDateInt(getCurrentDate());
    }

    public static Integer getCurrentTimeInt() {
        return formatToTimeInt(getCurrentDate());
    }

    public static Long getCurrentDateTimeLong() {
        return formatToDateTimeLong(getCurrentDate());
    }

    public static Long getCurrentTimestampMilli() {
        return getCurrentLocalDateTime().toInstant(ZoneOffset.of("+8")).toEpochMilli();
    }

    public static Date getMaxDate() {
        return parseToDate(21991231235959L);
    }

    public static String format(Integer idate) {
        return format(idate, DATE_FORMATTER);
    }

    public static String format(Date date) {
        return format(date, DATE_FORMATTER);
    }

    public static String format(LocalDate localDate) {
        return format(localDate, DATE_FORMATTER);
    }

    public static String format(LocalDateTime localDateTime) {
        return format(localDateTime, DATETIME_FORMATTER);
    }

    public static String format(Integer idate, String pattern) {
        return format(idate, getDateTimeFormatterWithDefaultZone(pattern));
    }

    public static String format(Date date, String pattern) {
        return format(date, getDateTimeFormatterWithDefaultZone(pattern));
    }

    public static String format(LocalDate localDate, String pattern) {
        return format(localDate, getDateTimeFormatterWithDefaultZone(pattern));
    }

    public static String format(LocalDateTime localDateTime, String pattern) {
        return format(localDateTime, getDateTimeFormatterWithDefaultZone(pattern));
    }

    public static String format(Integer idate, DateTimeFormatter formatter) {
        return format(parseToLocalDateTime(idate), formatter);
    }

    public static String format(Date date, DateTimeFormatter formatter) {
        LocalDateTime localDateTime = dateToLocalDateTime(date);
        return localDateTime == null ? null : formatter.format(localDateTime);
    }

    public static String format(LocalDate localDate, DateTimeFormatter formatter) {
        return localDate == null ? null : formatter.format(localDate);
    }

    public static String format(LocalDateTime localDateTime, DateTimeFormatter formatter) {
        return localDateTime == null ? null : formatter.format(localDateTime);
    }

    public static String format(Integer idate, Integer itime) {
        return format(idate, itime, DATETIME_FORMATTER);
    }

    public static String format(Integer idate, Integer itime, String pattern) {
        return format(idate, itime, getDateTimeFormatterWithDefaultZone(pattern));
    }

    public static String format(Integer idate, Integer itime, DateTimeFormatter formatter) {
        return format(parseToLocalDateTime(idate, itime), formatter);
    }

    public static Long formatToDateTimeLong(Date date) {
        LocalDateTime localDateTime = dateToLocalDateTime(date);
        return localDateTime == null ? null : Long.valueOf(DATETIME_INT_FORMATTER.format(localDateTime));
    }

    public static Long formatToDateTimeLong(LocalDateTime localDateTime) {
        return localDateTime == null ? null : Long.valueOf(DATETIME_INT_FORMATTER.format(localDateTime));
    }

    public static Integer formatToDateInt(Date date) {
        LocalDateTime localDateTime = dateToLocalDateTime(date);
        return localDateTime == null ? null : Integer.valueOf(DATE_INT_FORMATTER.format(localDateTime));
    }

    public static Integer formatToDateInt(LocalDate localDate) {
        return localDate == null ? null : Integer.valueOf(DATE_INT_FORMATTER.format(localDate.atStartOfDay()));
    }

    public static Integer formatToDateInt(LocalDateTime localDateTime) {
        return localDateTime == null ? null : Integer.valueOf(DATE_INT_FORMATTER.format(localDateTime));
    }

    public static Integer formatToTimeInt(Date date) {
        LocalDateTime localDateTime = dateToLocalDateTime(date);
        return localDateTime == null ? null : Integer.valueOf(TIME_INT_FORMATTER.format(localDateTime));
    }

    public static Integer formatToTimeInt(LocalDateTime localDateTime) {
        return localDateTime == null ? null : Integer.valueOf(TIME_INT_FORMATTER.format(localDateTime));
    }

    public static Integer formatToMillTimeInt(Date date) {
        LocalDateTime localDateTime = dateToLocalDateTime(date);
        return localDateTime == null ? null : Integer.valueOf(MILL_TIME_INT_FORMATTER.format(localDateTime));
    }

    public static Integer formatToMillTimeInt(LocalDateTime localDateTime) {
        return localDateTime == null ? null : Integer.valueOf(MILL_TIME_INT_FORMATTER.format(localDateTime));
    }

    public static Date parse(String str) {
        if (StringUtils.isBlank(str)) {
            return null;
        } else {
            try {
                int len = str.length();
                if (8 == len) {
                    return Character.isDigit(str.charAt(len - 2)) && Character.isDigit(str.charAt(len - 3)) ? parseDateForDateIntStr(str) : parseDateForDateReg(str);
                } else if (10 >= len) {
                    return parseDateForDateReg(str);
                } else if (14 == len) {
                    return Character.isDigit(str.charAt(len - 2)) && Character.isDigit(str.charAt(len - 3)) ? parseDateForDateTimeIntStr(str) : parseDateForDateTimeReg(str);
                } else if (19 >= len) {
                    return '.' != str.charAt(len - 2) && '.' != str.charAt(len - 3) && '.' != str.charAt(len - 4) ? parseDateForDateTimeReg(str) : parseDateForDateTimeWithMillisReg(str);
                } else {
                    return 23 >= len ? parseDateForDateTimeWithMillisReg(str) : null;
                }
            } catch (Exception var2) {
                throw new UfBaseException(HSErrorConstants.ERR_PPS_ERROR, "非法日期字符串，解析失败：" + str, var2);
            }
        }
    }

    private static Date parseDateForDateIntStr(String dateIntStr) {
        Calendar c = Calendar.getInstance();
        c.set(Integer.parseInt(dateIntStr.substring(0, 4)), Integer.parseInt(dateIntStr.substring(4, 6)) - 1, Integer.parseInt(dateIntStr.substring(6)), 0, 0, 0);
        c.set(14, 0);
        return c.getTime();
    }

    private static Date parseDateForDateReg(String dateStr) {
        Matcher m = DATE_REG_PATTERN.matcher(dateStr);
        if (m.matches()) {
            Calendar c = Calendar.getInstance();
            c.set(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(3)) - 1, Integer.parseInt(m.group(4)), 0, 0, 0);
            c.set(14, 0);
            return c.getTime();
        } else {
            return null;
        }
    }

    private static Date parseDateForDateTimeIntStr(String dateIntStr) {
        Calendar c = Calendar.getInstance();
        c.set(Integer.parseInt(dateIntStr.substring(0, 4)), Integer.parseInt(dateIntStr.substring(4, 6)) - 1, Integer.parseInt(dateIntStr.substring(6, 8)), Integer.parseInt(dateIntStr.substring(8, 10)), Integer.parseInt(dateIntStr.substring(10, 12)), Integer.parseInt(dateIntStr.substring(12)));
        c.set(14, 0);
        return c.getTime();
    }

    private static Date parseDateForDateTimeReg(String dateStr) {
        Matcher m = DATE_TIME_REG_PATTERN.matcher(dateStr);
        if (m.matches()) {
            Calendar c = Calendar.getInstance();
            c.set(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(3)) - 1, Integer.parseInt(m.group(4)), Integer.parseInt(m.group(5)), Integer.parseInt(m.group(6)), Integer.parseInt(m.group(7)));
            c.set(14, 0);
            return c.getTime();
        } else {
            return null;
        }
    }

    private static Date parseDateForDateTimeWithMillisReg(String dateStr) {
        Matcher m = DATE_TIME_WITH_MILLIS_REG_PATTERN.matcher(dateStr);
        if (m.matches()) {
            Calendar c = Calendar.getInstance();
            c.set(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(3)) - 1, Integer.parseInt(m.group(4)), Integer.parseInt(m.group(5)), Integer.parseInt(m.group(6)), Integer.parseInt(m.group(7)));
            c.set(14, Integer.parseInt(m.group(8)));
            return c.getTime();
        } else {
            return null;
        }
    }

    public static LocalDateTime parseLocalDateTimeString(String str) {
        if (StringUtils.isBlank(str)) {
            return null;
        } else {
            try {
                int len = str.length();
                if (8 == len) {
                    return Character.isDigit(str.charAt(len - 2)) && Character.isDigit(str.charAt(len - 3)) ? parseLocalDateTimeStringForDateIntStr(str) : parseLocalDateTimeStringForDateReg(str);
                } else if (10 >= len) {
                    return parseLocalDateTimeStringForDateReg(str);
                } else if (14 == len) {
                    return Character.isDigit(str.charAt(len - 2)) && Character.isDigit(str.charAt(len - 3)) ? parseLocalDateTimeStringForDateTimeIntStr(str) : parseLocalDateTimeStringForDateTimeReg(str);
                } else if (19 >= len) {
                    return '.' != str.charAt(len - 2) && '.' != str.charAt(len - 3) && '.' != str.charAt(len - 4) ? parseLocalDateTimeStringForDateTimeReg(str) : parseLocalDateTimeStringForDateTimeWithMillisReg(str);
                } else {
                    return 23 >= len ? parseLocalDateTimeStringForDateTimeWithMillisReg(str) : null;
                }
            } catch (Exception var2) {
                throw new UfBaseException(HSErrorConstants.ERR_PPS_ERROR, "非法日期字符串，解析失败：" + str, var2);
            }
        }
    }

    private static LocalDateTime parseLocalDateTimeStringForDateIntStr(String dateIntStr) {
        return LocalDateTime.of(LocalDate.of(Integer.parseInt(dateIntStr.substring(0, 4)), Integer.parseInt(dateIntStr.substring(4, 6)), Integer.parseInt(dateIntStr.substring(6))), LocalTime.MIDNIGHT);
    }

    private static LocalDateTime parseLocalDateTimeStringForDateReg(String dateStr) {
        Matcher m = DATE_REG_PATTERN.matcher(dateStr);
        return m.matches() ? LocalDateTime.of(LocalDate.of(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(3)), Integer.parseInt(m.group(4))), LocalTime.MIDNIGHT) : null;
    }

    private static LocalDateTime parseLocalDateTimeStringForDateTimeIntStr(String dateIntStr) {
        return LocalDateTime.of(LocalDate.of(Integer.parseInt(dateIntStr.substring(0, 4)), Integer.parseInt(dateIntStr.substring(4, 6)), Integer.parseInt(dateIntStr.substring(6, 8))), LocalTime.of(Integer.parseInt(dateIntStr.substring(8, 10)), Integer.parseInt(dateIntStr.substring(10, 12)), Integer.parseInt(dateIntStr.substring(12))));
    }

    private static LocalDateTime parseLocalDateTimeStringForDateTimeReg(String dateStr) {
        Matcher m = DATE_TIME_REG_PATTERN.matcher(dateStr);
        return m.matches() ? LocalDateTime.of(LocalDate.of(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(3)), Integer.parseInt(m.group(4))), LocalTime.of(Integer.parseInt(m.group(5)), Integer.parseInt(m.group(6)), Integer.parseInt(m.group(7)))) : null;
    }

    private static LocalDateTime parseLocalDateTimeStringForDateTimeWithMillisReg(String dateStr) {
        Matcher m = DATE_TIME_WITH_MILLIS_REG_PATTERN.matcher(dateStr);
        return m.matches() ? LocalDateTime.of(LocalDate.of(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(3)), Integer.parseInt(m.group(4))), LocalTime.of(Integer.parseInt(m.group(5)), Integer.parseInt(m.group(6)), Integer.parseInt(m.group(7)), Integer.parseInt(m.group(8)) * 1000000)) : null;
    }

    public static LocalTime parseLocalTimeString(String str) {
        if (StringUtils.isBlank(str)) {
            return null;
        } else {
            try {
                int len = str.length();
                if (6 != len && 5 != len) {
                    if (8 >= len) {
                        return '.' != str.charAt(len - 2) && '.' != str.charAt(len - 3) && '.' != str.charAt(len - 4) ? parseLocalTimeStringForTimeReg(str) : parseLocalTimeStringForTimeWithMillisReg(str);
                    } else {
                        return 12 >= len ? parseLocalTimeStringForTimeWithMillisReg(str) : null;
                    }
                } else {
                    return Character.isDigit(str.charAt(len - 2)) && Character.isDigit(str.charAt(len - 3)) ? parseLocalTimeStringForTimeIntStr(str) : parseLocalTimeStringForTimeReg(str);
                }
            } catch (Exception var2) {
                throw new UfBaseException(HSErrorConstants.ERR_PPS_ERROR, "非法时间字符串，解析失败：" + str, var2);
            }
        }
    }

    private static LocalTime parseLocalTimeStringForTimeIntStr(String timeIntStr) {
        int len = timeIntStr.length();
        return LocalTime.of(Integer.parseInt(timeIntStr.substring(0, len - 4)), Integer.parseInt(timeIntStr.substring(len - 4, len - 2)), Integer.parseInt(timeIntStr.substring(len - 2)));
    }

    private static LocalTime parseLocalTimeStringForTimeReg(String timeStr) {
        Matcher m = TIME_REG_PATTERN.matcher(timeStr);
        return m.matches() ? LocalTime.of(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(2)), Integer.parseInt(m.group(3))) : null;
    }

    private static LocalTime parseLocalTimeStringForTimeWithMillisReg(String dateStr) {
        Matcher m = TIME_WITH_MILLIS_REG_PATTERN.matcher(dateStr);
        return m.matches() ? LocalTime.of(Integer.parseInt(m.group(1)), Integer.parseInt(m.group(2)), Integer.parseInt(m.group(3)), Integer.parseInt(m.group(4)) * 1000000) : null;
    }

    public static LocalDateTime parseLocalDateTimeInt(long dateTimeLong) {
        int year = (int)(dateTimeLong / 10000000000L);
        int month = (int)(dateTimeLong % 10000000000L / 100000000L);
        int day = (int)(dateTimeLong % 100000000L / 1000000L);
        int hour = (int)(dateTimeLong % 1000000L / 10000L);
        int minute = (int)(dateTimeLong % 10000L / 100L);
        int second = (int)(dateTimeLong % 100L);
        return LocalDateTime.of(year, month, day, hour, minute, second);
    }

    public static LocalDate parseLocalDateInt(int dateLong) {
        int year = dateLong / 10000;
        int month = dateLong % 10000 / 100;
        int day = dateLong % 100;
        return LocalDate.of(year, month, day);
    }

    public static LocalTime parseLocalTimeInt(int timeLong) {
        int hour = timeLong / 10000;
        int minute = timeLong % 10000 / 100;
        int second = timeLong % 100;
        return LocalTime.of(hour, minute, second);
    }

    public static LocalDateTime parseLocalDateTimeWithMillisInt(long dateTimeLong) {
        int year = (int)(dateTimeLong / 10000000000000L);
        int month = (int)(dateTimeLong % 10000000000000L / 100000000000L);
        int day = (int)(dateTimeLong % 100000000000L / 1000000000L);
        int hour = (int)(dateTimeLong % 1000000000L / 10000000L);
        int minute = (int)(dateTimeLong % 10000000L / 100000L);
        int second = (int)(dateTimeLong % 100000L / 1000L);
        int millis = (int)(dateTimeLong % 1000L);
        return LocalDateTime.of(year, month, day, hour, minute, second, millis * 1000000);
    }

    public static Date parseToDate(Integer idate) {
        return idate != null && 0 != idate ? parseToDate(String.valueOf(idate)) : null;
    }

    public static LocalDate parseToLocalDate(Integer idate) {
        return idate != null && 0 != idate ? parseToLocalDate(String.valueOf(idate)) : null;
    }

    public static LocalDateTime parseToLocalDateTime(Integer idate) {
        return idate != null && 0 != idate ? parseToLocalDateTime(String.valueOf(idate)) : null;
    }

    public static Date parseToDate(Long ldate) {
        return ldate != null && 0L != ldate ? parseToDate(String.valueOf(ldate)) : null;
    }

    public static LocalDate parseToLocalDate(Long ldate) {
        return ldate != null && 0L != ldate ? parseToLocalDate(String.valueOf(ldate)) : null;
    }

    public static LocalDateTime parseToLocalDateTime(Long ldate) {
        return ldate != null && 0L != ldate ? parseToLocalDateTime(String.valueOf(ldate)) : null;
    }

    private static Date parseToDate(String date) {
        if (StringUtils.isBlank(date)) {
            return null;
        } else if (date.length() == "yyyyMMdd".length()) {
            return localDateToDate(parseToLocalDate(date, "yyyyMMdd"));
        } else {
            return date.length() == "yyyyMMddHHmmss".length() ? localDateTimeToDate(parseToLocalDateTime(date, "yyyyMMddHHmmss")) : null;
        }
    }

    private static LocalDate parseToLocalDate(String date) {
        if (StringUtils.isBlank(date)) {
            return null;
        } else if (date.length() == "yyyyMMdd".length()) {
            return parseToLocalDate(String.valueOf(date), "yyyyMMdd");
        } else {
            return date.length() == "yyyyMMddHHmmss".length() ? localDateTimeToLocalDate(parseToLocalDateTime(String.valueOf(date), "yyyyMMddHHmmss")) : null;
        }
    }

    private static LocalDateTime parseToLocalDateTime(String date) {
        if (StringUtils.isBlank(date)) {
            return null;
        } else if (date.length() == "yyyyMMdd".length()) {
            return localDateToLocalDateTime(parseToLocalDate(date, "yyyyMMdd"));
        } else {
            return date.length() == "yyyyMMddHHmmss".length() ? parseToLocalDateTime(date, "yyyyMMddHHmmss") : null;
        }
    }

    public static Date parseToDate(Integer idate, Integer itime) {
        return localDateTimeToDate(parseToLocalDateTime(idate, itime));
    }

    public static LocalDateTime parseToLocalDateTime(Integer idate, Integer itime) {
        if (idate != null && itime != null && 0 != idate) {
            String dttm = idate + StringUtils.leftPad(String.valueOf(itime), 6, "0");
            return dttm.length() == "yyyyMMddHHmmss".length() ? parseToLocalDateTime(dttm, "yyyyMMddHHmmss") : null;
        } else {
            return null;
        }
    }

    public static Date parseToDate(String date, String pattern) {
        try {
            return localDateTimeToDate(parseToLocalDateTime(date, pattern));
        } catch (Exception var3) {
            return localDateToDate(parseToLocalDate(date, pattern));
        }
    }

    public static LocalDate parseToLocalDate(String date, String pattern) {
        return date == null ? null : LocalDate.parse(date, getDateTimeFormatterWithDefaultZone(pattern));
    }

    public static LocalDateTime parseToLocalDateTime(String date, String pattern) {
        return date == null ? null : LocalDateTime.parse(date, getDateTimeFormatterWithDefaultZone(pattern));
    }

    public static LocalDate dateToLocalDate(Date date) {
        return localDateTimeToLocalDate(dateToLocalDateTime(date));
    }

    public static LocalDateTime dateToLocalDateTime(Date date) {
        return date == null ? null : LocalDateTime.ofInstant(date.toInstant(), ZoneId.systemDefault());
    }

    public static Date localDateToDate(LocalDate localDate) {
        return localDate == null ? null : localDateTimeToDate(localDate.atStartOfDay());
    }

    public static LocalDateTime localDateToLocalDateTime(LocalDate localDate) {
        return localDate == null ? null : localDate.atStartOfDay();
    }

    public static Date localDateTimeToDate(LocalDateTime localDateTime) {
        if (localDateTime == null) {
            return null;
        } else {
            ZoneId zone = ZoneId.systemDefault();
            Instant instant = localDateTime.atZone(zone).toInstant();
            return Date.from(instant);
        }
    }

    public static LocalDate localDateTimeToLocalDate(LocalDateTime localDateTime) {
        return localDateTime == null ? null : localDateTime.toLocalDate();
    }

    public static Long toTimestampSecond(LocalDateTime localDateTime) {
        return localDateTime == null ? null : localDateTime.toEpochSecond(ZoneOffset.of("+8"));
    }

    public static Long toTimestampMilli(LocalDateTime localDateTime) {
        return localDateTime == null ? null : localDateTime.toInstant(ZoneOffset.of("+8")).toEpochMilli();
    }

    public static LocalDateTime timestampToLocalDateTime(Long timestampMilli) {
        Instant instant = Instant.ofEpochMilli(timestampMilli);
        return LocalDateTime.ofInstant(instant, ZoneId.systemDefault());
    }

    public static Long dateTimeStrToLong(String dtStr) {
        return StringUtils.isBlank(dtStr) ? null : Long.parseLong(StringUtil.PATTERN_NON_WORD.matcher(dtStr).replaceAll(""));
    }

    public static Integer longDateTimeToIntDate(Long dt) {
        return null == dt ? null : (int)(dt / 1000000L);
    }

    public static Integer longDateTimeToIntTime(Long dt) {
        return null == dt ? null : (int)(dt % 1000000L);
    }

    public static String formatSecondsDuration(long seconds, String format) {
        if (0L <= seconds && !StringUtils.isBlank(format)) {
            long hours = seconds / 3600L;
            int minutes = (int)(seconds % 3600L / 60L);
            int secs = (int)(seconds % 60L);
            StringBuffer sBuffer = new StringBuffer(format.length());
            Matcher matcher = DURATION_FORMAT_REG_PATTERN.matcher(format);

            while(matcher.find()) {
                String durationSymbol = matcher.group(2);
                byte var12 = -1;
                switch(durationSymbol.hashCode()) {
                case 72:
                    if (durationSymbol.equals("H")) {
                        var12 = 0;
                    }
                    break;
                case 109:
                    if (durationSymbol.equals("m")) {
                        var12 = 2;
                    }
                    break;
                case 115:
                    if (durationSymbol.equals("s")) {
                        var12 = 4;
                    }
                    break;
                case 2304:
                    if (durationSymbol.equals("HH")) {
                        var12 = 1;
                    }
                    break;
                case 3488:
                    if (durationSymbol.equals("mm")) {
                        var12 = 3;
                    }
                    break;
                case 3680:
                    if (durationSymbol.equals("ss")) {
                        var12 = 5;
                    }
                }

                String durationUnitString;
                switch(var12) {
                case 0:
                    durationUnitString = Long.toString(hours);
                    break;
                case 1:
                    durationUnitString = Long.toString(hours);
                    if (durationUnitString.length() < 2) {
                        sBuffer.append('0');
                    }
                    break;
                case 2:
                    durationUnitString = Long.toString((long)minutes);
                    break;
                case 3:
                    durationUnitString = Long.toString((long)minutes);
                    if (durationUnitString.length() < 2) {
                        sBuffer.append('0');
                    }
                    break;
                case 4:
                    durationUnitString = Long.toString((long)secs);
                    break;
                case 5:
                    durationUnitString = Long.toString((long)secs);
                    if (durationUnitString.length() < 2) {
                        sBuffer.append('0');
                    }
                    break;
                default:
                    durationUnitString = "";
                }

                matcher.appendReplacement(sBuffer, durationUnitString);
                sBuffer.append(matcher.group(3));
            }

            matcher.appendTail(sBuffer);
            return sBuffer.toString();
        } else {
            return "";
        }
    }

    public static void main(String[] args) throws Exception {
        System.out.println(toTimestampMilli(getCurrentLocalDateTime()));
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
        System.out.println(simpleDateFormat.format(simpleDateFormat.parse("2-3-4 5:6:7.8")));
    }
}

```
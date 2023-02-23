```java

public static void main(String[] args) {

  // 字符串从后向前反向搜索, 搜索指定字符串在 源字符串的索引位置, 搜索不到返回-1
  String str = "com_hundsun_customer";
  int index = str.lastIndexOf("_");
  System.out.println("index = " + index);

  // 字符串从前向后正向搜索, 搜索指定字符串在 源字符串的索引位置, 搜索不到返回-1
  int index1 = str.indexOf("_");
  System.out.println("index1 = " + index1);

  // 从字符串的指定索引开始搜索, 从后向前反向搜索, 搜索指定字符串在 源字符串的索引位置, 搜索不到返回-1
  int index2 = str.lastIndexOf("_", 10);
  System.out.println("index2 = " + index2);

  // 从字符串的指定索引开始搜索, 从前向后正向搜索, 搜索指定字符串在 源字符串的索引位置, 搜索不到返回-1
  int index3 = str.indexOf("_", 10);
  System.out.println("index3 = " + index3);

  // 字符串的开头 是否为指定字符
  boolean boo = str.startsWith("c");
  boolean boo1 = str.startsWith("o");
  System.out.println("boo = " + boo);
  System.out.println("boo1 = " + boo1);

  // 字符串的结尾 是否为指定字符
  boolean boo2 = str.endsWith("r");
  boolean boo3 = str.endsWith("e");
  System.out.println("boo2 = " + boo2);
  System.out.println("boo3 = " + boo3);

  // 比较两个字符串, 返回一个整数, ==为0, >为正数, <为负数
  String str1 = "com_hundsun_customer";
  int compare = str.compareTo(str1);
  System.out.println("compare = " + compare);

  // 比较两个字符串, 返回一个整数, ==为0, >为正数, <为负数  忽略大小写
  String str2 = "COM_HUNDSUN_CUSTOMER";
  int compare2 = str.compareToIgnoreCase(str2);
  System.out.println("compare2 = " + compare2);

  /**
  * 比较字符串的中的区域 与 指定字符串的中的区域 是否相等
  * 
  * @param ignoreCase 是否忽略大小写, true忽略, false不忽略
  * @param toffset    指定从源字符串的某个索引开始比较
  * @param other      目标字符串
  * @param ooffset    指定从目标字符串的某个索引开始比较
  * @param len        比较n个长度
  */
  String str3 = "com_hundsun_broker";
  boolean matches = str3.regionMatches(true, 4, "org_hundsun_customer", 4, 9);
  System.out.println("matches = " + matches);

  // 比较两个字符串的值是否相等
  boolean equals = str.equals(str1);
  System.out.println("equals = " + equals);

  // 比较两个字符串的值是否相等 忽略大小写
  boolean ignoreCase = str.equalsIgnoreCase(str1);
  System.out.println("ignoreCase = " + ignoreCase);
}

```
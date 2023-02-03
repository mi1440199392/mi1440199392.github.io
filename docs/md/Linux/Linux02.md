# 语法

```text
选项:
    -c  创建新档案
    -t  列出档案目录
    -x  从档案中提取指定的 (或所有) 文件
    -u  更新现有档案
    -v  在标准输出中生成详细输出
    -f  指定档案文件名
    -m  包含指定清单文件中的清单信息
    -n  创建新档案后执行 Pack200 规范化
    -e  为捆绑到可执行 jar 文件的独立应用程序
    -0  仅存储; 不使用任何 ZIP 压缩
    -P  保留文件名中的前导 '/' (绝对路径) 和 ".." (父目录) 组件
    -M  不创建条目的清单文件
    -i  为指定的 jar 文件生成索引信息
    -C  更改为指定的目录并包含以下文件
```

# 查看 jar 包指定依赖的版本

<font face="幼圆">

例如：查看`apache-scrm-demo-3.7.0.0-SNAPSHOT.jar`里`platform`依赖的版本

</font>


```text
[root@linuxtest8fbd apache-scrm-demo]# jar vtf apache-scrm-demo-3.7.0.0-SNAPSHOT.jar | grep platform
130187 Mon Dec 12 22:38:28 CST 2022 BOOT-INF/lib/platform-biz-oplog-domain-3.12.0.0-SNAPSHOT.jar
102300 Mon Dec 12 22:38:32 CST 2022 BOOT-INF/lib/platform-biz-oplog-app-3.12.0.0-SNAPSHOT.jar
 73056 Mon Dec 12 22:39:44 CST 2022 BOOT-INF/lib/platform-biz-search-app-3.12.0.0-SNAPSHOT.jar
  9959 Mon Dec 12 22:39:40 CST 2022 BOOT-INF/lib/platform-biz-search-domain-3.12.0.0-SNAPSHOT.jar
 10259 Mon Dec 12 22:39:32 CST 2022 BOOT-INF/lib/platform-biz-scheduler-app-3.12.0.0-SNAPSHOT.jar
  4462 Mon Dec 12 22:39:30 CST 2022 BOOT-INF/lib/platform-biz-scheduler-domain-3.12.0.0-SNAPSHOT.jar
  1950 Mon Dec 12 22:35:30 CST 2022 BOOT-INF/lib/platform-biz-common-app-3.12.0.0-SNAPSHOT.jar
  1898 Mon Dec 12 22:35:30 CST 2022 BOOT-INF/lib/platform-biz-common-domain-3.12.0.0-SNAPSHOT.jar
 17624 Mon Dec 12 22:38:52 CST 2022 BOOT-INF/lib/platform-biz-workflow-domain-3.12.0.0-SNAPSHOT.jar
 21922 Mon Dec 12 22:38:36 CST 2022 BOOT-INF/lib/platform-biz-file-domain-3.12.0.0-SNAPSHOT.jar
  3468 Mon Dec 12 22:37:28 CST 2022 BOOT-INF/lib/platform-biz-omc-domain-3.12.0.0-SNAPSHOT.jar
 13500 Mon Dec 12 22:37:54 CST 2022 BOOT-INF/lib/platform-biz-notify-domain-3.12.0.0-SNAPSHOT.jar
 14818 Mon Dec 12 22:39:20 CST 2022 BOOT-INF/lib/platform-biz-form-domain-3.12.0.0-SNAPSHOT.jar
  8443 Mon Dec 12 22:39:26 CST 2022 BOOT-INF/lib/platform-biz-dataimport-domain-3.12.0.0-SNAPSHOT.jar
478137 Mon Dec 12 22:38:04 CST 2022 BOOT-INF/lib/platform-biz-dataauth-domain-3.12.0.0-SNAPSHOT.jar
325284 Mon Dec 12 22:38:24 CST 2022 BOOT-INF/lib/platform-biz-grid-domain-3.12.0.0-SNAPSHOT.jar
```

# 语法

<font face="幼圆">

- unzip -l apache-demo-3.7.0.0-SNAPSHOT.jar | grep platform
- zipinfo apache-demo-3.7.0.0-SNAPSHOT.jar | grep platform
- jar vtf apache-demo-3.7.0.0-SNAPSHOT.jar | grep platform
- zipinfo -M apache-demo-3.7.0.0-SNAPSHOT.jar | grep platform   以分页形式显示内容


</font>

# 查看 jar 包指定依赖的版本

<font face="幼圆">

例如：查看`apache-demo-3.7.0.0-SNAPSHOT.jar`里`platform`依赖的版本

</font>


```text
[root@linuxtest8fbd apache-scrm-demo]# jar vtf apache-demo-3.7.0.0-SNAPSHOT.jar | grep platform
130187 Mon Dec 12 22:38:28 CST 2022 BOOT-INF/lib/platform-biz-oplog-domain-3.12.0.0-SNAPSHOT.jar
102300 Mon Dec 12 22:38:32 CST 2022 BOOT-INF/lib/platform-biz-oplog-app-3.12.0.0-SNAPSHOT.jar
 73056 Mon Dec 12 22:39:44 CST 2022 BOOT-INF/lib/platform-biz-search-app-3.12.0.0-SNAPSHOT.jar
  9959 Mon Dec 12 22:39:40 CST 2022 BOOT-INF/lib/platform-biz-search-domain-3.12.0.0-SNAPSHOT.jar
 10259 Mon Dec 12 22:39:32 CST 2022 BOOT-INF/lib/platform-biz-scheduler-app-3.12.0.0-SNAPSHOT.jar
  4462 Mon Dec 12 22:39:30 CST 2022 BOOT-INF/lib/platform-biz-scheduler-domain-3.12.0.0-SNAPSHOT.jar
```

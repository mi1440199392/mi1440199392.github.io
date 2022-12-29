> jar 命令语法

```shell
用法: jar {ctxui}[vfmn0PMe] [jar-file] [manifest-file] [entry-point] [-C dir] files ...
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
        指定应用程序入口点
    -0  仅存储; 不使用任何 ZIP 压缩
    -P  保留文件名中的前导 '/' (绝对路径) 和 ".." (父目录) 组件
    -M  不创建条目的清单文件
    -i  为指定的 jar 文件生成索引信息
    -C  更改为指定的目录并包含以下文件
如果任何文件为目录, 则对其进行递归处理。
清单文件名, 档案文件名和入口点名称的指定顺序
与 'm', 'f' 和 'e' 标记的指定顺序相同。

示例 1: 将两个类文件归档到一个名为 classes.jar 的档案中:
       jar cvf classes.jar Foo.class Bar.class
示例 2: 使用现有的清单文件 'mymanifest' 并
           将 foo/ 目录中的所有文件归档到 'classes.jar' 中:
       jar cvfm classes.jar mymanifest -C foo/ .
```

> 查看 jar 包指定依赖的版本

> 查看 apache-scrm-demo-3.7.0.0-SNAPSHOT.jar 里 platform 依赖的版本
```shell
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
  1863 Mon Dec 12 22:35:30 CST 2022 BOOT-INF/lib/platform-fwk-springcloud-domain-3.12.0.0-SNAPSHOT.jar
329277 Mon Dec 12 22:38:58 CST 2022 BOOT-INF/lib/platform-biz-workflow-app-3.12.0.0-SNAPSHOT.jar
6090918 Mon Dec 12 22:38:36 CST 2022 BOOT-INF/lib/platform-biz-file-app-3.12.0.0-SNAPSHOT.jar
 81903 Mon Dec 12 15:03:08 CST 2022 BOOT-INF/lib/platform-biz-omc-app-3.12.0.0-SNAPSHOT.jar
 18205 Mon Dec 12 22:37:58 CST 2022 BOOT-INF/lib/platform-biz-notify-app-3.12.0.0-SNAPSHOT.jar
116329 Mon Dec 12 22:39:22 CST 2022 BOOT-INF/lib/platform-biz-form-app-3.12.0.0-SNAPSHOT.jar
 36420 Mon Dec 12 22:39:28 CST 2022 BOOT-INF/lib/platform-biz-dataimport-app-3.12.0.0-SNAPSHOT.jar
 62352 Mon Dec 12 22:38:10 CST 2022 BOOT-INF/lib/platform-biz-dataauth-app-3.12.0.0-SNAPSHOT.jar
477459 Mon Dec 12 22:39:06 CST 2022 BOOT-INF/lib/platform-biz-grid-app-3.12.0.0-SNAPSHOT.jar
 30013 Mon Dec 12 22:39:34 CST 2022 BOOT-INF/lib/platform-fwk-jrescloud-app-3.12.0.0-SNAPSHOT.jar
  1846 Mon Dec 12 22:35:30 CST 2022 BOOT-INF/lib/platform-fwk-jrescloud-domain-3.12.0.0-SNAPSHOT.jar
190205 Mon Dec 12 22:40:00 CST 2022 BOOT-INF/lib/platform-biz-hcreator-app-3.12.0.0-SNAPSHOT.jar
542452 Mon Dec 12 22:36:20 CST 2022 BOOT-INF/lib/platform-pub-3.12.0.0-SNAPSHOT.jar
2681931 Wed Oct 30 17:37:12 CST 2019 BOOT-INF/lib/jna-platform-5.5.0.jar
  1835 Mon Dec 12 22:35:30 CST 2022 BOOT-INF/lib/platform-biz-hcreator-domain-3.12.0.0-SNAPSHOT.jar
140651 Mon Dec 12 15:02:18 CST 2022 BOOT-INF/lib/platform-biz-base-domain-3.12.0.0-SNAPSHOT.jar
224608 Mon Dec 12 22:39:56 CST 2022 BOOT-INF/lib/platform-biz-wdp-app-3.12.0.0-SNAPSHOT.jar
321366 Mon Dec 12 22:37:06 CST 2022 BOOT-INF/lib/platform-biz-base-app-3.12.0.0-SNAPSHOT.jar
  1814 Mon Dec 12 22:35:30 CST 2022 BOOT-INF/lib/platform-biz-wdp-domain-3.12.0.0-SNAPSHOT.jar
 99589 Sun Sep 08 20:32:18 CST 2019 BOOT-INF/lib/junit-platform-launcher-1.5.2.jar
163866 Sun Sep 08 20:32:18 CST 2019 BOOT-INF/lib/junit-platform-engine-1.5.2.jar
 91709 Sun Sep 08 20:32:14 CST 2019 BOOT-INF/lib/junit-platform-commons-1.5.2.jar
```

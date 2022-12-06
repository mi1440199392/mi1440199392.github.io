> **tree** 命令的功能是用于以树状图形式列出目录内容，帮助运维人员快速了解到目录的层级关系
> 
> 语法格式：**tree** [参数]

| 参数       | 描述                    |
|----------|-----------------------|
| -a	      | 显示所有文件和目录             |
| -A       | ASNI绘图字符形式            |
| -C       | 彩色显示                  |
| -d       | 仅显示目录名称               |
| -D       | 显示文件更改时间              |
| -f       | 显示完整的相对路径名称           |
| -g       | 显示文件所属群组名称            |
| -i       | 不以阶梯状列出文件或目录名称        |
| -I<范本样式> | 不显示符合范本样式的文件或目录名称     |
| -l       | 直接显示连接文件所指向的原始目录      |
| -n       | 不在文件和目录清单上加上色彩        |
| -N       | 直接列出文件和目录名称           |
| -p       | 列出权限标示                |
| -P<范本样式> | 只显示符合范本像是的文件或目录名称     |
| -q       | 用“?”号取代控制字符，列出文件和目录名称 |
| -s       | 列出文件或目录大小             |
| -t       | 用文件和目录的更改时间排序         |
| -u       | 列出文件或目录的拥有者名称         |
| -x       | 将范围局限在现行的文件系统中        |
| -L       | 层级显示                  |


> 参考实例:

显示当前目录下的文件层级情况:

- -d  仅显示目录名称
- -L 2 显示2层


```shell
-- 

[root@svr127 app-project]# tree -d -L 2
.
├── arthas-output
├── config
├── fragment
│   └── admin
├── hcreator-resources
│   ├── 20200316_demof
│   └── runningConfigs
├── app-project-resources
│   └── BIZ
├── logs
│   ├── App-2022-08
│   ├── App-2022-09
│   ├── app-demo-2022-09
│   ├── app-demo-2022-10
│   ├── app-demo-2022-11
│   ├── app-demo-2022-12
│   ├── biz-demo-2022-09
│   ├── biz-demo-2022-10
│   ├── biz-demo-2022-11
│   ├── biz-demo-2022-12
│   ├── bizerr-demo-2022-09
│   ├── bizerr-demo-2022-10
│   ├── bizerr-demo-2022-11
│   ├── bizerr-demo-2022-12
│   ├── BizerrLog-2022-08
│   ├── BizerrLog-2022-09
│   ├── BizLog-2022-08
│   ├── BizLog-2022-09
│   ├── error-demo-2022-09
│   ├── error-demo-2022-10
│   ├── error-demo-2022-11
│   ├── error-demo-2022-12
│   ├── sql-demo-2022-09
│   ├── sql-demo-2022-10
│   ├── sql-demo-2022-11
│   ├── sql-demo-2022-12
│   ├── SqlLog-2022-08
│   ├── SqlLog-2022-09
│   ├── syserr-demo-2022-09
│   ├── syserr-demo-2022-10
│   ├── syserr-demo-2022-11
│   ├── syserr-demo-2022-12
│   ├── SyserrLog-2022-08
│   ├── SyserrLog-2022-09
│   ├── trace-demo-2022-09
│   ├── trace-demo-2022-10
│   ├── trace-demo-2022-11
│   ├── trace-demo-2022-12
│   ├── TraceLog-2022-08
│   └── TraceLog-2022-09
├── mq
│   └── cache
├── scripts
└── tmp
    ├── database
    ├── monitor
    ├── scripts
    ├── sqls
    └── template
```

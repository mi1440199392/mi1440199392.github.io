#### 1、起因
> 因 **IDEA** 编译工程缓慢，尝试调整　**-Xms**　**-Xmx**参数，修改 **idea64.exe.vmoptions** 文件配置；
> 问题是解决掉了，结果第二天点击 **IDEA** 启动不了

> 从 *GitHub* 存储库下载 *OpenSSH*  https://github.com/PowerShell/OpenSSH-Portable

#### 2、解决

> 恢复 **C**　盘下 **idea64.exe.vmoptions** 配置和 **IDEA** 安装目录 **bin** 下的 **idea64.exe.vmoptions** 配置

> C盘配置路径：C:\Users\hspcadmin\AppData\Roaming\JetBrains\IdeaIC2021.2\idea64.exe.vmoptions

#### 3、IDEA编译工程缓慢问题解决

> Shared build process heap size (Mbytes)， 翻译后：共享构建过程堆大小(兆字节)

![](./../../assets/img/a3.png)
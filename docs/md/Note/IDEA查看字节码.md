在*JDK*工具包的*bin*目录下，有一个*java*可执行文件*javap*即*javap.exe*，该工具可以查看*java*编译后的*class*文件，使用命令*javap -c Test.class*

###### 因此可以基于该命令在*IDEA*中设置宏，来快捷使用*javap*查看字节码，设置如图：

![](/docs/assets/img/a.png)

`Arguments：-c $FileNameWithoutExtension$.class  `
`Working directory：$OutputPath$\$FileDirRelativeToSourcepath$ `

---

###### *IDEA*查看某个类对应的字节码文件

![](/docs/assets/img/b.png)

!> *需要注意的是：查看某个类对应的字节码文件之前确保它已经被编译过*
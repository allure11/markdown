# Shell脚本

## Shell是什么？

- shell是一个命令行解释器，他为用户提供了一个面向Linux内核发送请求以便运行程序的界面系统程序，用户可以使用Shell来启动、挂起、停止甚至是编写一些程序。

![image-20220117212534631](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220117212534631.png)

- shell还是一个功能相当强大的编程语言，易编写、易调试，灵活性强。Shell是解释执行的脚本语言，在Shell中可以直接调用Linux命令

## Shell的分类

Bourne Shell：从1979年起，Unix系统就开始使用，Bourne Shell的主文件名为sh

C Shell：C Shell主要是用在BSD版本的Unix系统中，其语法和C语言相似而得名

> Bourne Shell 和C Shell是两种不同的语言，语法彼此不兼容
>
> Bourne Shell主要包括 sh、ksh、Bash（现在的Linux标准，与sh兼容）、psh、zsh
>
> C Shell主要包括 csh、tcsh

## Linux支持的Shell

查看方法（位于`/etc/shells`文件中）：[root@localhost ~]# vim /etc/shells 

# Shell脚本的执行方式

## 第一个脚本

建议将shell脚本的后缀写成`.sh`，有利于帮助我们区分文件类型

**编写脚本**

```shell
#!/bin/bash
#      第一行的	 #!/bin/Bash  不是注释，以后的 # 表示注释
# 		#!/bin/Bash 中的Bash是区分大小写的，查看 /etc/shells 文件，进行修改；如果不匹配就不能直接 ./hello.shell 执行文件
#The first program
echo -e "Hello Shell :)"
```

**赋予执行权限**

[root@localhost Test]# chmod 755 hello.sh 

**执行脚本**

通过bash执行：bash hello.sh

直接通过路径执行（绝对路径和相对路径都可以）：./hello.sh

<font color="red">**注意：由于windows和Linux的回车符不同，windows下写的脚本需要通过`[root@localhost Test]# unix2dos hello.sh `将其转换成linux格式**</font>

# Shell命令

## echo 输出命令

[root@localhost ~]# echo 【选项】 【输出内容】

-e 支持反斜线控制字符转换

有感叹号`!`时，输出内容必须加双引号，因为感叹号在shell中哟特殊的意义

颜色输出：[root@localhost ~]# echo -e "\e[1;31m abcd\e[0m"

> 30m=黑色	31m=红色	32m=绿色	33m=黄色	34m=蓝色	35m=洋红色	36m=青色	37m=白色

## history  历史命令

[root@localhost ~]# history 【选项】 【历史目录保存文件】

-c 清空历史命令（没有特殊情况不要使用）

-w 强制把缓存中的历史命令写入历史命令保存文件，否则会在正常退出后保存（默认保存在家目录下的 .bash_history）

历史目录默认保存1000条，可以在环境变量配置文件 `/etc/profile` 中进行修改。但是必须重新登录才能生效

![image-20220119204313116](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220119204313116.png)

> 可以使用上、下箭头调用以前的命令
>
> 可以使用 `!n` 执行第n条历史命令 
>
> 使用 `!!` 重复执行上一条命令
>
> 使用 `!字符串` 重复执行最后一条以该字符串开头的命令

 ## alias  命令别名

[root@localhost ~]# alias 别名='原命令'

删除别名：[root@localhost /]# unalias 别名

例如：alias vi='vim' (当敲击 `vi` 命令时，就相当于 `vim`，会显示颜色)

查询别名：[root@localhost ~]# alias 

**命令的执行时顺序**

> 第一顺位执行用绝对路径或相对路径执行的命令
>
> 第二顺位执行别名
>
> 第三顺位执行Bash的内部命令
>
> 第四顺位执行按照 $PATH 环境变量定义的目录查找顺序找到的第一个命令

<font color="red">注意：当使用alias添加的别名只会在当前生效，一旦重启系统就是失效</font>

**让别名永久生效**

修改 `/root/.bashrc` ，设置系统的默认别名

[root@localhost ~]# vim /root/.bashrc 

![image-20220119211352013](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220119211352013.png)

## Bash常用快捷键

ctrl+l　　为clear即清屏

ctrl+a　　跳转命令首处

ctrl+e　　跳转命令尾部

ctrl+c　　取消命令执行

ctrl+k　　删除光标到行尾

ctrl+u　　删除光标到行首

ctrl+r　　搜索历史命令

## 输入输出重定向

### 标准输入输出

|  设备  | 设备文件名  | 文件描述符 |     类型     |
| :----: | :---------: | :--------: | :----------: |
|  键盘  | /dev/stdin  |     0      |   标准输入   |
| 显示器 | /dev/sdtout |     1      |   标准输出   |
| 显示器 | /dev/sdterr |     2      | 标准错误输出 |

### 输出重定向（把命令执行的结果保存到文件中）

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/20190514150119510.png)

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/20190514150920867.png)

`/dev/null` 相当于垃圾堆，`[root@localhost Test]# hs &>/dev/null ` 表示不管结果是否正确都不保存

### 输入重定向

例如：命令 < 文件

统计命令：[root@localhost Test]# wc 【选项】 【文件名】

-c 统计字节数

-w 统计单词书

-l 统计行数

![image-20220120202048885](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220120202048885.png)	

## 多命令顺序执行与管道符

### 多命令顺序执行

![image-20220120204647764](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220120204647764.png)

 ### 管道符

命令格式：命令1 | 命令2

作用：命令1的正确输出作为命令2的操作对象

## 通配符和其他特殊符号

### 通配符

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/20179183927950.png)

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/20179184115805.png)


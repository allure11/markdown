# C语言

## 安装编译环境 gcc

下载mingw-x64：[mingw](https://onboardcloud.dl.sourceforge.net/project/mingw-w64/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/installer/mingw-w64-install.exe)

添加环境变量 Path：指向mingw的bin目录（里面有gcc.exe文件）

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211221232441950.png" alt="image-20211221232441950" style="zoom: 67%;" />

打开cmd，运行 `gcc -v`：查看是否安装成功

## 第一个c语言程序

记事本中写入代码：

```c
#include<stdio.h>
void main(){
printf("Hello World");
getchar;
}
```

将文件后缀改为`.c`

打开cmd，运行 `gcc -o 01.exe 01.c `将 `01.c `转换成 `01.exe `文件

执行 `01` (文件名)运行 `01.exe` 文件，得到输出结果：

![image-20211221233030452](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20211221233030452.png)


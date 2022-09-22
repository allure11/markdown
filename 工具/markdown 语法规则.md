# markdown 语法规则

[TOC]

### 标题

##### 使用 # 标记

使用 **#** 号可表示 1-6 级标题，一级标题对应一个 **#** 号，二级标题对应两个 **#** 号，以此类推。

```markdown
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

### 格式

##### 首行缩进

可以使用tab键缩进

##### 字体

Markdown 可以使用以下几种字体，也可以使用<font>标签设置字体颜色和大小，颜色可用十六进制数表示：

```markdown
*斜体文本*
_斜体文本_
**粗体文本**
__粗体文本__
***粗斜体文本***
___粗斜体文本___
<font face="黑体" color="red" size=4>红色的字</font>
```

示例：

​		*斜体文本*
​		_斜体文本_
​		**粗体文本**
​		__粗体文本__
​		***粗斜体文本***
​		___粗斜体文本___

​		<font face="黑体" color="#ff0000" size=3>红色的字</font>

##### 分割线

你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。下面每种写法都可以建立分隔线：

```markdown
***
*****
---
----------
```

示例：

---

##### 删除线

如果段落上的文字要添加删除线，只需要在文字的两端加上两个波浪线 **~~** 即可，实例如下：

```markdown
~~BAIDU.COM~~
```

效果：~~test~~

##### 下划线

下划线可以通过 HTML 的 **\<u\>** 标签来实现：

```markdown
<u>带下划线文本</u>
```

示例：<u>带下划线文本</u>

##### 脚注

脚注是对文本的补充说明。Markdown 脚注的格式如下:

```markdown
[^要注明的文本]
```

示例：Test[^test是需要注明的文本]

### 列表

##### 无序列表

无序列表使用星号(**\***)、加号(**+**)或是减号(**-**)作为列表标记，__这些标记后面要添加一个空格__，然后再填写内容：

```markdown
* 第一项
* 第二项
* 第三项

+ 第一项
+ 第二项
+ 第三项

- 第一项
- 第二项
- 第三项
```

示例：

* 第一项

+ 第二项

##### 有序列表

有序列表使用数字并加上 **.** 号来表示，如：

```markdown
1. 第一项
2. 第二项
3. 第三项
```

示例：

1. 第一项
2. 第二项

##### 列表嵌套

列表嵌套只需在子列表中的选项前面添加四个空格即可：

```markdown
1. 第一项：
    - 第一项嵌套的第一个元素
    - 第一项嵌套的第二个元素
2. 第二项：
    - 第二项嵌套的第一个元素
    - 第二项嵌套的第二个元素
```

示例：

1. 第一项：
    - 第一项嵌套的第一个元素
2. 第二项：
    - 第二项嵌套的第一个元素

##### 区块

Markdown 区块引用是在段落开头使用 **>** 符号 ，然后后面紧跟一个**空格**符号：

```markdown
> 区块引用
> 学习技术永不止步
> 学的不仅是技术更是梦想
```

示例：

> 区块引用
>
> > 学习技术永不止步
> >
> > > 学的不仅是技术更是梦想

###### 可以在区块中引用列表，也可以在列表中引用区块

在区块中使用列表：

>区块
>
>1. 列表
>
>- 列表

在列表中使用区块

1. 列表

   >区块

- 列表

  >区块

### 代码

##### 段落上的代码片段可以使用  ` 将其隔开

```markdown
`main()`函数
```

示例：`main()`函数

##### 代码区块

你也可以用 **```** 包裹一段代码，并指定一种语言（也可以不指定）；

可以在``` 后面指定语言，也可以在写完代码后，区块的右下角指定语言：

```markdown
```public static void main(){}```
```

示例：

```java
public static void main（）{}
```

### 链接

##### 链接名称+链接地址

```markdown
[百度](www.baidu.com)
```

示例：[百度](https://www.baidu.com)

##### 直接使用地址

```markdown
<https://www.baidu.com>
```

示例：<https://www.baidu.com>

##### 高级链接

我们可以通过变量来设置一个链接，变量赋值在文档末尾进行：

```markdown
这个链接用 1 作为网址变量 [Google][1]
这个链接用 runoob 作为网址变量 [Runoob][runoob]
然后在文档的结尾为变量赋值（网址）

  [1]: http://www.google.com/
  [runoob]: http://www.runoob.com/
```

示例：

第一个链接 [百度官网][1]

[1]: https://www.baidu.com

### 图片

##### 语法格式

- 开头一个感叹号 !
- 接着一个方括号，里面放上图片的替代文字
- 接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上选择性的 'title' 属性的文字。

```markdown
![alt 属性文本](图片地址)
![alt 属性文本](图片地址 "可选标题")
```

示例：

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/-151a37ae538fba6e.jpg" alt="alt width" style="zoom:25%;" title="标题" />

你也可以像网址那样对图片网址使用变量（ctrl+鼠标左键点击才能查看）:

```markdown
这个链接用 1 作为网址变量 [RUNOOB][1]
然后在文档的结尾为变量赋值（网址）
[1]: http://static.runoob.com/images/runoob-logo.png
```

示例：

[RUNOOB][2]

[2]:http://static.runoob.com/images/runoob-logo.png

还可以使用普通的html格式的 <img> 标签展示图片：

```markdown
<img src="http://static.runoob.com/images/runoob-logo.png" width="50%">
```

示例：

<img src="http://static.runoob.com/images/runoob-logo.png" width="50%">

### 表格

制作表格使用 **|** 来分隔不同的单元格，使用 **-** 来分隔表头和其他行。

语法格式如下：

```
|  表头   | 表头  |
|  ----  | ----  |
| 单元格  | 单元格 |
| 单元格  | 单元格 |
```

示例：

| 表头   | 表头   |
| ------ | ------ |
| 单元格 | 单元格 |

##### 对齐方式

我们可以设置表格的对齐方式：

- **-:** 设置内容和标题栏居右对齐。
- **:-** 设置内容和标题栏居左对齐。
- **:-:** 设置内容和标题栏居中对齐。

```markdown
| 左对齐 | 右对齐 | 居中对齐 |
| :-----| ----: | :----: |
| 单元格 | 单元格 | 单元格 |
| 单元格 | 单元格 | 单元格 |
```

示例：

| 左对齐 | 右对齐 | 居中对齐 |
| :----- | -----: | :------: |
| 单元格 | 单元格 |  单元格  |

### 高级技巧

##### html标签

不在 Markdown 涵盖范围之内的标签，都可以直接在文档里面用 HTML 撰写。

目前支持的 HTML 元素有：`<kbd> <b> <i> <em> <sup> <sub> <br>`等 ，如：

```markdown
使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd> 重启电脑
```

示例：

使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd> 重启电脑

##### 转义字符

Markdown 使用了很多特殊符号来表示特定的意义，如果需要显示特定的符号则需要使用转义字符，Markdown 使用反斜杠转义特殊字符：

```markdown
**文本加粗** 
\*\* 正常显示星号 \*\*
```

示例：

​		**文本加粗**

​		\*\*显示星星\*\*

支持以下这些符号前面加上反斜杠来帮助插入普通的符号：

```markdown
\   反斜线
`   反引号
*   星号
_   下划线
{}  花括号
[]  方括号
()  小括号
#   井字号
+   加号
-   减号
.   英文句点
!   感叹号
```

##### 公式

当你需要在编辑器中插入数学公式时，可以使用两个美元符 $$ 包裹 TeX 或 LaTeX 格式的数学公式来实现。提交后，问答和文章页会根据需要加载 Mathjax 对数学公式进行渲染。

```markdown
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
${$tep1{\style{visibility:hidden}{(x+1)(x+1)}}
$$

```

示例：
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
${$tep1}{\style{visibility:hidden}{(x+1)(x+1)}}
$$

##### 特殊字符

通过字符的Unicode码表示特殊字符：

```markdown
&#10084;
```

示例：&#10084;

可在[Unicode编码查询](https://unicode-table.com)查看字符对应的Unicode编码

##### 锚点

```markdown
语法如下,路径可以是任意标题名称，例如跳转到开头“markdown 语法规则”：
跳转到[目录](#“markdown 语法规则”)
```

示例：

[markdown 语法规则](#markdown 语法规则)

##### 显示目录结构

```markdown
[TOC]
```


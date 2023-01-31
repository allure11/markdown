# HTML

在网页中HTML专门用来负责网页的结构，所以在使用html标签时，应该关注的是标签的语义，而不是它的样式

## 标签的类型

块元素（block elem）：标题标签都是块元素  p标签也是一个块元素

- 在网页中一般通过块元素来对页面进行布局

行内元素（inline element）

- 行内元素主要用来包裹文字
- 一般情况下会在块元素中放行内元素，而不会在行内元素中放块元素
- 块元素中基本上什么都能放
- p元素中不能放任何的块元素

用于结构布局的元素 id 命名： 

- 主容器： main   页头： header   页脚： footer   内容区域： content
- LOGO： logo  主导-航： main-nav  二级导航： sub-nav

**<font color=red>严禁多 div 症、多 span 症、多 table 症，正确使用标签表现 DOM 结构，在文档去除 css 的情况下，任然具有结 构和可读性。</font>**

## html 标记的使用规范

- p：文本段落； 
- dl：定义列表，可包括标题和内容简介的列表； 
- ul：无序列表； 
- ol：有序列表； 
- h1~h6：文章标题、内容区块标题，根据重要性由大到小区分，h1 一个页面只出现一次； 
- strong/em：强调文本； 
- img：图像，必须加上 alt 属性，当图像无法显示时，可表示图像信息，背景和按钮使用 css 处理，不使用 img 元素；
- label： 表示字段文本； 
- table：数据网格，规则的分栏布局，尽可能显性的定宽和定高。

---

- header：表示网页的头部

- main：表示网页的主体部分（一个页面中只会有一个 main）

- footer：表示网页的底部

- nav：表示网页中的导航

- aside：和主体相关的其他内容（侧边栏）

- article：表示一个独立的文章

- section：表示一个独立的区块，上边的标签都不能表示时使用 section

- iframe：内联框架，用于向当前页面中引入一个其他页面

    ```
    <!--
    src 指定要引入的网页的路径
    frameborder 指定内联框架的边框 值为0或1
    -->
    <iframe src="https://www.qq.com" width="800" height="600" frameborder="0"></iframe>
    ```

## 音视频

audio 标签用来向页面中引入一个外部的音频文件，音视频文件引入时，**默认情况下不允许用户自己控制播放停止**

属性：

- controls 是否允许用户控制播放
- autoplay 音频文件是否自动播放
- loop 音乐是否循环播放

如果设置了 autoplay 则音乐在打开页面时会自动播放，但是目前来讲大部分浏览器默认都不会自动对音乐进行播放

```
<!-- <audio src="./source/audio.mp3" controls autoplay loop></audio> -->  
<!-- <audio src="./source/audio.mp3" controls></audio> -->
<!-- 除了通过src来指定外部文件的路径以外，还可以通过source来指定文件的路径 -->
<audio controls>
    <!-- 对不起，您的浏览器不支持播放音频！请升级浏览器！ -->
    <source src="./source/audio.mp3">
    <source src="./source/audio.ogg">
    <embed src="./source/audio.mp3" type="audio/mp3" width="300" height="100">
</audio>
```

示例：

<audio controls>
    <!-- 对不起，您的浏览器不支持播放音频！请升级浏览器！ -->
    <source src="./source/audio.mp3">
    <source src="./source/audio.ogg">
    <embed src="./source/audio.mp3" type="audio/mp3" width="300" height="100">
</audio>

```
<!-- 
    使用 video 标签来向网页中引入一个视频
    使用方式和 audio 基本上是一样的
-->
<video controls>
    <source src="./source/flower.webm">
    <source src="./source/flower.mp4">
    <embed src="./source/flower.mp4" type="video/mp4">
</video>
```

示例：
<video controls>
    <source src="./source/flower.webm">
    <source src="./source/flower.mp4">
    <embed src="./source/flower.mp4" type="video/mp4">
</video>

## 表格和表单

### 表格

```
<table> 			用来创建一个表格
    <tr> 			表示表格中的一行
    	<th> </th>	表示表头中的单元格
    </tr>
    <tr> 			表示表格中的一行
    	<td> </td>	表示表格中的单元格
    </tr>
</table>
```

#### 属性

colspan 横向的合并单元格

rowspan 纵向的合并单元格

另外，可以将一个表格分成三个部分：

- 头部 thead
- 主体 tbody
- 底部 tfoot

```
<table> 			
    <thead> 			
    	<th> </th>	
    </thead>
    <tbody> 			
    	<td> </td>	
    </tbody>
    <tfoot> 
    	<td> </td> 
    </tfoot>
</table>
```

#### 常用 css 样式

```css
/* border-spacing: 指定边框之间的距离 */
/* border-spacing: 0px; */
/* border-collapse: collapse; 设置边框的合并 */
border-collapse: collapse;
}

td{
    border: 1px solid black;
    height: 100px;
    /* 默认情况下元素在td中是垂直居中的 可以通过 vertical-align 来修改*/
    vertical-align:middle;
    text-align: center; 
}

/* 
如果表格中没有使用tbody而是直接使用tr，
那么浏览器会自动创建一个tbody，并且将tr全都放到tbody中
tr不是table的子元素
*/
tbody > tr:nth-child(odd){
    background-color: #bfa;
}

.box1{
    width: 300px;
    height: 300px;
    background-color: orange;

    /* 将元素设置为单元格 td  */
    display: table-cell;
    vertical-align: middle;

}
```

### 表单

```
<form action="target.html">
<!-- 
    autocomplete="off" 关闭自动补全
    readonly 将表单项设置为只读，数据会提交
    disabled 将表单项设置为禁用，数据不会提交
    autofocus 设置表单项自动获取焦点
-->
    <input type="text" name="username" value="hello" readonly>
    <br>
    <input type="text" name="username" autofocus>
    <br>
    <input type="text" name="b">
    <br>
    <!-- <input type="color"> -->
    <br>
    <!-- <input type="email"> -->
    <br>
    <input type="submit">
    <!-- 重置按钮 -->
    <input type="reset">
    <!-- 普通的按钮 -->
    <input type="button" value="按钮">
    <br>

    <button type="submit">提交</button>
    <button type="reset">重置</button>
    <button type="button">按钮</button>
</form>
```

示例：

<form action="target.html">
<!-- 
    autocomplete="off" 关闭自动补全
    readonly 将表单项设置为只读，数据会提交
    disabled 将表单项设置为禁用，数据不会提交
    autofocus 设置表单项自动获取焦点
-->
    <input type="text" name="username" value="hello" readonly>
    <br>
    <input type="text" name="username" autofocus>
    <br>
    <input type="text" name="b">
    <br>
    <!-- <input type="color"> -->
    <br>
    <!-- <input type="email"> -->
    <br>
    <input type="submit">
    <!-- 重置按钮 -->
    <input type="reset">
    <!-- 普通的按钮 -->
    <input type="button" value="按钮">
    <br>
    <button type="submit">提交</button>
    <button type="reset">重置</button>
    <button type="button">按钮</button>
</form>

# CSS

## 选择器

### 复合选择器
交集选择器
	作用：选中同时符合多个条件的元素
	语法：选择器1选择器2选择器3选择器n{}
	注意点：交集选择器中如果有元素选择器，必须使用元素选择器开头

```
        div.red{
        	font-size: 30px;
        }
```

选择器分组（并集选择器）
	作用：同时选择多个选择器对应的元素
	语法：选择器1,选择器2,选择器3,选择器n{}

	        #b1,.p1,h1,span,div.red{}
	        h1, span{
	        	color: green
	        }

### 关系选择器

子代元素选择器
    作用：选中指定父元素的指定子元素
    语法：父元素 > 子元素

```
        div.box > span{
        	color: orange;
        } 
```

后代元素选择器：
    作用：选中指定元素内的指定后代元素
    语法：祖先 后代

            div span{
                color: skyblue
            } 

选择下一个兄弟
	语法：前一个 + 下一个

            p + span{
                color: red;
            }

选择下边所有的兄弟
    语法：兄 ~ 弟

            p ~ span{
                color: red;
            }

### 属性选择器

```
    /* 
        [属性名] 选择含有指定属性的元素
        [属性名=属性值] 选择含有指定属性和属性值的元素
        [属性名^=属性值] 选择属性值以指定值开头的元素
        [属性名$=属性值] 选择属性值以指定值结尾的元素
        [属性名*=属性值] 选择属性值中含有某值的元素的元素
    */
    /* p[title]{ */
    /* p[title=abc]{ */
    /* p[title^=abc]{ */
    /* p[title$=abc]{ */

    p[title*=e]{
        color: orange;
    }
```

### 伪类选择器

伪类：不存在的类 特殊的类

- 伪类用来描述一个元素的特殊状态

  比如：第一个子元素、被点击的元素、鼠标移入的元素...

- 伪类一般情况下都是使用 `:` 开头

  :first-child 第一个子元素

  :last-child 最后一个子元素

  :nth-child() 选中第 n 个子元素

  ​	特殊值：
  ​		n 第 n 个 n 的范围 0 到正无穷

  ​		2n 或 even 表示选中偶数位的元素

  ​		2n+1 或 odd 表示选中奇数位的元素

  以上这些伪类都是根据所有的子元素进行排序，下面这几个伪类的功能和上述的类似，不同的是他们是在同类型元素中进行排序

  :first-of-type

  :last-of-type

  :nth-of-type()

  :not() 否定伪类， 将符合条件的元素从选择器中去除

  ```css
  /* ul > li:first-child{
  	color: red;
  } */
  
  /* ul > li:last-child{
  	color: red;
  } */
  
  /* ul > li:nth-child(2n+1){
  	color: red;
  } */
  
  /* ul > li:nth-child(even){
  	color: red;
  } */
  
  /* ul > li:first-of-type{
  	color: red;
  } */
  
  ul > li:not(:nth-of-type(3)){
  	color: yellowgreen;
  }
  ```

  :focus input:focus 选择具有焦点的输入元素

a 元素的伪类：

```css
/* 
	:link 用来表示没访问过的链接（正常的链接）
*/
a:link{
	color: red;
}

/* 
	:visited 用来表示访问过的链接
	由于隐私的原因，所以visited这个伪类只能修改链接的颜色
*/
a:visited{
	color: orange; 
	/* font-size: 50px;   */
}

/* 
	:hover 用来表示鼠标移入的状态
*/
a:hover{
	color: aqua;
	font-size: 50px;
}

/*
	:active 用来表示鼠标点击
*/
a:active{
	color: yellowgreen;
}
```

在样式的优先级相同时，后面设置的样式会覆盖之前设置的

点击（按住不放）一个被点击过的 a 元素，这个 a 元素实际上有 :visited，:hover，:active 三个状态共存，三个状态的样式都有生效的机会，现在就是拼优先级（不好意思，你们仨优先级相等）和位置（后来居上）的时候了，这就是状态共存。 

至于触发性就是给 :hover 设置了样式，如果不以鼠标事件触发，它只能在小黑屋等着，连和 :link，:visited 竞争的机会都没有。 

### 伪元素选择器

伪元素：表示页面中一些特殊的并不真实的存在的元素（特殊的位置）
伪元素使用 `::` 开头

    ::first-letter 表示第一个字母
    ::first-line 表示第一行
    ::selection 表示选中的内容
    ::before 元素的开始 
    ::after 元素的最后
        before 和 after 必须结合content属性来使用

```css
p::first-letter{
	font-size: 50px;
}

p::first-line{
	background-color: yellow; 
}

p::selection{
	background-color: greenyellow;
}

div::before{
	content: 'abc';
	color: red;
}

div::after{
	content: 'haha';
	color: blue;
} 
```

## 继承和权重

### 样式的继承

我们为一个元素设置的样式同时也会应用到它的后代元素上

- 继承是发生在祖先后后代之间的
- 继承的设计是为了方便我们的开发，

利用继承我们可以将一些通用的样式统一设置到共同的祖先元素上，这样只需设置一次即可让所有的元素都具有该样式

注意：并不是所有的样式都会被继承，比如背景相关的，布局相关等的这些样式都不会被继承（背景默认是透明）。

### 样式的冲突

当我们通过不同的选择器，选中相同的元素，并且为相同的样式设置不同的值时，此时就发生了样式的冲突。

发生样式冲突时，应用哪个样式由选择器的权重（优先级）决定

选择器的权重

​    内联样式        1,0,0,0

​    id选择器        0,1,0,0

​    类和伪类选择器   0,0,1,0

​    元素选择器       0,0,0,1

​    通配选择器       0,0,0,0

​    继承的样式       没有优先级

比较优先级时，需要将所有的选择器的优先级进行相加计算，最后优先级越高，则越优先显示（分组选择器是单独计算的）,选择器的累加不会超过其最大的数量级，类选择器在高也不会超过id选择器

如果优先级计算后相同，此时则优先使用靠下的样式

可以在某一个样式的后边添加 `!important` ，则此时该样式会获取到最高的优先级，甚至超过内联样式 <font color=red>注意：在开发中这个玩意一定要慎用！</font>

```
.d1{
	background-color: purple !important;
}
```

## 长度单位

<table>
    <tr>
    	<td>像素</td>
        <td>百分比</td>
        <td>em</td>
        <td>rem</td>
    </tr>
    <tr>
    	<td>
            屏幕（显示器）实际上是由一个一个的小点点构成的<br/><br/>
			不同屏幕的像素大小是不同的，像素越小的屏幕显示的效果越清晰<br/><br/>
			所以同样的200px在不同的设备下显示效果不一样
        </td>
        <td>
        	也可以将属性值设置为2020相对于其父元素属性的百分比<br/><br/>
			设置百分比可以使子元素跟随父元素的改变而改变
        </td>
        <td>
        	em是相对于元素的字体大小来计算的<br/><br/>
			1em = 1font-size<br/><br/>
			em会根据字体大小的改变而改变
        </td>
        <td>
            rem是相对于根元素的字体大小来计算
        </td>
    </tr>
</table>

## 盒模型

![image-20221016205023283](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20221016205023283.png)

### 内容区（content）

元素中的所有的子元素和文本内容都在内容区中排列，内容区的大小由width 和 height两个属性来设置

- width 设置内容区的宽度

- height 设置内容区的高度

    ```
    width: 200px;
    height: 200px;
    background-color: #bfa;
    ```

### 边框（border）

边框属于盒子边缘，边框里边属于盒子内部，出了边框都是盒子的外部；

边框的大小会影响到整个盒子的大小，要设置边框，需要至少设置三个样式：

- 边框的宽度 border-width

    ```
    border-width: 10px; 
    默认值，一般都是 3个像素
    border-width 可以用来指定四个方向的边框的宽度
    值的情况：
        四个值：上 右 下 左
        三个值：上 左右 下
        两个值：上下 左右
        一个值：上下左右
    
    除了border-width还有一组 border-xxx-width
    xxx可以是 top right bottom left
    用来单独指定某一个边的宽度：
        /* border-width: 10px; */
        /* border-top-width: 10px;
        border-left-width: 30px; */
    ```

- 边框的颜色 border-color

    ```
    border-color也可以省略不写，如果省略了则自动使用color的颜色值 (黑色)
    ```

- 边框的样式 border-style

border简写属性，通过该属性可以同时设置边框所有的相关样式，并且没有顺序要求

除了border以外还有四个 border-xxx：border-top、border-right、border-bottom、border-left

```
border: solid 10px orange;
border-top: 10px solid red;
border-left: 10px solid red;
border-bottom: 10px solid red;

border: 10px red solid;
border-right: none;
```

### 内边距（padding）

内容区和边框之间的距离是内边距
- 一共有四个方向的内边距：

  padding-top

  padding-right

  padding-bottom

  padding-left

- 内边距的设置会影响到盒子的大小

- 背景颜色会延伸到内边距上

  可以在内容区再加一个盒子，指定width: 100%;height: 100%; background-color: yellow;

一个盒子的可见框的大小，由内容区、内边距和边框共同决定，所以在计算盒子大小时，需要将这三个区域加到一起计算

```
padding: 10px 20px 30px ;
padding: 10px 20px ;
padding: 10px ;
```

### 行内元素的盒模型

行内元素不支持设置宽度和高度

行内元素可以设置 padding，但是垂直方向 padding 不会影响页面的布局

行内元素可以设置 border，垂直方向的 border 不会影响页面的布局

行内元素可以设置 margin，垂直方向的 margin 不会影响布局

注意：行内元素不会影响布局，但会直接覆盖到其他元素之上

- display 用来设置元素显示的类型

    可选值：

    - inline 将元素设置为行内元素
    - block 将元素设置为块元素
    - inline-block 将元素设置为行内块元素 （行内块：既可以设置宽度和高度又不会独占一行）
    - table 将元素设置为一个表格
    - none 元素不在页面中显示（相当于物理删除）

visibility 用来设置元素的显示状态

- 可选值：
    - visible 默认值，元素在页面中正常显示
    - hidden 元素在页面中隐藏 不显示，但是依然占据页面的位置

### 盒模型特点

一个元素在其父元素中，水平布局必须要满足以下的等式

margin-left+border-left+padding-left+width+padding-right+border-right+margin-right = 其父元素内容区的宽度 （必须满足）

0 + 0 + 0 + 200 + 0 + 0 + 0 = 800

0 + 0 + 0 + 200 + 0 + 0 + 600 = 800

如果相加结果使等式不成立，则称为过度约束，则等式会自动调整

- 如果这七个值中没有为 auto 的情况，则浏览器会自动调整 margin-right 值以使等式满足
- 如果某个值为 auto，则会自动调整为 auto 的那个值以使等式成立
- 如果将两个外边距设置为 auto，宽度固定值，则会将外边距设置为相同的值

所以我们经常利用这个特点来使一个元素在其父元素中水平居中，示例：
```
width:xxxpx;
margin:0 auto;
```

默认情况下，盒子可见框的大小由内容区、内边距和边框共同决定

box-sizing 用来设置盒子尺寸的计算方式（设置 width 和 height 的作用）

可选值：

- content-box：默认值，宽度和高度用来设置内容区的大小
- border-box：宽度和高度用来设置整个盒子可见框的大小（不包括外边距）
- width 和 height 指的是内容区 和 内边距 和 边框的总大小

```
box-sizing: border-box;
```

## 垂直布局

子元素是在父元素的内容区中排列的，

如果子元素的大小超过了父元素，则子元素会从父元素中溢出

使用 overflow 属性来设置父元素如何处理溢出的子元素

可选值：

- visible：默认值 子元素会从父元素中溢出，在父元素外部的位置显示
- hidden：溢出内容将会被裁剪不会显示
- scroll：生成两个滚动条，通过滚动条来查看完整的内容
- auto：根据需要生成滚动条

```
overflow-x: 
overflow-y:
overflow: auto;
```

## 外边距的折叠

垂直外边距的重叠（折叠）
- 相邻的垂直方向外边距会发生重叠现象

兄弟元素

- 兄弟元素间的相邻垂直外边距会取两者之间的较大值（两者都是正值）

- 特殊情况：

    ​	如果相邻的外边距一正一负，则取两者的和

    ​	如果相邻的外边距都是负值，则取两者中绝对值较大的

- 兄弟元素之间的外边距的重叠，对于开发是有利的，所以我们不需要进行处理

父子元素

- 父子元素间相邻外边距，子元素的会传递给父元素（上外边距）

- 父子外边距的折叠会影响到页面的布局，必须要进行处理

    **<font color=red>注意：行内元素，例如span 不会发生折叠 </font>**

    **<font color=red>clearfix 这个样式可以同时解决高度塌陷和外边距重叠的问题，当你在遇到这些问题时，直接使用 clearfix 这个类即可</font>**

```
.clearfix{*zoom:1;}
.clearfix::before,.clearfix::after{
    content: ''; 
    display: table;
    clear: both;
}
```

## 圆角和阴影

box-shadow 用来设置元素的阴影效果，阴影不会影响页面布局 

```
box-shadow: 0px 0px 50px rgba(0, 0, 0, .3) ; 
```

- 第一个值 水平偏移量 设置阴影的水平位置 正值向右移动 负值向左移动
- 第二个值 垂直偏移量 设置阴影的水平位置 正值向下移动 负值向上移动
- 第三个值 阴影的模糊半径
- 第四个值 阴影的颜色

outline 用来设置元素的轮廓线，用法和border一模一样，轮廓和边框不同的点，就是轮廓不会影响到可见框的大小（只是轮廓 不改动布局）

```
.box1:hover{
	outline: 10px red solid;
}
```

border-radius: 用来设置圆角 圆角设置的圆的半径大小

```
border-radius: 20px / 40px;
    可以分别指定四个角的圆角
    四个值 左上 右上 右下 左下
    三个值 左上 右上/左下 右下 
    两个个值 左上/右下 右上/左下  
                    
border-top-left-radius:  
border-top-right-radius:
border-bottom-left-radius:  
border-bottom-right-radius:  
border-top-left-radius:50px 100px;

将元素设置为一个圆形
border-radius: 50%;
```

## 完美居中

```css
.box3{
    background-color: orange;
    position: absolute;
    /* 
        这种居中方式，只适用于元素的大小确定
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        margin: auto; 
    */

    left: 50%;
    top: 50%;
    transform: translateX(-50%) translateY(-50%);
}
```

## 浮动

使用 float 属性来设置于元素的浮动，通过浮动可以使一个元素向其父元素的左侧或右侧移动

可选值：

​	none 默认值 ，元素不浮动

​	left 元素向左浮动

​	right 元素向右浮动

> 注意：
>
> 元素设置浮动以后，水平布局的等式便不需要强制成立
>
> 元素设置浮动以后，会完全从文档流中脱离，不再占用文档流的位置，所以元素下边的还在文档流中的元素会自动向上移动

浮动的特点：

1. 浮动元素会完全脱离文档流，不再占据文档流中的位置
2. 设置浮动以后元素会向父元素的左侧或右侧移动，
3. 浮动元素默认不会从父元素中移出
4. 浮动元素向左或向右移动时，不会超过它前边的其他浮动元素
5. 如果浮动元素的上边是一个没有浮动的块元素，则浮动元素无法上移
6. 浮动元素不会超过它上边的浮动的兄弟元素，最多最多就是和它一样高

简单总结：浮动目前来讲它的主要作用就是让页面中的元素可以水平排列，通过浮动可以制作一些水平方向的布局   

浮动元素不会盖住文字，文字会自动环绕在浮动元素的周围，所以我们可以利用浮动来设置文字环绕图片的效果

### 脱离文档流的特点

元素设置浮动以后，将会从文档流中脱离，从文档流中脱离后，元素的一些特点也会发生变化

块元素：

​	块元素不在独占页面的一行

​	脱离文档流以后，块元素的宽度和高度默认都被内容撑开

行内元素：

​	行内元素脱离文档流以后会变成块元素，特点和块元素一样，所以脱离文档流以后，不需要再区分块和行内了

## 父级元素高度塌陷问题

高度塌陷的问题：

在浮动布局中，父元素的高度默认是被子元素撑开的，当子元素浮动后，其会完全脱离文档流，子元素从文档流中脱离

这时，子元素将会无法撑起父元素的高度，导致父元素的高度丢失，父元素高度丢失以后，其下的元素会自动上移，导致页面的布局混乱

所以高度塌陷是浮动布局中比较常见的一个问题，这个问题我们必须要进行处理！

### clear 属性

- 作用：清除浮动元素对当前元素所产生的影响

- 可选值：

  left 清除左侧浮动元素对当前元素的影响

  right 清除右侧浮动元素对当前元素的影响

  both 清除两侧中最大影响的那侧

原理：设置清除浮动以后，浏览器会自动为元素添加一个上外边距，以使其位置不受其他元素的影响

clearfix 这个样式可以同时解决高度塌陷和外边距重叠的问题，当你在遇到这些问题时，直接使用 clearfix 这个类即可
```
.clearfix::before,
.clearfix::after{
    content: ''; 
    display: table;
    clear: both;
}
```

## BFC(Block Formatting Context) 块级格式化环境

BFC 是一个 CSS 中的一个隐含的属性，开启 BFC 该元素会变成一个独立的布局区域

元素开启 BFC 后的特点：

1. 开启 BFC 的元素不会被浮动元素所覆盖
2. 开启 BFC 的元素子元素和父元素外边距不会重叠
3. 开启 BFC 的元素可以包含浮动的子元素

可以通过一些特殊方式来开启元素的 BFC：

1. 设置元素的浮动（不推荐）
2. 将元素设置为行内块元素（不推荐）
3. 将元素的 overflow 设置为一个非 visible 的值

常用的方式 为元素设置 overflow:hidden 开启其 BFC 以使其可以包含浮动元素

```
/* float: left; */
/* display: inline-block; */
overflow: hidden;
```

## 定位

定位（position）
- 定位是一种更加高级的布局手段
- 通过定位可以将元素摆放到页面的任意位置
- 使用position属性来设置定位
可选值：
static 默认值，元素是静止的没有开启定位
relative 开启元素的相对定位
absolute 开启元素的绝对定位
fixed 开启元素的固定定位
sticky 开启元素的粘滞定位

### 相对定位：
当元素的 position 属性值设置为 relative 时则开启了元素的相对定位

相对定位的特点：元素开启相对定位以后，如果不设置偏移量元素不会发生任何的变化了

偏移量（offset）:当元素开启了定位以后，可以通过偏移量来设置元素的位置

​	top：定位元素和定位位置上边的距离

​	bottom：定位元素和定位位置下边的距离

​	left：定位元素和定位位置的左侧距离

​	right：定位元素和定位位置的右侧距离

定位元素垂直方向的位置由top和bottom两个属性来控制，通常情况下我们只会使用其中一

- top 值越大，定位元素越向下移动
- bottom 值越大，定位元素越向上移动

定位元素水平方向的位置由left和right两个属性控制,通常情况下只会使用一个

- left越大元素越靠右
- right越大元素越靠左

偏移量也可以指定一个负值，如果是负值则元素会向相反的方向移动

### 绝对定位

当元素的 position 属性值设置为 absolute 时，则开启了元素的绝对定位

绝对定位的特点：

1. 开启绝对定位后，如果不设置偏移量元素的位置不会发生变化
2. 开启绝对定位后，元素会从文档流中脱离
3. 绝对定位会改变元素的性质，行内变成块，块的宽高被内容撑开
4. 绝对定位会使元素提升一个层级
5. 绝对定位元素是相对于其包含块进行定位的

包含块( containing block )
- 正常情况下：

- 包含块就是离当前元素最近的祖先块元素

  ```
  <div> <div></div> </div>
  <div><span><em>hello</em></span></div>
  ```
- 绝对定位的包含块:
- 包含块就是离它最近的开启了定位的祖先元素，
- 如果所有的祖先元素都没有开启定位则根元素就是它的包含块

### 固定定位：

将元素的 position 属性设置为 fixed 则开启了元素的固定定位

固定定位也是一种绝对定位，所以固定定位的大部分特点都和绝对定位一样

唯一不同的是固定定位永远参照于浏览器的视口进行定位，固定定位的元素不会随网页的滚动条滚动。

### 粘滞定位

当元素的 position 属性设置为 sticky 时则开启了元素的粘滞定位

粘滞定位和相对定位的特点基本一致，不同的是粘滞定位可以在元素到达某个位置时将其固定

> 注意：不推荐使用 不兼容IE

```
position: sticky;
top: 10px;
```

### 水平布局

left + margin-left + border-left + padding-left + width + padding-right + border-right + margin-right + right = 包含块的内容区的宽度

当我们开启了绝对定位后，水平方向的布局等式就需要添加 left 和 right 两个值，此时规则和之前一样只是多添加了两个值

当发生过度约束：

​	如果 9 个值中没有 auto 则自动调整 right 值以使等式满足

​	如果有 auto，则自动调整 auto 的值以使等式满足

可设置 auto 的值：margin width left right

因为 left 和 right 的值默认是 auto，所以如果不指定 left 和 right 则等式不满足时，会自动调整这两个值

垂直方向布局的等式的也必须要满足

top + margin-top/bottom + padding-top/bottom + border-top/bottom + height = 包含块的高度

### 层级

默认层级：文档流 < 浮动 < 定位	

当元素开启定位以后，可以通过 z-index 来设置层级，z-index 需要一个整数作为参数，值越大元素的层级越高，元素的层级越高越优先显示

如果层级一样，则后边的会盖住前边的，祖先的元素的层级再高也不会盖住后代元素
```
/* z-index: 3; */
```

### 元素的透明度

使用 opacity 来设置元素的透明度

- 需要一个 0-1 之间的值
- 0 表示完全透明
- 1 表示完全不透明

IE8及以下的浏览器不支持该样式，需要使用如下方式来设置
```
filter:alpha(opacity=透明度);
```

- 需要一个0-100之间的值
- 0 表示完全透明
- 100 表示完全不透明

## 文本的样式

### text-align 文本的水平对齐

可选值：

​    left 左侧对齐

​    right 右对齐

​    center 居中对齐

​    justify 两端对齐

```
/* text-align: justify; */
```

### vertical-align 设置元素垂直对齐的方式

可选值：

​    baseline 默认值 基线对齐

​    top 顶部对齐

​	bottom 底部对齐

​	middle 居中对齐

```
vertical-align:baseline; 
img{
	vertical-align: bottom;
} （如果不设置 img元素在p元素里面 底部会有间隙）
```

### text-decoration 设置文本修饰

可选值：

​    none 什么都没有

​    underline 下划线

​    line-through 删除线

​    overline 上划线

```
/* text-decoration: overline; */
/* text-decoration: underline red dotted; */
```

### white-space 设置网页如何处理空白

可选值：

​	normal 正常

​	nowrap 不换行

​	pre 保留空白

```
.box2{
	width: 200px;
	
    //必须一起写才有效
    white-space: nowrap;
    overflow: hidden;  
    text-overflow: ellipsis; （省略号）
}
```

## 字体

### font-face 可以将服务器中的字体直接提供给用户去使用 

问题：

1. 加载速度
2. 版权
3. 字体格式

```
@font-face {
/* 指定字体的名字 */
font-family:'myfont' ;
/* 服务器中字体的路径 */
src: url('./font/ZCOOLKuaiLe-Regular.ttf') format("truetype");
}
```

### 字体相关的样式 

​	color 用来设置字体颜色

​	font-size 字体的大小

和font-size相关的单位

​	em 相当于当前元素的一个font-size

​	rem 相对于根元素的一个font-size

### font-family 字体族（字体的格式）

可选值：

​	serif  衬线字体

​	sans-serif 非衬线字体

​	monospace 等宽字体

指定字体的类别，浏览器会自动使用该类别下的字体

font-family 可以同时指定多个字体，多个字体间使用 `“,”` 隔开，字体生效时优先使用第一个，第一个无法使用则使用第二个 以此类推

```
p{
color: blue;
font-size: 40px;

/* font-family: 'Courier New', Courier, monospace; */
font-family: myfont;
}
```

### font-weight 字重 字体的加粗 

可选值：

​	normal 默认值 不加粗

​	bold 加粗

​	100-900 九个级别（没什么用）

```
/* font-weight: bold; */
font-weight: 500;
```

### font-style 字体的风格

可选值：

​	normal 正常的

​	italic 斜体

```
font-style: italic;
```

### 图标字体（iconfont）

在网页中经常需要使用一些图标，可以通过图片来引入图标，但是图片大小本身比较大，并且非常的不灵活，所以在使用图标时，我们还可以将图标直接设置为字体，然后通过 font-face 的形式来对字体进行引入，这样我们就可以通过使用字体的形式来使用图标。

fontawesome 使用步骤
1. 下载 https://fontawesome.com/
2. 解压
3. 将css和webfonts移动到项目中
4. 将all.css引入到网页中
5. 使用图标字体

直接通过类名来使用图标字体

​	class="fas fa-bell"

​	class="fab fa-accessible-icon"

```
<i class="fas fa-bell" style="font-size:80px; color:red;"></i>
```

通过伪元素来设置图标字体

1. 找到要设置图标的元素通过before或after选中

2. 在content中设置字体的编码

3. 设置字体的样式

    ```
    li::before{
    content: '\f1b0';
    /* font-family: 'Font Awesome 5 Brands'; */
    font-family: 'Font Awesome 5 Free';
    font-weight: 900; 
    color: blue;
    margin-right: 10px;
    }
    ```

通过实体来使用图标字体：&#x图标的编码;

```
<span class="fas">&#xf0f3;</span>
```

### 行高（line height）

行高指的是文字占有的实际高度

​	可以通过 line-height 来设置行高，行高可以直接指定一个大小（px em），也可以直接为行高设置一个整数，如果是一个整数的话，行高将会是字体的指定的倍数

行高经常还用来设置文字的行间距

行间距 = 行高 - 字体大小

### 字体框

字体框就是字体存在的格子，设置 font-size 实际上就是在设置字体框的高度，行高会在字体框的上下平均分配

### font

font 可以设置字体相关的所有属性

语法：

font: 字体大小/行高 字体族 

行高可以省略不写 如果不写使用默认值（1.333）

## 背景

### 设置背景的范围 background-clip 

可选值：

​	border-box 默认值，背景会出现在边框的下边

​	padding-box 背景不会出现在边框，只出现在内容区和内边距

​	content-box 背景只会出现在内容区

```
background-clip: content-box;
```

### 背景图片的偏移量计算的原点 background-origin

​	padding-box 默认值，background-position从内边距处开始计算

​	content-box 背景图片的偏移量从内容区处计算

​	border-box 背景图片的变量从边框处开始计算

```
background-origin: border-box;
```

### background-image 设置背景图片 

- 可以同时设置背景图片和背景颜色，这样背景颜色将会成为图片的背景色
- 如果背景的图片小于元素，则背景图片会自动在元素中平铺将元素铺满
- 如果背景的图片大于元素，将会一个部分背景无法完全显示
- 如果背景图片和元素一样大，则会直接正常显示

```
background-image: url("./img/1.png");
```

### background-repeat 用来设置背景的重复方式

可选值：

- repeat 默认值，背景会沿着 x 轴 y 轴双方向重复
- repeat-x 沿着x轴方向重复
- repeat-y 沿着y轴方向重复
- no-repeat 背景图片不重复

```
background-repeat: no-repeat;
```

### background-position 用来设置背景图片的位置

设置方式：

通过 top left right bottom center 几个表示方位的词来设置背景图片的位置

使用方位词时必须要同时指定两个值，如果只写一个则第二个默认就是 center

通过偏移量来指定背景图片的位置：水平方向的偏移量 垂直方向变量

```
/* background-position: center center; */
background-position: -50px 300px;
```

### background-size 设置背景图片的大小

第一个值表示宽度，第二个值表示高度

如果只写一个，则第二个值默认是 auto（建议！）

cover 图片的比例不变，将元素铺满

contain 图片比例不变，将图片在元素中完整显示

```
background-size:auto  100px;
background-size: 100px;
background-size: contain contain ;
```

> 注意：
>
> 慎用 background-size: contain contain
>
> background-size:100% auto  会更好

### background-attachment 背景图片是否跟随元素移动

可选值：

​	scroll 默认值 背景图片会跟随元素移动

​	fixed 背景会固定在页面中，不会随元素移动

```
background-attachment: fixed;
```

### backgound 背景相关的简写属性

所有背景相关的样式都可以通过该样式来设置，并且该样式没有顺序要求，也没有哪个属性是必须写的

```
background-color
background-image
background-repeat
background-position
background-size
background-origin
background-clip
background-attachment
```

> 注意：
>
> background-size 必须写在 background-position 的后边，并且使用 `/` 隔开：`background-position/background-size`
>
> background-origin background-clip 两个样式 ，orgin 要在 clip 的前边

### 颜色渐变

通过渐变可以设置一些复杂的背景颜色，可以实现从一个颜色向其他颜色过渡的效果；如果渐变是图片，需要通过 background-image 来设置
#### 线性渐变

颜色沿着一条直线发生变化：linear-gradient()

linear-gradient(red,yellow) 红色在开头，黄色在结尾，中间是过渡区域

线性渐变的开头，我们可以指定一个渐变的方向

| to left | to right | to bottom | to top | deg（deg表示度数） | turn（表示圈） |
| ------- | -------- | --------- | ------ | ------------------ | -------------- |

渐变可以同时指定多个颜色，多个颜色默认情况下平均分布，也可以手动指定渐变的分布情况

repeating-linear-gradient() 可以平铺的线性渐变

```
/* background-image: linear-gradient(red,yellow,#bfa,orange); */
/* background-image: linear-gradient(red 50px,yellow 100px, green 120px, orange 200px); */
background-image: repeating-linear-gradient(to right ,red, yellow 50px);
```

#### 径向渐变

放射性的效果：radial-gradient() 

默认情况下径向渐变的形状根据元素的形状来计算的

​	正方形 --> 圆形

​	长方形 --> 椭圆形

我们也可以手动指定径向渐变的大小和指定渐变的位置

语法：

​	radial-gradient(大小 at 位置, 颜色 位置 ,颜色 位置 ,颜色 位置)

大小：

​	circle 圆形

​	ellipse 椭圆

​	closest-side 近边	

​	closest-corner 近角

​	farthest-side 远边

​	farthest-corner 远角

位置：top right left center bottom

```
background-image: radial-gradient(farthest-corner at 100px 100px, red , #bfa)
```

## 过渡

过渡（transition）
- 通过过渡可以指定一个属性发生变化时的切换方式
- 通过过渡可以创建一些非常好的效果，提升用户的体验

### transition-property:

指定要执行过渡的属性，多个属性间使用 "," 隔开，如果所有属性都需要过渡，则使用 all 关键字

大部分属性都支持过渡效果，注意过渡时必须是从一个有效数值向另外一个有效数值进行过渡

```
/* transition-property: height , width; */
/* transition-property: all; */
```

### transition-duration:

指定过渡效果的持续时间，时间单位：s 和 ms  1s = 1000ms

```
/* transition-duration: 100ms, 2s; */给属性分别指定时间
/* transition-duration: 2s; */
```

### transition-timing-function

过渡的时序函数，指定过渡的执行的方式  

可选值：

​	ease 默认值，慢速开始，先加速，再减速

​	linear 匀速运动

​	ease-in 加速运动

​	ease-out 减速运动

​	ease-in-out 先加速 后减速

​	cubic-bezier() 来指定时序函数

​	steps() 分步执行过渡效果

​	可以设置一个第二个值：

​		end ， 在时间结束时执行过渡(默认值)

​		start ， 在时间开始时执行过渡

```
/* transition-timing-function: cubic-bezier(.24,.95,.82,-0.88); */
/* transition-timing-function: steps(2, start); */
```

### transition-delay

过渡效果的延迟，等待一段时间后在执行过渡

```
/* transition-delay: 2s; */
```

### transition

可以同时设置过渡相关的所有属性，只有一个要求，如果要写延迟，则两个时间中第一个是持续时间，第二个是延迟

```
transition:2s margin-left 1s cubic-bezier(.24,.95,.82,-0.88);
```

## 动画

动画
动画和过渡类似，都是可以实现一些动态的效果，不同的是过渡需要在某个属性发生变化时才会触发，而动画可以自动触发动态效果
设置动画效果，必须先要设置一个关键帧，关键帧设置了动画执行每一个步骤

```css
@keyframes test {
    /* from表示动画的开始位置 也可以使用 0% */
    from{
        margin-left: 0;
        background-color: orange;
    } 

    /* to动画的结束位置 也可以使用100%*/
    to{
        background-color: red;
        margin-left: 700px;
    }
}
```

```
animation: name .5s forwards linear infinite alternate;
```

animation-name：要对当前元素生效的关键帧的名字

animation-duration：动画的执行时间

animation-delay：动画的延时

animation-timing-function：定义 CSS 动画在每一动画周期中执行的节奏

animation-iteration-count 动画执行的次数 

​	可选值：

​		次数（整数）

​		infinite 无限执行

animation-direction：指定动画运行的方向

​	可选值：

​		normal 默认值  从 from 向 to运行 每次都是这样 

​		reverse 从 to 向 from 运行 每次都是这样 

​		alternate 从 from 向 to运行 重复执行动画时反向执行

​		alternate-reverse 从 to 向 from运行 重复执行动画时反向执行

animation-play-state: 设置动画的执行状态 

​	可选值：

​		running 默认值 动画执行

​		paused 动画暂停

animation-fill-mode: 动画的填充模式

​	可选值：

​		none 默认值 动画执行完毕元素回到原来位置

​		forwards 动画执行完毕元素会停止在动画结束的位置

​		backwards 动画延时等待时，元素就会处于开始位置

​		both 结合了forwards 和 backwards

## 旋转、变形、缩放

> 用 transform 去位移的方式比 position 性能好很多 它不会每次都单独新建图层

变形就是指通过CSS来改变元素的形状或位置

变形不会影响到页面的布局

transform 用来设置元素的变形效果

### 平移

translateX() 沿着x轴方向平移

translateY() 沿着y轴方向平移

translateZ() 沿着z轴方向平移

平移元素，百分比是相对于自身计算的

```
transform: translateX(100%);
```

Z 轴平移，调整元素在z轴的位置，正常情况就是调整元素和人眼之间的距离，距离越大，元素离人越近
Z 轴平移属于立体效果（近大远小），默认情况下网页是不支持透视，如果需要看见效果必须要设置网页的视距

```
perspective: 800px;
```

### 旋转

通过旋转可以使元素沿着x y 或 z旋转指定的角度

rotateX()

rotateY()

rotateZ()

```
transform: rotateY(180deg);
```

是否显示元素的背面

```
backface-visibility: hidden;
```

### 缩放

对元素进行缩放的函数：

scaleX() 水平方向缩放

scaleY() 垂直方向缩放

scale() 双方向的缩放

```
transform:scale(1.2);
```

### 设置变形的原点

```
transform-origin:20px 20px;
```

## GPU 加速 CSS 渲染

参考：https://www.jianshu.com/p/6fdcd651ea06

使用 CSS Transform 模块的属性，开启 GPU 加速，提升动画流畅度

为动画 DOM 元素添加 CSS3 样式 -webkit-transform:transition3d(0,0,0) 或 -webkit-transform:translateZ(0);，这两个属性都会开启 GPU 硬件加速模式，从而让浏览器在渲染动画时从 CPU 转向 GPU，其实说白了这是一个小伎俩，也可以算是一个 Hack

-webkit-transform:transition3d 和 -webkit-transform:translateZ 其实是为了渲染 3D 样式，但我们设置值为 0 后，并没有真正使用 3D效果，但浏览器却因此开启了GPU硬件加速模式。

这种 GPU 硬件加速在当今 PC 机及移动设备上都已普及，在移动端的性能提升是相当显著地。

当然也可以这样开启所有浏览器的GPU硬件加速：

```
webkit-transform:translateZ(0);
-moz-transform:translateZ(0);
-ms-transform:translateZ(0);
-o-transform:translateZ(0);
transform:translateZ(0);
```

### 开启 GPU 硬件加速可能触发的问题：

通过 `-webkit-transform:transition3d/translateZ` 开启 GPU 硬件加速之后，有些时候可能会导致浏览器频繁闪烁或抖动，可以尝试以下办法解决：

```
-webkit-backface-visibility:hidden;
-webkit-perspective:1000;
```

## 弹性盒（Flex）

flex(弹性盒、伸缩盒)
- 是CSS中的又一种布局手段，它主要用来代替浮动来完成页面的布局
- flex可以使元素具有弹性，让元素可以跟随页面的大小的改变而改变

弹性容器

- 要使用弹性盒，必须先将一个元素设置为弹性容器

- 我们通过 display 来设置弹性容器

    ```
    display:flex  设置为块级弹性容器
    display:inline-flex 设置为行内的弹性容器
    ```

弹性元素

- 弹性容器的子元素是弹性元素（弹性项）
- 弹性元素可以同时是弹性容器

> 注意：当我们为父盒子设为 flex 布局以后 子元素的 float，clear 和 vertical-align 属性将失效

### flex-direction 

指定容器中弹性元素的排列方式

可选值：

​	row 默认值，弹性元素在容器中水平排列（左向右），主轴自左向右

​	row-reverse 弹性元素在容器中反向水平排列（右向左），主轴 自右向左

​	column 弹性元素纵向排列（自上向下）

​	column-reverse 弹性元素方向纵向排列（自下向上）

主轴：弹性元素的排列方向称为主轴

侧轴：与主轴垂直方向的称为侧轴

```
flex-direction: row;
```

### 弹性元素的属性

flex-grow 指定弹性元素的伸展的系数

- 当父元素有多余空间的时，子元素如何伸展
- 父元素的剩余空间，会按照比例进行分配

flex-shrink 指定弹性元素的收缩系数

- 当父元素中的空间不足以容纳所有的子元素时，如何对子元素进行收缩

```
flex-shrink: 1;
```

### 弹性容器的样式

flex-wrap: 设置弹性元素是否在弹性容器中自动换行

可选值：

​	nowrap 默认值，元素不会自动换行

​	wrap 元素沿着辅轴方向自动换行

​	wrap-reverse 元素沿着辅轴反方向换行

```
flex-wrap: wrap-reverse;
```

flex-flow 属性是 flex-direction 和 flex-wrap 属性的复合属性。

```
flex-flow: row wrap;
```

justify-content：如何分配主轴上的空白空间（主轴上的元素如何排列）

可选值：

​    flex-start 元素沿着主轴起边排列

​    flex-end 元素沿着主轴终边排列

​    center 元素居中排列

​    space-around 空白分布到元素两侧

​    space-between 空白均匀分布到元素间

​    space-evenly 空白分布到元素的单侧

```
justify-content: center;
```

align-items: 元素在辅轴上如何对齐元素间的关系

可选值：

​    stretch 默认值，将元素的长度设置为相同的值（行与行之间）

​    flex-start 元素不会拉伸，沿着辅轴起边对齐

​    flex-end 沿着辅轴的终边对齐

​    center 居中对齐

​    baseline 基线对齐

```
/* justify-content: center; */
align-items: flex-start;
```

align-self: 用来覆盖当前弹性元素上的 align-items

### 弹性元素的样式 

弹性的增长系数

```
flex-grow: 1;
```

弹性元素的缩减系数
- 缩减系数的计算方式比较复杂
- 缩减多少是根据 缩减系数 和 元素大小来计算

```
flex-shrink: 1;
```

元素基础长度

flex-basis 指定的是元素在主轴上的基础长度

如果主轴是横向的,则该值指定的就是元素的宽度

如果主轴是纵向的,则该值指定的是就是元素的高度

- 默认值是 auto，表示参考元素自身的高度或宽度
- 如果传递了一个具体的数值，则以该值为准

```
 flex-basis: auto;
```

flex 可以设置弹性元素所有的三个样式

```
flex: 增长 缩减 基础;
initial "flex: 0 1 auto".
auto  "flex: 1 1 auto"
none "flex: 0 0 auto" 弹性元素没有弹性
```

order 决定弹性元素的排列顺序

```
order: 2;
```

## 颜色单位

在 CSS 中可以直接使用颜色名来设置各种颜色，比如：red、orange、yellow、blue、green ... ...

但是在 CSS  中直接使用颜色名是非常的不方便

RGB值：
- RGB通过三种颜色的不同浓度来调配出不同的颜色
- R red，G green ，B blue
- 每一种颜色的范围在 0 - 255 (0% - 100%) 之间
- 语法：RGB(红色,绿色,蓝色)     

RGBA:
- 就是在rgb的基础上增加了一个a表示不透明度
- 需要四个值，前三个和rgb一样，第四个表示不透明度（1表示完全不透明   0表示完全透明  .5半透明）  

十六进制的RGB值：
- 语法：#红色绿色蓝色
- 颜色浓度通过 00-ff
- 如果颜色两位两位重复可以进行简写（#aabbcc --> #abc）        

HSL值 HSLA值

- H 色相(0 - 360)
- S 饱和度，颜色的浓度 0% - 100%
- L 亮度，颜色的亮度 0% - 100%                      

## 文档流（normal flow）

网页是一个多层的结构，一层摞着一层，通过CSS可以分别为每一层来设置样式，作为用户来讲只能看到最顶上一层

这些层中，最底下的一层称为文档流，文档流是网页的基础，我们所创建的元素默认都是在文档流中进行排列

对于我们来元素主要有两个状态

- 在文档流中
- 不在文档流中（脱离文档流）

元素在文档流中有什么特点：

块元素

- 块元素会在页面中独占一行(自上向下垂直排列)
- 默认宽度是父元素的全部（会把父元素撑满）
- 默认高度是被内容撑开（子元素）

行内元素

- 行内元素不会独占页面的一行，只占自身的大小
- 行内元素在页面中左向右水平排列，如果一行之中不能容纳下所有的行内元素，则元素会换到第二行继续自左向右排列（书写习惯一致）
- 行内元素的默认宽度和高度都是被内容撑开

## CSS Hack

```
只在IE下生效
<!--[if IE]>
这段文字只在IE浏览器显示
<![endif]-->

只在IE6下生效
<!--[if IE 6]>
这段文字只在IE6浏览器显示
<![endif]-->

只在IE6以上版本生效
<!--[if gte IE 6]>
这段文字只在IE6以上(包括)版本IE浏览器显示  (gt大于   lt小于)
<![endif]-->

只在IE8上不生效
<!--[if ! IE 8]>
这段文字在非IE8浏览器显示
<![endif]-->

非IE浏览器生效
<!--[if !IE]>
这段文字只在非IE浏览器显示
<![endif]-->

提示：可以配合 link 引入外部 css 使用
注意：IE10 及以上版本已将条件注释特性移除 使用时需谨慎

```

## 禁止系统默认滚动条

HTML 的父级是文档，body 的父级是 HTML

如果想要 HTML、body、视口三合一，必须设置 HTML 高度 100%，body 高度 100%，如果只给 HTML 加上 `overflow: scroll` 则滚动条在文档上

如果只给 body 加上 `overflow: scroll` 则滚动条也在文档上，这就是 overflow 的隐藏属性

必须同时给 HTML 和 body 同时加上 `overflow: scroll` 才会在 body 上，也就是说 HTML 的滚动条永远存在于文档上

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			html{
				margin: 30px;
				border: 1px solid;
				height: 100%;
				overflow: scroll;
			}
			body{
				margin: 30px;
				border: 1px solid pink;
				height: 100%;
				overflow: scroll;
			}

            /*
                禁止系统默认滚动条：
                html,body{
                height: 100%;
                overflow: hidden;
                }
            */

		</style>
	</head>
	<body>
		<div style="height: 1000px;">
			
		</div>
	</body>
</html>
```

使用绝对定位来模拟固定定位：

```
	<!DOCTYPE html>
	<html>
		<head>
			<meta charset="UTF-8">
			<title></title>
			<style type="text/css">
				*{
					margin: 0;
					padding: 0;
				}
				html{
					overflow: hidden;
					height: 100%;
				}
				
				body{
					overflow: auto;   //这个时候滚动条是在body上
					height: 100%;
				}
				
				#test{
					position: absolute;
					left: 50px;
					top: 50px;
					width: 100px;
					height: 100px;
					background: pink;
				}
			</style>
		</head>
		<body>
			<div id="test">
				
			</div>
			<div style="height: 1000px;">
				csjkahcjk <br />
				csjkahcjk <br />
				csjkahcjk <br />
				csjkahcjk <br />
				csjkahcjk <br />
				csjkahcjk <br />
				csjkahcjk <br />
				csjkahcjk <br />
			</div>
		</body>
	</html>
```





# less

## 简介

less 是一门 css 的预处理语言

less 是一个 css 的增强版，通过 less 可以编写更少的代码实现更强大的样式

在 les s中添加了许多的新特性：像对变量的支持、对 mixin 的支持... ...

less 的语法大体上和 css 语法一致，但是 less 中增添了许多对 css 的扩展，所以浏览器无法直接执行 less 代码，要执行必须向将 less 转换为 css，然后再由浏览器执行

## 使用

注释

```
// less中的单行注释，注释中的内容不会被解析到css中

/*
    css中的注释，内容会被解析到css文件中
*/
```

:extend() 对当前选择器扩展指定选择器的样式（选择器分组）

```
.p2:extend(.p1){
    color: red;
}
```

直接对指定的样式进行引用，这里就相当于将p1的样式在这里进行了复制

```
.p3{
    //mixin 混合
    .p1();
}
```

使用类选择器时可以在选择器后边添加一个括号，这时我们实际上就创建了一个 mixins

```
.p4(){
    width: 100px;
    height: 100px;
}
.p5{
    .p4;
}
```

混合函数 在混合函数中可以直接设置变量

```
.test(@w:100px,@h:200px,@bg-color:red){
    width: @w;
    height: @h;
    border: 1px solid @bg-color;
}
```

调用混合函数，按顺序传递参数

```
div{
    // .test(200px,300px,#bfa);
    .test(300px);
    // .test(@bg-color:red, @h:100px, @w:300px);
}
```

变量，在变量中可以存储一个任意的值，并且我们可以在需要时，任意的修改变量中的值
变量的语法： @变量名

> 变量发生重名时，会优先使用比较近的变量

```
@a:200px;
@b:#bfa;
@c:box6;
```

使用变量是，如果是直接使用则以 `@变量名` 的形式使用即可

```
.box5{
    width: @a;
    color:@b;
}
```

作为类名，或者一部分值使用时必须以 @{变量名} 的形式使用

```
.@{c}{
    width: @a;
    background-image: url("@{c}/1.png");
}
```

可以在变量声明前就使用变量

```
div{
    height: @e;
}
@e:335px;
```

新版的语法

```
div{
    width: 300px;
    // height: $width;
}
```

& 就表示外层的父元素
```
&:hover{
	color: orange;
}
```

import 用来将其他的 less 引入到当前的 less

```
.box1{
    // 在 less 中所有的数值都可以直接进行运算
    width: 100px + 100px;
    height: 100px/2;
    background-color: #bfa;
}
```

# PC 端

在 pc 端的网页通常情况下都是一个固定宽度的==版心==，用来显示网页的主要内容，是水平居中的。

版心的宽度, 960px、980px、1000px、1190px、1200px 以上，版心之外被称为叫==通栏效果==

# 移动端

## 像素

屏幕是由一个一个发光的小点构成，这一个个的小点就是像素，分辨率（1920 x 1080）说的就是屏幕中小点的数量。

在前端开发中像素要分成两种情况讨论：CSS像素 和 物理像素

- 物理像素，上述所说的小点点就属于物理像素
- CSS像素，编写网页时，我们所用像素都是CSS像素

浏览器在显示网页时，需要将CSS像素转换为物理像素然后再呈现

一个css像素最终由几个物理像素显示，由浏览器决定：默认情况下在pc端，一个css像素 = 一个物理像素

## 视口（viewport）

视口就是屏幕中用来显示网页的区域，可以通过查看视口的大小，来观察CSS像素和物理像素的比值

默认情况下：

视口宽度 1920px（CSS像素） 1920px（物理像素），此时，css像素和物理像素的比是 1:1

放大两倍的情况：视口宽度 960px（CSS像素） 1920px（物理像素），此时，css像素和物理像素的比是1:2

们可以通过改变视口的大小，来改变 CSS 像素和物理像素的比值

## 移动端的像素问题

https://material.io/resources/devices/

在不通的屏幕，单位像素的大小是不同的，像素越小屏幕会越清晰

24寸 1920x1080

i6 4.7寸 750 x 1334

> 智能手机的像素点远远小于计算机的像素点

### 一个宽度为 900px 的网页在 iphone6 中要如何显示呢？

默认情况下，移动端的网页都会将视口设置为 980 像素（css像素），以确保 pc 端网页可以在移动端正常访问，但是如果网页的宽度超过了 980，移动端的浏览器会自动对网页缩放以完整显示网页。

所以基本大部分的pc端网站都可以在移动端中正常浏览，但是往往都不会有一个好的体验，为了解决这个问题，大部分网站都会专门为移动端设计网页

### 像素问题解决办法 

移动端默认的视口大小是980px(css像素)，

默认情况下，移动端的像素比就是  980/移动端宽度  （980/750）

如果我们直接在网页中编写移动端代码，这样在 980 的视口下，像素比是非常不好，导致网页中的内容非常非常的小

编写移动页面时，必须要确保有一个比较合理的像素比：

​     1css像素 对应 2个物理像素

​     1css像素 对应 3个物理像素

可以通过 meta 标签来设置视口大小，每一款移动设备设计时，都会有一个最佳的像素比，一般我们只需要将像素比设置为该值即可得到一个最佳效果,将像素比设置为最佳像素比的视口大小我们称其为完美视口

```
将网页的视口设置为完美视口
<meta name="viewport" content="width=device-width, initial-scale=1.0">
结论：以后再写移动端的页面，就把上边这个玩意先写上
```

### 处理移动端与 PC 端的不同

CSS3 盒子模型

```
box-sizing:border-box;
-webkit-box-sizing:border-box;
```

点击高亮我们需要清除清除设置为transparent完成透明

```
-webkit-tap-highlight-color:transparent;
```

在移动端浏览器默认的外观在 i0S 上加上邀个属性才能给按钮和输入框自定义样式
```
-webkit-appearance:none;
```

禁用长按页面时的弹出菜单
```
img,a {-webkit-touch-callout:none;}
```

## 响应式布局

网页可以根据不通的设备或窗口大小呈现出不同的效果，使用响应式布局，可以使一个网页适用于所有设备

响应布局的关键就是 媒体查询，通过媒体查询，可以为不通的设备，或设备不同状态来分别设置样式

### 使用媒体查询 

语法： @media 查询规则{}

媒体类型：可以使用 `","` 连接多个媒体类型，可以在媒体类型前添加一个only，表示只有。only的使用主要是为了兼容一些老版本浏览器

- all 所有设备
- print 打印设备
- screen 带屏幕的设备
- speech 屏幕阅读器

```
@media print,screen{
    body{
    	background-color: #bfa;
    }
}

@media only screen {
    body{
    	background-color: #bfa;
    }
}
```

### 媒体特性

width 视口的宽度
height 视口的高度
min-width 视口的最小宽度（视口大于指定宽度时生效）
max-width 视口的最大宽度（视口小于指定宽度时生效）

```
@media (max-width: 500px){
    body{
    	background-color: #bfa;
    }
}
```

 样式切换的分界点，我们称其为断点，也就是网页的样式会在这个点时发生变化，一般比较常用的断点

```
小于768 超小屏幕 max-width=768px
大于768 小屏幕   min-width=768px
大于992 中型屏幕 min-width=992px
大于1200 大屏幕  min-width=1200px

@media only screen and (min-width: 500px) and (max-width:700px){
    body{
    	background-color: #bfa;
    }
}
```

# 技巧

## HTML

### 在 html 页面引入固定 html 文件

1. 使用 `<iframe></iframe>` 标签引入，原生的 iframe 会生成一个边框，需要自己调一下样式。

    ```
    <iframe width="800" height="500" src="http://www.baidu.com"></iframe>
    ```

2. 使用 jquery

    > 语法 `element+$("#page1").load(“b.html”)`

    ```
    <body>
        <div id="page1"></div>
        <div id="page2"></div>
        <script>
              $("#page1").load("page/Page_1.html");
              $("#page2").load("page/Page_2.html");
        </script>
    </body>
    ```

3. object 引入

    ```
     <object style="border:0px" type="text/x-scriptlet" data="page/Page_1.html" width=100% height=150></object>
    ```

    <font color=red>注意：经测试，此方法写的 html 在没有部署到服务器上时，在 Firefox 上显示不出来！在 Edge 和 Chrome 上有效。</font>

4. 将下方 js 文件代码保存成 `include.js` 文件引入，然后通过 `<include>` 标签引入

    ```
    <include src="test.html"></include>
    ```

    `include.js` 文件

    ```js
      (function(window, document, undefined) {
        var MInclude = function() {}
        MInclude.prototype = {
          //倒序循环
          forEach: function(array, callback) {
            var size = array.length;
            for(var i = size - 1; i >= 0; i--){
              callback.apply(array[i], [i]);
            }
          },
          getFilePath: function() {
            var curWwwPath=window.document.location.href;
            var pathName=window.document.location.pathname;
            var localhostPaht=curWwwPath.substring(0,curWwwPath.indexOf(pathName));
            var projectName=pathName.substring(0,pathName.substr(1).lastIndexOf('/')+1);
            return localhostPaht+projectName;
          },
          //获取文件内容
          getFileContent: function(url) {
            var ie = navigator.userAgent.indexOf('MSIE') > 0;
            var o = ie ? new ActiveXObject('Microsoft.XMLHTTP') : new XMLHttpRequest();
            o.open('get', url, false);
            o.send(null);
            return o.responseText;
          },
          parseNode: function(content) {
            var objE = document.createElement("div");
            objE.innerHTML = content;
            return objE.childNodes;
          },
          executeScript: function(content) {
            var mac = /<script>([\s\S]*?)<\/script>/g;
            var r = "";
            while(r = mac.exec(content)) {
              eval(r[1]);
            }
          },
          getHtml: function(content) {
            var mac = /<script>([\s\S]*?)<\/script>/g;
            content.replace(mac, "");
            return content;
          },
          getPrevCount: function(src) {
            var mac = /\.\.\//g;
            var count = 0;
            while(mac.exec(src)) {
              count++;
            }
            return count;
          },
          getRequestUrl: function(filePath, src) {
            if(/http:\/\//g.test(src)){ return src; }
            var prevCount = this.getPrevCount(src);
            while(prevCount--) {
              filePath = filePath.substring(0,filePath.substr(1).lastIndexOf('/')+1);
            }
            return filePath + "/"+src.replace(/\.\.\//g, "");
          },
          replaceIncludeElements: function() {
            var $this = this;
            var filePath = $this.getFilePath();
            var includeTals = document.getElementsByTagName("include");
            this.forEach(includeTals, function() {
              //拿到路径  
              var src = this.getAttribute("src");
              //拿到文件内容  
              var content = $this.getFileContent($this.getRequestUrl(filePath, src));
              //将文本转换成节点  
              var parent = this.parentNode;
              var includeNodes = $this.parseNode($this.getHtml(content));
              var size = includeNodes.length;
              for(var i = 0; i < size; i++) {
                parent.insertBefore(includeNodes[0], this);
              }
              //执行文本中的额javascript  
              $this.executeScript(content);
              parent.removeChild(this);
              //替换元素 this.parentNode.replaceChild(includeNodes[1], this);  
            })
          }
        }
        window.onload = function() {
          new MInclude().replaceIncludeElements();
        }
      })(window, document)
    ```

## CSS

### 使元素浮动起来（脱离文档流）

使用 position 属性 absolute（绝对定位）、relative（相对定位）

使用 float 属性，完成块级元素的布局时使用，后来布局一般使用 display:flex ，但 float 兼容性好

### flex 布局

使用 `justify-content` 属性设置对其样式 space-between 表示两端对齐，空白留在中间

### 为 `<a></a>` 标签设置宽度

a 标签是行内元素，但是行内元素不可以设置宽高，所以使用 `display: block;` 将 a 标签编程块级元素后即可设置。

### 为文字设置样式

文字样式一般以 `text-` 开头：

text-indent: 设置 `<p></p>` 标签的段落首行缩进 

### transform 属性设置元素的旋转、平移和倾斜属性

translateZ(0.2px)：平移

rotateY(180deg)：旋转

scale(2, 0.5)：变形

### 实现卡片翻转效果

- 定义两个 div，将其叠加到一起
- 通过 `backface-visibility: hidden;` 属性设置两个 div 反面不可见
- 先显示的 div 设置旋转 0deg，翻转过来才显示的 div 设置旋转 180deg
- 点击旋转按钮，让已经显示的 div 设置旋转到 180deg，让即将显示的 div 设置旋转到 0deg

```html
<div class="map clearfix">
    <div class="addr clearfix">
        正面显示
    </div>

    <div id="container">
        反面显示
    </div>
</div>

<style>
.addr {
    width: 100%;
    height: 600px;
    border-radius: 8px;
    background-color: #fff;
    box-shadow: 0 0 5px 10px rgba(204, 26, 26, 0.06);
    
    transition: 1s;
    backface-visibility: hidden;
}
    
#container {
    width: 100%;
    height: 600px;
    box-shadow: 0 0 5px 10px rgba(204, 26, 26, 0.06);
    border-radius: 8px;

    transition: 1s;
    transform: rotateY(180deg);
    backface-visibility: hidden;
}
</style>
<script>
  function showMap() {
    let addr = document.querySelector(".addr");
    addr.style.transform = "rotateY(180deg)";

    document.querySelector("#container").style.transform = "rotateY(0deg)";
    document.querySelector(".x-btn").style.visibility = 'initial';
  }

  function showTable() {
    let addr = document.querySelector(".addr");
    addr.style.transform = "rotateY(0deg)";
    document.querySelector("#container").style.transform = "rotateY(180deg)";
    document.querySelector(".x-btn").style.visibility = 'hidden';
  }
</script>
```

### 实现文字两边居中的横线

参考：[Bumphy CSS实战样式：文字两侧加居中横线](https://blog.csdn.net/Bumphy/article/details/78319678?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-78319678-blog-117774448.pc_relevant_3mothn_strategy_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-78319678-blog-117774448.pc_relevant_3mothn_strategy_recovery&utm_relevant_index=2)

```html
<div class="dateLine">
  <div class="lineOfDate"></div>
  <a class="dateString">2011年12月12日</a>
  <div class="lineOfDate"></div>
</div>
```

```javascript
.dateLine {
  height: 10px;
  line-height: 20px;
  font-size: 4px;
  text-align: center;
}

.lineOfDate {
  display: inline-block;
  width: 30%;
  border-bottom: 1px solid white;
}

.dateString {
  font-size: 2px;
  color: white;
  vertical-align: middle;
  vertical-align: -20%;
  margin: 0 20px;
}
```

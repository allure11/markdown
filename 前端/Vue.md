[toc]

# Vue.js概述

## Vue是什么？

Vue(读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://cn.vuejs.org/v2/guide/single-file-components.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#libraries--plugins)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

**官网：**[https://cn.vuejs.org/](https://cn.vuejs.org/)

**渐进式：**

> 意味着你可以将Vue作为你引用的一部分嵌入其中，带来丰富的交互体验
>
> 如果你希望能将更多的业务使用Vue实现，那么可以使用Vue的核心库及其生态

**Vue的特点和web开发中的常见高级功能：**

>  解耦视图和数据
>
> 可复用的组件
>
> 前端路由技术
>
> 状态管理
>
> 虚拟DOM

# Vue.js的安装

## 方式一：CDN引入

对于制作原型或学习（开发环境版本）：

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
```

对于生产环境，我们推荐链接到一个明确的版本号和构建文件，以避免新版本造成的不可预期的破坏：

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14"></script>
```

## 方式二：下载后引入

官网下载

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210721085433476.png" alt="image-20210721085433476"  />

# 初体验

### 第一个vue页面

注意这种编程范式

```html
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <!-- 引入vuejs文件 -->
    <script src="js/vue.js"></script>
</head>
<body>
    <div id="app">{{messsge}}{{name}}</div>
    <script>
        // let（变量）和const（常量）取代原来javascript中的var定义变量
        // Vue的编程范式：声明式编程
        // 好处：坐待代码与数据完全分离
        const app = new Vue({
            el:'#app',//用于挂载要管理的元素
            data:{ //定义数据
                messsge:'你好啊,傻逼',
                name:'yyb'
            }
        }) 
    </script>
</body>
```

### 遍历显示数组

```html
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="js/vue.js"></script>
</head>
<body>
    <div id="app">
        <ul>
            <!--循环打印movies中的数据放在li中-->
            <li v-for="item in movies">{{item}}</li>
        </ul>
    </div>
    <script>
        //创建Vue对象，管理“#app”元素
        const app = new Vue({
            el:'#app',
            data:{
                message:'你好',
                movies:['海王','狗哥','大话西游','PUBG']
            }
        })
    </script>
</body>
```

![image-20210721110525550](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210721110525550.png)

### 计数器

```html
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="js/vue.js"></script>
</head>
<body>
    <div id="app">
        当前计数：{{counter}}<br>
        <!-- 
            <button v-on:click="counter++">+</button>
            <button v-on:click="counter--">-</button>
        -->
        <button v-on:click="add">+</button>
        <!--@click：语法糖，就是简写-->
        <button @click="sub">-</button>
    </div>
    <script>
        const app =new Vue({
            el:'#app',
            data:{
                counter:0
            },
            methods:{
                add:function(){
                    console.log("点击+");
                    this.counter++;
                },
                sub:function(){
                    console.log("点击-");
                    this.counter--;
                }
            }
        })
    </script>
</body>
```

# Vue中的MVVM

概念：**MVVM（Model–view–viewmodel**）是一种软件[架构模式](https://zh.wikipedia.org/wiki/架构模式)。

MVVM有助于将[图形用户界面](https://zh.wikipedia.org/wiki/图形用户界面)的开发与[业务逻辑](https://zh.wikipedia.org/wiki/业务逻辑)或[后端](https://zh.wikipedia.org/wiki/前端和后端)逻辑（*数据模型*）的开发[分离](https://zh.wikipedia.org/wiki/关注点分离)开来，这是通过[置标语言](https://zh.wikipedia.org/wiki/置标语言)或GUI代码实现的。MVVM的*视图模型*是一个值转换器，[[1\]](https://zh.wikipedia.org/wiki/MVVM#cite_note-MVVM-eliminates-valueconverters-1) 这意味着视图模型负责从模型中暴露（转换）[数据对象](https://zh.wikipedia.org/wiki/对象_(计算机科学))，以便轻松管理和呈现对象。在这方面，视图模型比视图做得更多，并且处理大部分视图的显示逻辑。[[1\]](https://zh.wikipedia.org/wiki/MVVM#cite_note-MVVM-eliminates-valueconverters-1) 视图模型可以实现[中介者模式](https://zh.wikipedia.org/wiki/中介者模式)，组织对视图所支持的[用例](https://zh.wikipedia.org/wiki/用例)集的后端逻辑的访问。

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210721113720693.png" alt="image-20210721113720693" style="zoom: 80%;" />

**View层：**

> 视图层，通常就是DOM层，主要就是给用户展示各种信息

**Model层：**

> 数据层
>
> 数据就是从服务器获取的数据
>
> 在计数器案例里，就是后来抽取出来的obj

**VueModel层：**

> 视图模型层
>
> 是View和Model沟通的桥梁
>
> 一方面，它实现了Data Binding，也就是数据绑定，将Model的改变事实的反应到View中
>
> 另一方面，它实现了DOM Listener，也就是DOM监听，当DOM发生一些事情（点击，滚动，touch等）时，‘可以监听到，并在需要的情况下改变对应的Data

### 以计数器为例：

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210721115212335.png" alt="image-20210721115212335" style="zoom: 80%;" />

# 创建Vue时传入的options

![image-20210721115724575](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210721115724575.png)

**这个options可以包含哪些选项：**详情请看[https://cn.vuejs.org/v2/api/](https://cn.vuejs.org/v2/api/)

# Vue的生命周期

![image-20210721181405826](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210721181405826.png)

## 生命周期函数

|   函数名称    |                             说明                             |
| :-----------: | :----------------------------------------------------------: |
| beforeCreate  | **vue实例创建初始化后，数据观测  (data observer) 和event/watch事件配置之前触发** |
|    created    | **在实例创建完成后被立即调用**，此时实例已完成数据观测  (data observer)，属性方法的运算，watch/event 事件回调的配置。然而，挂载阶段还没开始，$el 属性目前不可见 |
|  beforeMount  | **实例挂载开始之前被调用**， render  函数首次被调用，该钩子在**服务器端渲染期间不被调用** |
|    mounted    | **实例已挂载**。mounted  不会承诺所有的子组件也都一起被挂载，如果你希望等到整个视图都渲染完毕再进行一些操作，请用 vm.$nextTick：mounted: function  () {this.$nextTick(function () { // Code that will run only after the entire  view has been rendered })}该钩子在服务器端渲染期间不被调用 |
| beforeUpdate  | **数据更新时调用，**发生在虚拟  DOM 打补丁之前，这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器，该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务端进行 |
|    updated    | **数据更改会导致虚拟  DOM 重新渲染和打补丁，在这之后会调用updated钩子。**updated 不会承诺所有的子组件也都一起被重绘。如果你希望等到整个视图都重绘完毕，请用  vm.$nextTick ：updated: function () {this.$nextTick(function () { // Code that  will run only after the entire view has been re-rendere})} updated钩子被调用时，组件  DOM 已经更新，你现在可以执行依赖于 DOM  的操作。然而在大多数情况下，你应该避免在此期间更改实例中的状态属性，如果要相应状态改变，通常最好使用计算属性或 watcher |
| beforeDestroy | **实例销毁之前调用**。在这一步，实例仍然完全可用，该钩子在服务器端渲染期间不被调用 |
|   destroyed   | **Vue  实例销毁后调用**。调用后，Vue 实例指示的所有东西都会接触绑定，所有的事件监听器会被移除，所有的子实例也会被销毁，该钩子在服务器端渲染期间不被调用 |
|   activated   | **当某个组件使用了keep-alive组件缓存时，该组件激活时调用activated钩子，**该钩子在服务器端渲染期间不被调用 |
|  deactivated  | **当某个组件使用了keep-alive组件缓存时，该组件停用时调用deactivated钩子，**该钩子在服务器端渲染期间不被调用 |
| errorCaptured | **当捕获一个来自子孙组件的错误时被调用**。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回  false 以阻止该错误继续向上传播 |

# Vue的模板文件

## 代码规范

前端一般流行缩进两个空格，例如：Vue源码

![image-20210721181759397](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210721181759397.png)

## VSCode编写模板文件

```html
"vue template": {
		"prefix": "vue",
		"body": [
			"<!DOCTYPE html>"
			"<html lang=\"en\">"
			"<head>"
			"	<meta charset=\"UTF-8\">"
			"	<title>Document</title>"
			"	<script src=\"js/vue.js\"></script>"
			"</head>"
			"<body>"
			"	<div id='app'>{{message}}</div>"
			"	<script>"
			"		const app = new Vue({"
			"			el: '#app',"
			"			data: {"
			"				message: 'hello'"
			"			}"	
			"		})"
			"	</script>"
			"</body>"
			"</html>"
		 ],
		"description": "Vue template"
	}
```

# 插值操作

## Mustache语法[^{{message}}]

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>    <!--hello-->
    <h2>{{message}}，hello</h2>   <!--hello,hello-->
    <h2>{{FirstName+LastName}}</h2>   <!--余彦波-->
    <h2>{{FirstName}}{{LastName}}</h2>    <!--余彦波-->
    <h2>{{age * 2}}</h2>    <!--36-->
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello',
        FirstName: '余',
        LastName: '彦波',
        age: 18
      }
    })
  </script>
</body>
```

## 基本指令的使用

| **指令** |                           **作用**                           |
| :------: | :----------------------------------------------------------: |
|  v-once  |              禁止其显示的值根据数据的变化而变化              |
|  v-for   |       循环遍历数组`movies`中的数据，放入新的`\<li\>`中       |
|  v-html  |                将数据转换成html格式，进行显示                |
|  v-text  | 显示文本（一般不使用，直接使用mustache比较方便），会将标签本来要显示的数据覆盖掉 |
|  v-pre   | 将数据标签中的数据原封不动的展示，例如{{message}}不显示hello，就显示大括号这样的格式 |
| v-cloak  |            vue对象中没有此数据就不显示，有才显示             |

```html
<div id='app' v-once>{{message}}</div>

<li v-for="item in movies">{{item}}</li>

<h2 v-html="url">{{url}}</h2>

<hz v-text="message"></hz>

<h2 v-pre>{{message}}</h2>

<h2 v-cloak>{{message}}</h2>
```

##  v-bind

除了元素的内容需要动态指定外，某些元素的属性也需要动态的绑定，例如：

- `<a href="xxxxx"></a>`标签的 href 属性
- `<img src="xxxx"/>`标签的 src 属性

作用：动态绑定属性

### 基本使用方法：

```html
<body>
  <div id='app'>
    <img v-bind:src="imgUrl" alt="">
    <!--语法糖-->
    <a :href="baiduUrl">百度一下</a>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        imgUrl: 'https://cn.vuejs.org/images/components.png',
        baiduUrl: 'http://www.baidu.com'
      }
    })
  </script>
</body>
```

### 语法糖

省略v-bind，直接写冒号`<a :href="baiduUrl">百度一下</a>`

### 动态绑定class属性

一个class属性可以使用对象的方式传递多个键值对，例如

```html
<h2 :class="{‘active’ : true,‘line’: true}">{{message}}</h2>
<!--
当键值对的值为true时，显示其对应的键的属性                  class="active line"
当键值对的值为false时，不显示其对应的键的属性
-->
```

**点击变色案例：**

```html
  <style>
    .active{
      color: red;
    }
    .line{
      color: royalblue;
    }
  </style>

<body>
  <div id='app'>
    <!--isLind为true，就显示蓝色，否则显示红色-->
    <h2 :class="{‘active’ : isActive,‘line’: isLine}">{{message}}</h2>
    <button @click="btnClick">点击一下，改变颜色</button>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello',
        isActive: true,
        isLine: true
      },
      methods:{
          //点击按钮，执行的函数
        btnClick:function(){
          //改变isLind
          this.isLine = !this.isLine;
        }
      }
    })
  </script>
</body>
```

**在标签中，规定的class可以写一个新的属性，固定显示：**

```html
<!--title是需要固定显示的样式，后面的是多需要动态绑定的样式--> 
<h2 class="title" :class="{‘active’ : isActive,‘line’: isLine}">{{message}}</h2>
```

**可以使用函数返回class需要的值：**

```html
<body>
  <div id='app'>
    <h2 :class="{active : isActive,line: isLine}">{{message}}</h2>
    <h2 :class="getClass()">{{message}}</h2>
    <button @click="btnClick">点击一下，改变颜色</button>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello',
        isActive: true,
        isLine: true
      },
      methods:{
        btnClick:function(){
          this.isLine = !this.isLine;
        },
        getClass:function(){
          return {active : this.isActive,line: this.isLine}
        }
      }
    })
  </script>
</body>
```

### 动态绑定style属性

使用方法与动态绑定class属性一样：

```html
<h2 :style="{属性名 : 属性值 , 属性名 : 属性值}">{{message}}</h2>
例：
<h2 :style="{fontSize:'100px'}">{{message}}</h2>

<!--示例-->
<body>
  <div id='app'>
    <h2 :style="{fontSize:size}">{{message}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello',
        size: '100px'
      }
    })
  </script>
</body>
```

**注意：**属性值不加单引号会解析成Vue中的数据，加单引号才会解析成普通的值

# 计算属性

## 基本使用

**案例：**将message和name拼接显示，利用普通方法和计算属性方法进行对比

```html
<body>
  <div id='app'>
    <h2>{{message}} {{name}}</h2>
    <h2>{{message+" "+name}}</h2>
    <h2>{{getA()}}</h2>
    <h2>{{welcome}}</h2>    <!----使用计算属性-->
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello',
        name: 'yyb'
      },
      // 定义计算属性
      computed: {
        welcome:function(){
          return this.message+" "+this.name
        }
      },
      methods:{
        getA:function(){
          return this.message+" "+this.name
        }
      }
    })
  </script>
</body>
```

## 计算属性的复杂操作

**案例一：使用计算属性计算数据books中的书价格的总和**

```html
<body>
  <div id='app'>
    <h2>总价格：{{TotalPrice}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        books: [
          {
            id : 110,
            name : 'html入门到精通',
            price : 100
          },
          {
            id : 111,
            name : '数据结构与算法',
            price : 88
          },
          {
            id : 112,
            name : '剑指Offer',
            price : 179
          }
        ]
      },
      computed:{
        TotalPrice:function(){
          let result = 0
          this.books.forEach(book => {
            result+=book.price
          });
          return result
        }
      }
    })
  </script>
</body>
```

## 计算属性的setter和getter

computed（计算属性）一般有两个方法，set和get

```html
      computed: {
        //计算属性一般情况下没有set方法，在实际情况下，set方法可以删除
        fullName : {
          set:function(){

          },
          get:function(){
            return this.firstName+" "+this.lastName
          }
        }
      }
```

由于计算属性一般情况下不允许修改，所以没有set方法，在实际情况下，set方法可以删除，只保留get方法

```html
      computed: {
        fullName : {
          get:function(){
            return this.firstName+" "+this.lastName
          }
        }
      }
```

可以简写成我们之前的方式

```html
computed : {
  fullName : function(){
    return this.firstName+" "+this.lastName
  }
} 
```

## 计算属性和methods的对比

拼接属性的三种方法：

```html
// 直接拼接，语法过于繁琐
<h2>{{firstName}} {{lastName}}</h2>
//计算属性，在多次调用时，只执行一次函数
<h2>{{fullName}}</h2>
// methods，在多次调用时，调用几次就执行几次函数
<h2>{{getFullName()}}</h2>
```

# 需要使用的ES6语法

## let 和 var

**var可以看做是javaScript语言设计上的错误，但这种错误不能修复和移除，因为需要向后兼容**

> 增加let是为了修复这一问题
>
> 我们可以将let看做更完美的var，var没有块级作用域，但let有

**块级作用域**

> Es5之前只有函数中有作用域，使用var定义的变量，在if或for语句块中发生更改，之后得到的值也会变
>
> Js中使用var类声明变量，变量的作用域主要是和函数的定义有关
>
> 针对于其他块定义来说是没有作用域的，比如 if/for 等，这在我们的开发中会出现一些问题

示例：

```html
<script>
	{
      var name = 'yyb';
      let a = 'pink';
      console.log(a);          // pink
      console.log(name);       // yyb
    }
    console.log(name);         // yyb
    console.log(a);            //a is not defined
</script>    
```

## const

将某个变量修饰成常量，不能再次赋值

建议：在开发中，优先使用const，只有在需要改变某一标识符的时候才使用let

注意：

> 在使用const定义常量时，必须进行初始化（赋值）
>
> const定义的对象，可以改变其内部的属性，但不能将其指向另一个对象

## 字面量的增强写法

字面量就是值

```html
  <script>
    const name = 'yyb';
    const age = 10;
    const sex = '男';
    //定义对象
    const ong = {
      name,             //name = 'yyb'
      age,              //age = 10
      sex               //sex = '男'
    }
    console.log(obj);
    //对象
    const obj2 = {
      //定义函数
      a(){
        console.log('hello');
      },
      b(){
        console.log('b');
      }
    }
  </script>
```

## 箭头函数

```javascript
    // 箭头函数，aaa是函数名
    const aaa = (/*参数列表*/) => {

    }

    // 两个参数
    const sum = (num1, num2) => {
      return num1+numi2
    }

    //一个参数,参数列表的（）可以省略
    const power = num => {
      return num*num
    }

    // 函数代码块中只有一行代码,函数的 {} 可以省略,会自动执行者一行代码，并将其结果返回
    const mul = a => a*2
    const add = (a,b) => a +b
```

箭头函数的this引用的是最近作用域中的this，就是向外层作用域中一层一层的查找，直到有this的定义，函数中的this就是他 

# 事件监听`v-on`

## 基本语法

```html
<body>
  <div id='app'>
    <h2>{{counter}}</h2>
    //标准写法
    <button v-on:click="add()">+</button>
    //语法糖写法
    <button @click="dec">-</button>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        counter: 0
      },
      methods: {
        add(){
          this.counter++
        },
        dec(){
          this.counter--
        }
      }
    })
  </script>
</body>
```

**语法糖：**@事件名称 （例如：@ click）

## 参数问题

**正常格式**

```html
<button @click="dec（）">-</button>
```

**没有参数**：调用方法时后面的小括号可以不写

```html
<button @click="dec">-</button>
```

**需要参数**

```html
//一个参数，当函数需要一个参数时，可以不传参数，形参返回undefined，但一定要加括号，否则，将Event对象赋值给参数
<button @click="dec(参数1)">-</button>
//多个参数，不加括号会将第一个参数赋值Event对象，第二个参数为undefined
//添加括号时，需要获取Event对象，使用$event手动传入Event对象，传递字符串参数时不加单引号会解析成变量
<button @click="dec(参数1,参数2)">-</button>
```

## 修饰符的使用

| 修饰符                 | 作用                                                   | 使用方法                                                     |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| .stop                  | 调用event.stopPropagation()，阻止事件冒泡              | `<button @click.stop="btnClick2">按钮1</button>`             |
| .prevent               | 调用event.preventDefault()，阻止吗，默认事件           | `<a href="www.baidu.com" @click.prevent="btnClick3">百度</a>` |
| .{KeyCode \| KeyAlias} | 可以写按键的编码或者简写，当从特定按键触发时，调用函数 | `<input type="text" @keyup.enter="keyUp">`                   |
| .once                  | 只触发一次回调函数                                     | `<button @click.once="btnClick3">按钮3</button>`             |

**示例：**

```html
<body>
  <div id="app"> 
    <div @click="btnClick1" style="background-color: red; width: 100px; height: 100px;">
      <button @click.stop="btnClick2">按钮1</button>
    </div>

      <form action="" >
        <input type="submit" value="提交" @click.prevent="btnClick1">
      </form>
      
      <a href="www.baidu.com" @click.prevent="btnClick3">百度一下</a>
  
      <input type="text" @keyup.enter="keyUp">
        
      <button @click.once="btnClick3">按钮3</button>
  </div>
  
  <script>
    const app = new Vue({
      el: '#app',
      data: {

      },
      methods: {
        btnClick1(){
          alert(1)
        },
        btnClick2(){
          alert(2)
        },
        btnClick3(){
         alert(3)
        },
        keyUp(){
          alert("key")
        }
      }
    })
  </script>
```

# 条件判断

**三个指令：v-if，v-else-if，v-else**

> 这三个指令与js中的if，elseif，else类似
>
> Vue的指令可以根据表达式的值在DOM中渲染或销毁组件

## 示例

在正常的开发中不建议这样做，只有else if较少的情况下适用

```html
<body>
  <div id='app'>
    <h2 v-if="coser>=90">优秀</h2>
    <h2 v-else-if="coser>=70">良好</h2>
    <h2 v-else-if="coser>=60">及格</h2>
    <h2 v-else="coser<60">不及格</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        coser: 91
      }
    })
  </script>
</body>
```

else if较多的情况建议使用计算属性

```html
<body>
  <div id='app'>
    <h2>{{result}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        coser: 91
      },
      computed: {
        result(){
          let show = '';
          if(this.coser>=90){
            show = '优秀'
          }else if(this.coser>=70){
            show = '良好'
          }else if(this.coser>=60){
            show = '及格'
          }else{
            show = '不及格'
          }
          return show;
        }
      }
    })
  </script>
</body>
```

## 账号登录和邮箱登录切换案例

```html
<body>
  <div id='app'>
    <div v-if="show">账号登录：<input type="text" placeholder="账号"></div>
    <div v-else>邮箱登录：<input type="text" placeholder="邮箱"></div>
    <button @click="qiehuan">切换</button>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        show:true
      },
      methods: {
        qiehuan(){
          this.show = !this.show
        }
      }
    })
  </script>
</body>
```

### 出现的问题

如果在输入框中写了内容，切换后内容依然会存在

![image-20210724134537800](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210724134537800.png)

### 原因

> 因为Vue在进行DOM渲染时，出于性能考虑，会尽可能复用已经存在的元素，而不是创建新的元素
>
> 在上面的案例中，Vue会发现原来的input元素不再使用，而直接将其作为else中的input使用

### 解决方案

如果，我们不希望Vue出现类似重复利用的问题，可以给对应的input添加 key 属性，并且需要保证 key 不相同

 ```html
   <div id='app'>
     <div v-if="show">账号登录：<input type="text" placeholder="账号" key="a"></div>
     <div v-else>邮箱登录：<input type="text" placeholder="邮箱" key="b"></div>
     <button @click="qiehuan">切换</button>
   </div>
 ```

当key相同时，表示可以复用，Vue会直接修改之前的标签，渲染到DOM，不相同时表示不可以复用，就会创建一个新的标签

# v-show

**作用：**用法和v-if相似，用于决定一个元素是否需要显示

**示例：**

```html
<body>
  <div id='app'>
    <h2 v-if="isShow">{{message}}</h2>
    <h2 v-show="isShow">{{message}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello',
        isShow: true
      }
    })
  </script>
</body>
```

 **v-show 和 v-if对比：**

 当条件为false时，使用v-if的标签会被删除，根本不会存在DOM中，而使用v-show的标签，只是添加了 display=none 的行内样式

![image-20210724140717357](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210724140717357.png)

**在开发中如何选择 v-show 和 v-if**

> 当标签需要在显示和隐藏之间频繁切换时，选择 -vshow
>
> 当只需要一次或几次切换时，选择 v-if

# v-for 遍历数组和对象

**示例：**

```html
<body>
  <div id='app'>
    <!--遍历数组-->
    <ur>
      <li  v-for="item in names">{{item}}</li>
    </ur>
    <!--在遍历的过程中获取元素的索引值-->
    <br>
    <ur>
      <li  v-for="(item,index) in names">{{item}}:{{index}}</li>
    </ur>
    <br>
    <!--遍历对象-->
    <ur>
      <li  v-for="item in obj">{{item}}</li>    <!--只获取一个值，item获取到的是对象中属性的值：yyb-->
    </ur>
    <br>
    <ur>
      <li  v-for="(key,value) in obj">{{key}}:{{value}}</li>    <!--获取两个值，得到key和value：yyb:name-->
    </ur>
    <br>
    <ur>
      <li  v-for="(key,value,index) in obj">{{key}}:{{value}}:{{index}}</li>    <!--获取三个个值，得到key和value，索引：yyb:name:0-->
    </ur>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        names:['yyb','pink','red','zym'],
        obj:{
          name:'yyb',
          age:'18',
          sex:'man'
        }
      }
    })
  </script>
</body>
```

### 官方写法

官方推荐我们在使用 v-for 时，给对应的元素添绑定一个key属性，key的值最好就是要显示的数据（item）

绑定key的方法：

```html
 <li  v-for="item in obj" :key="item">{{item}}</li> 
```

**为什么需要添加key属性**

> 这个和Vue的虚拟DOM的Diff算法有关系
>
> 在数据出现变化时（在数组中间插入元素），可以提高更新DOM的效率

![image-20210724150130043](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210724150130043.png)

**作用：**为了更加高效的更新虚拟DOM

## 数组中那些方法时响应式的

### 什么是响应式

Vue会监听我们的数据，当数据发生变化时，Vue会马上根据我们的新数据，渲染出虚拟DOM，然后根据虚拟DOM修改我们页面上的DOM

### 数组中的响应式方法

|   方法    |                             作用                             |
| :-------: | :----------------------------------------------------------: |
|  push()   |                      在数组末尾追加数据                      |
|   pop()   |                      删除数组末尾的数据                      |
|  shift()  |                      从数组开头删除数据                      |
| unshift() |                     在数组最前面添加数据                     |
| splice()  | 可以删除元素，插入元素，替换元素，第一个参数表示需要修改的位置，第二个参数表示需要删除几个元素（不写表示删除后面的所有元素），后面的参数表示需要插入的元素，例如` this.names.splice(2,1,'1','2','3')` |
|  sort()   |                             排序                             |
| reverse() |                             反转                             |

**响应式方法和非响应是方法一起使用非响应式的方法做出的改变也会显示在页面上**

## 使用Vue的set方法修改数组

一般情况下，最好使用js的方法修改数组，没必要使用其他的方法

**示例：**

```html
<script>
    const app = new Vue({
      el: '#app',
      data: {
        names:['yyb','pink','red','zym'],
      },methods: {
        btnClick(){
            //第一个参数是数组名称
            //第二个参数时索引值
            //第三个参数时修改后的数据
            Vue.set(this.names,1,'hello')
        }
      }
    })
  </script>
```

# 过滤器

## 定义过滤器

```html
<script>
const app = new new Vue({
  el:"#app",
  data:{
    books:[
      {
        id : 110,
        name : 'html入门到精通',
        date:'2001-2',
        price : 100,
        count:1
      }
	]}, 
  filters: {
    showPrice(price){
      return '￥'+price.toFixed(2)
    }
  }
});
</script>
```

## 使用过滤器

```html
<!--相当于将item.price传给函数showPrice，得到其返回值-->
<td>{{item.price | showPrice}}</td>
```

# 表单绑定 `v-model`

作用：实现表单元素和数据的双向绑定

## 基本使用

```html
<body>
  <div id='app'>
    <div>
      <!-- 将input中的数据与message绑定，修改任意一个，另外一个也会随之改变 -->
      <input type="text" v-model="message">
    </div>
    {{message}}
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      }
    })
  </script>
</body>
```

![image-20210725134319162](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210725134319162.png)

## 原理

v-model本质上是一个语法糖，背后包括两个操作

> v-bind 绑定一个value属性，将message绑定给input的value
>
> v-on 给当前元素绑定input事件，当input元素中的内容发生改变时，获取其中的数据，给message

v-model除了与input绑定之外，还可以与其他的标签绑定

## v-model绑定其他标签

### v-model绑定单选框

```html
<body>
  <div id='app'>
    <!--当单选框绑定v-model后，不需要加name属性也会互斥-->
    <input type="radio" id="main" name="sex" v-model="sex" value="男">男
    <input type="radio" id="women" name="sex" v-model="sex" value="女">女
    <br>
    您的性别是：{{sex}}
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        sex:'男'
      }
    })
  </script>
</body>
```

### v-model绑定复选框

 分为两种情况，单个复选框和多个复选框

**单个复选框，绑定对象是boolean类型**

```html
<div id='app'>
    <div>
      <input type="checkbox" id="agree" name="a" v-model="agree">同意协议
      <br>
      您选择的是：{{agree}}
    </div>
</div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        agree:false
      }
    })
  </script>
</body>
```

**多个复选框，绑定的对象应该是数组**

```html
<div id='app'>
    <div>
      <input type="checkbox" value="篮球" v-model="hobbies">篮球
      <input type="checkbox" value="足球" v-model="hobbies">足球
      <input type="checkbox" value="乒乓球" v-model="hobbies">乒乓球
      <input type="checkbox" value="羽毛球" v-model="hobbies">羽毛球
    </div>
    <ul>
      <li v-for="item in hobbies">{{item}}</li>
    </ul>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        hobbies:[]
      }
    })
  </script>
</body>
```

### v-model 结合select`下拉列表`使用

**选择单个：绑定对象是单个值**

```html
<div id='app'>
    <select name="abc" id="" v-model="fruit">
      <option value="苹果">苹果</option>
      <option value="香蕉">香蕉</option>
      <option value="菠萝">菠萝</option>
      <option value="荔枝">荔枝</option>
      <option value="西瓜">西瓜</option>
    </select>
    <h2>{{fruit}}</h2>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        fruit:'香蕉'
      }
    })
  </script>
</body>
```

**选择多个:绑定对象是数组**

```html
  <div id='app'>
    <select name="abc" id="" v-model="fruit" multiple>
      <option value="苹果">苹果</option>
      <option value="香蕉">香蕉</option>
      <option value="菠萝">菠萝</option>
      <option value="荔枝">荔枝</option>
      <option value="西瓜">西瓜</option>
    </select>
    <h2>{{fruit}}</h2>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        agree:false,
        hobbies:[],
        fruit:['香蕉']
      }
    })
  </script>
</body>
```

## 值绑定

真实开发中，不要直接把值写死，例如爱好，从服务器动态获取可以选择的爱好，使用v-for生成checkbox

**示例：**

```html
<body>
  <div id='app'>
    <label v-for="(item,index) in originHobbies">
      <input type="checkbox" :value="item" v-model="hobbies">{{item}}
    </label>
    <h2>{{hobbies}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        hobbies:[],
        originHobbies:['篮球','足球','乒乓球','羽毛球']
      }
    })
  </script>
</body>
```

## 修饰符

### lazy 懒加载

当数据发生变化后，不会实时更新数据，而是在输入框失去焦点，或者敲击回车后，将输入框中的数据同步到message中

```html
<body>
  <div id='app'>
    <!-- lazy -->
    <input type="text" v-model.lazy="message">
    <h2>{{message}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      }
    })
  </script>
</body>
```

### number

将输入的数据转换成number类型，如果使用text形式的输入框，输入的是数字和字符串混合形式的数据，会出现不可预料的结果

```html
<body>
  <div id='app'>
    <input type="number" v-model.number="message">
    <h2>{{message}}-{{typeof message}}</h2>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      }
    })
  </script>
</body>
```

### trim

去除数据左右两边的空格

```html
<body>
  <div id='app'>
    <input type="text" v-model.trim="message">
    <h2>{{message}}-{{typeof message}}</h2>
  </div>
    
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      }
    })
  </script>
</body>
```

# 组件化

## Vue的组件化思想

1. 组件化是Vue中的重要思想

   > 它提供了一种抽象，让我们可以开发出一个独立可复用的小组件来构造问我们的应用
   >
   > 它的任何应用都会被抽象成一颗组件树

   ![image-20210726102122365](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210726102122365.png)

2. 组件化思想的应用

   > 尽可能的将页面拆分成一个个小的，可复用的组件
   >
   > 这样，我们的代码更加方便的组织和管理，并且扩展性也更强

## 注册组件的基本步骤

组件的使用分成三个步骤：

> 创建组件构造器
>
> > 调用Vue.extend()创建组件构造器
> >
> > 通常在创建组件构造器时，传入template代表我们组件的模板
> >
> > 该模板就是在需要用到组件的地方显示的Html代码
> >
> > 事实上这种方式已经不多见，我们可以使用语法糖来快速创建
>
> 注册组件
>
> > 调用Vue.component()将刚才的组件构造器注册成一个组件，并给他起一个组件标签名称
> >
> > 需要传递两个参数：1、注册组件的标签名，2、组件构造器
>
> 使用组件
>
> > 组件必须挂载到某个vue示例下，否则不会生效

**示例：**

```html
<body>
  <div id='app'>
    <!-- 3.使用组件 -->
    <my-cpn></my-cpn>
    <my-cpn></my-cpn>
    <my-cpn></my-cpn>
    <my-cpn></my-cpn>
  </div>

  <script>
    // 1. 创建组件构造器对象
    const cpnC = Vue.extend({
      template:`
        <div>
          <h2>我是标题</h2>
          <h2>啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊</h2>
          <h2>呵呵</h2>
        </div>`
    })

    // 2. 注册组件
    Vue.component('my-cpn', cpnC)

    const app = new Vue({
      el: '#app'
    })
  </script>
</body>
```

## 全局组件和局部组件

全局组件可以在所有的Vue实例中使用，而局部组件只能在当前注册的Vue实例中使用

```html
  
<script>
    // 1. 创建组件构造器对象
    const cpnC = Vue.extend({
      template:`
        <div>
          <h2>我是标题</h2>
          <h2>啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊</h2>
          <h2>呵呵</h2>
        </div>`
    })

    // 2. 注册组件(全局组件)
    Vue.component('my-cpn', cpnC)

    const app = new Vue({
      el: '#app',
      components: {
        //定义局部组件，cpn是标签名，cpnC是构造器对象
        cpn:cpnC
      }
    })
</script>
```

## 父组件和子组件

在组件构造器 cpnC2 中注册组件构造器 cpnC1 ，组件cpnC2就是父组件，cpnC1就是子组件

可以在父组件中使用子组件，但不能在Vue实例中使用子组件

只有在注册过的全局组件和局部组件才能在Vue实例中使用

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>
    <cpn2></cpn2>

  </div>
  <script>
    //子组件
    const cpnC1 = Vue.extend({
      template: `
        <div>
          <h2>我是标题一</h2>
          <p>aaaaaaaaaaaaaaaaaaaaaaa</p>
          <p>bbbbbbbbbbbbb</p>
        </div>
      `
    })

    //父组件
    const cpnC2 = Vue.extend({
      template: `
        <div>
          <h2>我是标题二</h2>
          <p>啊啊啊啊啊啊啊啊啊啊啊啊啊</p>
          <p>不不不不不不不不不不不不</p>
          <cpn1><cpn1>
        </div>
      `,
      components: {
        cpn1:cpnC1
      }
    })

    //vue示例也可以看做一个最顶层的组件
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },components: {
        cpn2:cpnC2
      }
    })
  </script>
</body>
```

## 注册组件的语法糖

省略了Vue.extend创建构造器的步骤

### 语法糖注册全局组件

```html
<body>
  <div id='app'>
    <cpn1></cpn1>
  </div>

  <script>
    //在Vue.component完成组件的构造和注册，cpn1 是标签名，后面的是组件构造器
    Vue.component('cpn1', {
      template: `
        <div>
          <h2>我是标题一</h2>
          <p>aaaaaaaaaaaaaaaaaaaaaaa</p>
          <p>bbbbbbbbbbbbb</p>
        </div>
      `
    })

    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      }
    })
  </script>
</body>
```

### 语法糖注册局部组件

```html
<body>
  <div id='app'>
    <cpnn1></cpnn1>
  </div>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },components: {
        //cpnn1是标签名，后面的是组件构造器
        cpnn1:{
          template: `
            <div>
              <h2>我是标题一</h2>
             <p>aaaaaaaaaaaaaaaaaaaaaaa</p>
             <p>bbbbbbbbbbbbb</p>
            </div>
          `
        }
      }
    })
  </script>
</body>
```

## 模板分离的写法

<font color='red'>注意：模板文件中如果有多个标签，则必须要有一个根` <div>` 标签将其包裹</font>

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>
    <cpn></cpn>
    <cpn2></cpn2>
  </div>

  <!-- 创建组件的模板 -->
  <script type="text/x-template" id="cpn">
    <div>
      <h2>hhhhhhhhhhhhhhhhhhhhhhh</h2>
    </div>
  </script>

  <!-- 创建组件的模板 -->
  <template id="cpn2">
    <div>
      <h2>我是template</h2>
    </div>
  </template>

  <script>
    // 注册组件
    Vue.component('cpn', {
      template:'#cpn'
    })

    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },components: {
        cpn2:{template:'#cpn2'}
      }
    })
  </script>
</body>
```

## 组件中数据的存放

组件不能直接访问Vue实例的data中的数据，Vue组件有自己的保存数据的地方

### 组件自己的数据存放在哪里？

组件对象也有一个data属性（一颗有method等与实例一样的属性）

这个data必须是一个函数

这个函数必须返回一个对象，里面可以保存着数据

### 示例：

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>
    <cpn1></cpn1>
  </div>

  <!--模板-->
  <template id="cpn">
    <div>
      <h2>{{title}}</h2>
    </div>
  </template>

  <script>
      
    //注册组件
    Vue.component('cpn1', {
      template:'#cpn',
      //数据，data属性
      data(){
        //返回对象，里面存放数据
        return {
          title:'hhhhhhhhhhh'
        }
      }
    })

    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      }
    })
  </script>
</body>
```

### 为什么组件的data必须是一个函数

当一个组件被对此使用后，如果data不是一个函数，那么各个组件的数据就是通用的，在修改一个组件中的数据后，其他的组件中的数据也会随之更改

## 父子组件的通信

子组件不能直接访问父组件中的数据，但是，在开发中确实有一些数据需要传递到下层

### 父传子

**父组件通过props向子组件传递数据，props可以是数组，也可以是对象**

props是对象时，需要指定数据的数据类型

验证支持的数据类型：**除此之外，还可以使用自定义数据类型**

| String | Number | Boolean | Array | Object | Date | Function |          Symbol           |
| :----: | :----: | :-----: | :---: | :----: | :--: | :------: | :-----------------------: |
| 字符串 |  数字  | 布尔值  | 数组  |  对象  | 时间 |   函数   | 函数栈（ES6的新数据类型） |

```html
<body>
  <div id='app'>
    <!-- 使用v-bind动态传递数据,不使用v-bind会传递字符串 -->
    <cpn1  :cmovies="movies" :cmessage="message"></cpn1>
  </div>

  <template id="cpn">
    <div>
      <h2>aaaaaaaaaaaaaaaaaaaa</h2>
      <!-- 遍历movise中的数据 -->
      <ul>
        <li v-for="item in cmovies">{{item}}</li>
      </ul>
      <h2>{{cmessage}} </h2>
    </div>
  </template>

  <script>
      const cpn1 = {
        template:'#cpn',
        // 使用props，传递多个数据
        /*
        props传递对象时，需要指定数据的数据类型
        props: {
          cmovies : Array,
          cmessage : String
        }
        
        在使用对象形式的props时，还可以传递默认值等其他限制,当数据时Array形式时，default必须是一个函数
        props: {
          cmovies : {
          	type：Array,
          	default(){       //默认值
          	  return []
          	},          
          	
          },
          cmessage : {
          	type：String,
          	default:'hello',       //默认值
          	required:true          //是否必须，如果为true则必须传递此参数，否则会报错（只是显示错误信息，不会终止程序）
          	validator:function(value){          //自定义验证函数
          		//验证
          	}
          }
        }
        */
        props: ['cmovies','cmessage'],
        data(){
          return {

          }
        }
      }

    const app = new Vue({
      el: '#app',
      data: {
        message:'hello',
        movies:['aaa','bbb','ccc','ddd']
      },components: {
        cpn1
      }
    })
  </script>
</body>
```

<font color='red'>注意：在props传递数据时，由于传递数据时的 v-bind 不支持驼峰命名法，所以最好不要使用驼峰命名法，否则可能会接收不到数据。如果非要写，可以在动态传值在大写字母前用“-”分开。例如：`<cpn1  :c-movies="movies" :cmessage="message"></cpn1>`：</font>

```html
<body>
  <div id='app'>
    <!-- 为驼峰命名法命名的数据传值 -->
    <cpn1  :c-movies="movies" :c-message="message"></cpn1>
  </div>
  <!--模板-->
  <template id="cpn">
      <h2>{{cmovies}}{{cMessage}} </h2>
    </div>
  </template>

  <script>
      const cpn1 = {
        template:'#cpn',
        props: {
          //驼峰命名法
          cMovies : Array,
          cMessage : String
        },
        data(){
          return {

          }
        }
      }

    const app = new Vue({
      el: '#app',
      data: {
        message:'hello',
        movies:['aaa','bbb','ccc','ddd']
      },components: {
        cpn1
      }
    })
  </script>
</body>
```

### 子传父

使用自定义事件向父组件传递数据

> 当子组件向父组件传递数据时，需要使用自定义事件
>
> v-on 命令可以监听自定义事件

自定义事件的流程

>  在子组件中，通过 $emit() 来触发事件
>
> 在父组件中通过 v-on 监听子组件发送的事件

**示例：**

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>
    <!-- 监听 itemclick 自定义事件，给回调函数 cpnClick -->
    <cpn :message='message' @itemclick="cpnClick"></cpn>
  </div>

  <!-- 模板 -->
  <template id="cpn">
    <div>
      <h2>hello</h2>
      <h2>{{message}}</h2>
      <!-- 监听组件的点击事件 btnClick ,传递参数item.id -->
      <button v-for="item in categories" @click="btnClick(item.id)">{{item.name}}</button>
    </div>
  </template>
  <script>

    const cpn = {
      template:'#cpn',
      props: {
        message : String
      },
      data(){
        return {
          categories:[
            {
            id:'yyb',name:'热门'
          },
          {
            id:'pink',name:'哈哈哈'
          },
          {
            id:'yyzzzb',name:'白给'
          },
          {
            id:'aaaa',name:'发狙'
          }
        ]
        }
      },
      methods: {
        // 组件的点击事件
        btnClick(id){
          // 发送自定义事件 itemclick 发送给父组件，id是参数
          this.$emit('itemclick',id)
        }
      }

    }

    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },
      components: {
        cpn
      },
      methods: {
        cpnClick(id){
          console.log('id =' + id);
        }
      }
    })
  </script>
</body>
```

## watch

用于监听数据的改变

**案例：子组件修改自己的数据，传递给父组件，与父组件中的数据同步**

```html
<body>
  <div id='app'>
    <!--使用组件，为number1、2分别绑定num1、2；监听number1,2事件-->
    <cpn :number1='num1' :number2='num2' @number1='upnumber1' @number2='upnumber2'/>
  </div>

  <!--模板文件-->
  <template id="cpn">
    <div>
      <h2>{{number1}}</h2>
      <h2>{{anumber1}}</h2>
      <!-- 修改anumber1，与data中的anumber1双向绑定-->
      <input type="number" v-model="anumber1">
      <h2>{{number2}}</h2>
      <h2>{{anumber2}}</h2>
      <!-- 修改anumber2，与data中的anumber2双向绑定 -->
      <input type="number" v-model="anumber2">
    </div>
  </template>


  <script>
    //子组件
    const cpn = {
      template:'#cpn',
      //父组件传入两个数据
      props: {
        number1:Number,
        number2:Number
      },
      data(){
        return{
          //将父组件传入的数据放入data函数，作为本地数据
          anumber1:this.number1,
          anumber2:this.number2
        }
      },
      //监听data中的数据的变化，
      watch: {
        //anumber1发生变化，将其变化后的新数据作为参数发送给number1事件
        //newValue是新数据，还可以使用oldValue获取旧数据
        anumber1(newValue){
          this.$emit('number1',newValue)
        },
        //anumber2发生变化，将其变化后的新数据作为参数发送给number2事件
        anumber2(newValue){
          this.$emit('number2',newValue)
        }
      }
    }

    //父组件
    const app = new Vue({
      el: '#app',
      data: {
        num1:1,
        num2:0
      },components: {
        //注册组件
        cpn
      },
      methods: {
        //number1事件发生后的回调函数，参数number1是子组件传入的anumber1的新数据
        upnumber1(number1){
          this.num1 = parseInt(number1)
        },
        //number2事件发生后的回调函数，参数number2是子组件传入的anumber2的新数据
        upnumber2(number2){
          this.num2 = parseInt(number2)
        }
        }
    })
  </script>
</body>
```

## 父子组件的相互访问

有时候我们需要使用父组件注解访问子组件，或子组件注解访问父组件，直接调用其方法或者属性

### 父组件访问子组件：

使用 $children 或 $refs     (reference的简写：意思是引用)

推荐使用 $refs，在组件中插入新的子组件后，使用 $children 访问的子组件的索引值可能会发生变化

**示例：**

```html
<body>
  <div id='app'>
    <cpn1></cpn1>
    <cpn1></cpn1>
    <cpn1 ref="aaa"></cpn1>
    <cpn1 ref="bbb"></cpn1>
    <button @click="btnClick1">$children访问子组件</button>
    <button @click="btnClick2">使用$refs访问子组件</button>
  </div>

  <template id="cpn">
    <div>
      我是子组件
    </div>
  </template>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },
      components: {
        // 注册子组件cpn1
        cpn1 : {
          template:'#cpn',
          methods: {
            showMessage(){
              console.log('showMessage');
            }
          },
          data(){
            return {
              name : 'hello world'
            }
          }
        }  
      },
      methods: {
        // 使用$children访问子组件,得到的是所有子组件的数组
        btnClick1(){
          console.log(this.$children);         //Array(4)
          console.log(this.$children[0].name);     //hello world
          this.$children[0].showMessage();       //showMessage
        },
         // 使用$refs访问子组件,得到的是包含ref属性的子组件集合
        btnClick2(){
          console.log(this.$refs);      //{aaa: VueComponent, bbb: VueComponent}
          console.log(this.$refs.aaa);        // VueComponent{}
          console.log(this.$refs.aaa.name);        //hello world
        }
      }
    })
  </script>
</body>
```

### 子组件访问父组件：

使用 $parent（访问父组件）和 $root（直接访问根组件，即Vue实例）

```html
<body>
  <div id='app'>
    <cpn1></cpn1>
  </div>

  <template id="cpn">
    <div>
      我是子组件
      <ccpn></ccpn>
      <button @click="btnClick">子组件访问父组件</button>
    </div>
  </template>

  <template id="cpn2">
    <div>
      我是子组件的子组件
      <button @click="btnClick2">子组件的子组件访问父组件</button>
    </div>
  </template>

  <script>
    //父组件
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },
      components: {
        // 注册子组件cpn1
        cpn1 : {
          template:'#cpn',
          methods: {
            btnClick(){
              console.log(this.$parent);      //Vue {}
              console.log(this.$parent.message);
            }
          },
          data(){
            return {
              name : '子组件的name'
            }
          },
          components: {
            //子组件的子组件
            ccpn:{
              template:'#cpn2',
              methods: {
                btnClick2(){
                  console.log(this.$parent);          //VueComponent {}   子组件
                  console.log(this.$parent.name);      //子组件的name
                  console.log(this.$root);         //Vue {}   直接访问Vue示例（根组件）
                  console.log(this.$root.message);       //hello
                }
              }
            }
          }
        }
      }
    })
  </script>
</body>
```

## slot插槽

生活中有许多的插槽，例如电脑上的 usb插槽，硬盘插槽，电源插槽等等

### 组件的插槽

组件的插槽也是为了让我们封装的组件更加具有扩展性

让使用者可以决定组件内部的一些内容到底展示什么

### 插槽的基本使用

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>
    <cpn1>
      <!-- 使用组件，里面添加的标签会显示在插槽里 -->
      <button>按钮</button>
    </cpn1>
    <cpn1>
      <span>
        哈哈哈
      </span>
    </cpn1>
    <cpn1>
      <a href="#">连接</a>
    </cpn1>
    <cpn1>
      <p>aaaaaaaaaaaa</p>
    </cpn1>
    <cpn1>
      我是纯文字
    </cpn1>
    <cpn1></cpn1>
    <cpn1></cpn1>

  </div>

  <template id="cpn">
    <div>
      <h2>组件</h2>
      <p>hhhhhhhhhhhhhhhhhhhhhhhhhhhh</p>
      <!-- 定义插槽，里面的 button 标签是默认值，使用时没有传入东西就会显示按钮 -->
      <slot><button>按钮</button></slot>
    </div>
  </template>
  <script>

    const cpn1 = {
      template : '#cpn',
    }

    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },
      // 注册组件
      components: {
        cpn1
      }
    })
  </script>
</body>
```

**示例：**

![image-20210727140618237](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210727140618237.png)

---

### 具名插槽

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>
    <cpn1>
      <button slot="left">按钮</button>
      <!-- 替换指定的插槽，用slot属性是定插槽的name -->
      <div slot="center">Title</div>
    </cpn1>
  </div>

  <template id="cpn">
    <div>
      <h2>组件</h2>
      <p>hhhhhhhhhhhhhhhhhhhhhhhhhhhh</p>
      <!-- 定义具名插槽，为slot添加name属性，里面的 span 标签是默认值，使用时没有传入东西就会显示按钮 -->
      <slot name="left"><span>左</span></slot>
      <slot name="center"><span>中</span></slot>
      <slot name="reght"><span>右</span></slot>
    </div>
  </template>

  <script>
    const cpn1 = {
      template : '#cpn',
    }

    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },
      // 注册组件
      components: {
        cpn1
      }
    })
  </script>
</body>
```

### 作用域插槽

作用域：

![image-20210727143723721](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210727143723721.png)

**作用域插槽：**

父组件替换插槽里的标签，但标签的内容由子组件通过

其实就是调用组件时，把slot里需要使用的数据交给父组件，使用新的方法展示

```html
<body>
  <div id='app'>
    <h2>{{message}}</h2>

    <!-- 默认展示 -->
    <cpn1></cpn1>
    
    <!-- 使用新的方法展示 -->
    <cpn1>
      <!-- slot-scope 获取需要展示的插槽对象 -->
      <template slot-scope="slot">
        <!-- slot.data 就是需要展示的数据 pLanguage -->
        <span>{{slot.data.join('-')}}</span>
      </template>
    </cpn1>


  </div>
  <template id="cpn">
    <div>
      <!-- 使用 :data 设置需要展示的数据 -->
      <slot :data="pLanguages">
        <ul>
          <li v-for="item in pLanguages">{{item}}</li>
        </ul>
      </slot>
    </div>
  </template>

  <script>
    const app = new Vue({
      el: '#app',
      data: {
        message: 'hello'
      },
      components: {
        cpn1 : {
          template:'#cpn',
          data(){
            return {
              pLanguages:['js','c++','java','python','c#','go']
            }
          }
        }
      }
    })
  </script>
</body>
```

# 模块化开发

## 常规的模块化规范

CommonJS、AMD、CMD、ES6的Modules

### 模块化开发的核心是：导入和导出

CommonJS的导出：

```javascript
module.exports = {
    flag : true,
    test(a,b){
        return a+b
    }
}
```

CommonJS的导入：

```javascript
let {flag,test} = require('mudile');
```

## ES6的模块化实现

定义模块：

```html
<body>
  hello
  <script src="aaa.js" type="module"></script>
  <script src="bbb.js" type="module"></script>
</body>
```

export  导出：

```javascript
let num1 = 10
function sum(a,b) {
  return a+b;
}
let name = "yyb"

//导出方式一
export {num1,name,sum}

//导出方式二
export let num2 = 30
export let age = 'man'
```

export defult 导出：

当某些情况下，一个模块中包含某个功能，我们不希望给这功能命名，而让导入者自己命名

在同一个模块中，不允许存在多个export defult导出

```javascript
let aaa = 'hello'
export default aaa
```

导入：可以使用 * 导入所有内容

```javascript
import { sum,num1,num2 } from "./aaa.js";

//使用 * 导出所有内容，as aaa 是别名，之后取值需要使用 aaa.num1 的形式
//import * as aaa from "./aaa.js"
//console.log(aaa.num1);

console.log(num2);
```

导入export defult导出的属性，自己命名，不需要跟导出时的名称一样

```javascript
import a from "./aaa.js"
console.log(a);
```

# node使用npm安装可以使用淘宝镜像安装

安装淘宝镜像：npm install -g cnpm --registry=https://registry.npm.taobao.org

使用：将npm的语法中npm改为cnpm

# webpack

## 什么是webpack

webpack是一个现代化的JavaScript引用的静态**模块打包**工具

即有两个核心的功能，模块，打包

官网[https://webpack.docschina.org/](https://webpack.docschina.org/)

## 前端模块化

> 在ES6之前，想要进行前端模块化必须要借助其他工具，
>
> 并且，在通过模块化开发完成项目后，还需要处理模块之间的各种依赖，并且进行整合打包
>
> 而webpack其中的一个核心就是让我们可能进行模块化开发，并且帮助我们处理模块之间的依赖关系
>
> 而且，不仅仅是JavaScript文件，我们的CSS，图片，json文件等，在webpack中都能被当做模块来使用

## 如何理解打包

> 就是将webpack中的各种资源模块进行打包合并成一个或多个包
>
> 并且在打包的过程中，还可以对资源进行处理，比如压缩图片，将scss转化成css，将ES6语法转换成ES5语法，将TypeScript转换成JavaScript等等操作

## webpack安装

webpack依赖node环境才能正常运行，安装node环境版本不能小于8.5

node -v查看已经安装的node的版本

全局安装webpack（这里安装3.6.0，因为cli3的webpack配置被异常起来了，所以只能使用cli2，而 vue cli2 依赖该版本

**在cmd中执行命令：**  -g 表示全局安装

```shell
npm install webpack@3.6.0 -g
```

![image-20210728102402480](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728102402480.png)

**查看已经安装的webpack版本：**

![image-20210728102435740](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728102435740.png)

## webpack的基本使用

1. ##### 先编写代码

   main.js

   ```javascript
   import { add1,num } from "./mathUtil.js";
   console.log(add1(30,20));
   console.log(num(10,2));
   ```

   mathUtil.js

   ```javascript
   function add1(num1,num2){
     return num1 + num2
   }
   function num(num1,num2) {
     return num1 * num2
   }
   
   export {
     add1,num
   }
   ```

   

2. ##### 终端进入项目目录执行命令：webpack ./src/main.js ./dist/bundle.js

   意思是：使用webpack 将 .\src\main.js 及其依赖打包，生成的文件是 ./dist/ 下的 bundle.js

   ![image-20210728105400409](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728105400409.png)

   **<font color="red">出现报错在此系统上禁止运行脚本的解决方案:</font>**

   ![image-20210728104631973](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728104631973.png)

   **以管理员身份运行：**Windows PowerShell

   **输入命令：**Set-ExecutionPolicy RemoteSigned

   **选择：**Y

   ![image-20210728104938405](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728104938405.png)

3. ##### 使用刚刚打包的文件

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta http-equiv="X-UA-Compatible" content="IE=edge">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <title>Document</title>
   </head>
   <body>
     hello
     <script src="./dist/bundle.js"></script>
   </body>
   </html>
   ```

## webpack的配置

1. 在项目根目录下创建 webpack.config.js 文件

   ![image-20210728110728315](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728110728315.png)

2. 编写配置文件，配置好后即可直接使用 webpack 打包，不需要指定其他东西

   ```javascript
   // 使用node的path，获取当前项目的绝对路径
   const path = require('path')
   
   module.exports = {
     // 配置入口文件
     entry:'./src/main.js',
     output:{
       // 将绝对路径与dist包相加，得到导出的目录
       path:path.resolve(__dirname,'dist'),
       // 配置导出的文件名
       filename:'bundle.js'
     },
   }
   ```

3. 使用`PS E:\VSCode_work\webpack> npm init`命令初始化项目

   会生成package.js配置文件

   ```json
   {
     "name": "webpack",
     "version": "1.0.0",
     "description": "",
     "main": "index.js",
     
     "scripts": {
       // 配置快捷命令，使用 npm run test 即使用期后面的具体命令,这个命令优先使用本地webpack，没有才会使用全局webpack
       "test": "echo \"Error: no test specified\" && exit 1",
       "build": "webpack"
     },
     "author": "",
     "license": "ISC",
     "devDependencies": {
       "webpack": "^3.6.0"
     }
   }
   ```

4. 使用`PS E:\VSCode_work\webpack> npm install webpack@3.6.0 --save-dev `安装本地webpack

## webpack核心一 loader 

webpack本来是用来打包js文件的，但是在开发中我们不仅仅有js文件，还有css，图片，将ES6或TypeScript转换成ES5,等文件；

对于webpack本身的功能来说是不支持css，图片等其他功能的，但我们可以给webpack扩展对应的loader

#### loader的使用过程：

1. 通过npm安装对应的loader
2. 在webpack.config.js中的modules关键字下面进行配置

#### 查看需要使用什么loader

点击需要使用的loader会出现安装方法

[https://webpack.docschina.org/loaders/](https://webpack.docschina.org/loaders/)

### webpack打包css文件

1. 编写css代码

   ```css
   body {
     background-color: antiquewhite;
   }
   ```

2. 在main.js文件中添加对css文件的依赖

   ```javascript
   // 依赖css文件
   import css from "./css/normal.css"
   ```

3. 安装loader，`3.6.0`版本的webpack兼容`0.28.11`的css-loader

   命令：`npm install --save-dev css-loader@0.28.11`

4. 按照官方文档的使用方法进行配置

   在**webpack.config.js**中添加`css.loader`

   ```javascript
   module.exports = {
     module: {
       rules: [
         {
           test: /\.css$/i,
           use: ["css-loader"],
         },
       ],
     },
   };
   ```

5. css-loader只负责文件的加载，需要与style-loader一起使用才会展示样式

   安装style-loader：`webpack3.6.0`支持`0.1.5`版本的style-loader

   修改**webpack.config.js**文件：

   ```javascript
   module:{
       rules: [
         {
           test: /\.css$/i,
           use: ["style-loader","css-loader"],
         },
       ],
     }
   ```

   style-loader将css-loader加载的配置文件添加到DOM中，使其生效。

   user的读取顺序是从后往前，而style依赖css，所以`use: ["style-loader","css-loader"]`中，style-loader必须添加在前面，否则会报错

6. 打包，使用本地webpack，命令：`PS E:\VSCode_work\webpack> npm run build`

7. 测试，样式修改成功

   ![image-20210728130807534](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728130807534.png)

### webpack图片文件的处理

1. 在css文件中添加图片作为背景图片

   ```css
   body {
     background: url(../img/a.jpg);
   }
   ```

2. 安装url-loader，`webpack3.6.0`对应的url-loader是`1.0.0`版本

   命令：` npm install url-loader --save-dev`

3. 在**webpack.config.js**文件中配置module：

   ```javascript
     // 添加loader
     module:{
       rules: [
         {
           test: /\.(png|jpg|gif)$/i,
           use: [
             {
               loader: 'url-loader',
               options: {
                 // limit文件的最大体积，kb*1024
                 limit: 2048576,
               },
             },
           ],
         },
       ],
     }
   ```

   limit配置小于需要添加的文件的体积就会报错

   ![image-20210728140113791](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210728140113791.png)

4. 对于超过limit的图片，需要使用file-loader进行处理

### ES6语法转ES5

使用babel-loader

详情看官网

## webpack核心二 plugin[^插件]

### loader和plugin的区别

loader主要用于转换某些类型的模块，是一个转换器

plugin是一个插件，他是对webpack本身的扩展，是一个扩展器

### plugin的使用过程

**步骤：**

> 通过npm安装需要使用的plugin（某些webpack内置的插件不需要安装）、
>
> 在webpack.config.js文件的plugins中配置插件

### 添加版权的plugin

```javascript
const webpack = require('webpack')

module.exports = {
  // 配置入口文件
  entry:'./src/main.js',
  output:{
    // 将绝对路径与dist包相加，得到导出的目录
    path:path.resolve(__dirname,'dist'),
    // 配置导出的文件名
    filename:'bundle.js'
  },
  plugins:[
    //版权信息
    new webpack.BannerPlugin('hello 请给我版权费！！！')
  ]
}
```

### 将index.html文件打包的plugin

使用HtmlWebpackPlugin插件，会自动生成一个index.html文件（可以指定模板），将打包的js文件通过script标签插入到body中

步骤：

> 安装插件 执行命令：npm install html-webpack-plugin --save-dev
>
> 修改webpack.config.js
>
> ```javascript
> const HtmlWebpackPlugin = require('html-webpack-plugin')
> 
> module.exports = {
>   // 配置入口文件
>   entry:'./src/main.js',
>   output:{
>     // 将绝对路径与dist包相加，得到导出的目录
>     path:path.resolve(__dirname,'dist'),
>     // 配置导出的文件名
>     filename:'bundle.js'
>   },
>   plugins:[
>     //添加插件
>     new HtmlWebpackPlugin(
>         //模板，可写可不写，不写就默认生成原始的html，并引入打包的js文件
>         {
>             template:'index.html'
>         }
>     )
>   ]
> }
> ```

### JS压缩plugin

> 安装：npm install uglifyjs-webpack-plugin --save-dev
>
> 修改webpack.config.js
>
> ```javascript
> const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin')
> 
> module.exports = {
>   // 配置入口文件
>   entry:'./src/main.js',
>   output:{
>     // 将绝对路径与dist包相加，得到导出的目录
>     path:path.resolve(__dirname,'dist'),
>     // 配置导出的文件名
>     filename:'bundle.js'
>   },
>   plugins:[
>     new UglifyjsWebpackPlugin(),
>   ]
> }
> ```

# 在webpack中使用Vue的配置过程

## 步骤

1. 使用npm命令安装vue模块：npm install vue --save

   ![image-20210729102520644](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210729102520644.png)

2. 在main.js中编写vue代码

   ```javascript
   // 导入vue
   import Vue from "vue"
   
   // 使用Vue
   const app = new Vue({
     el : '#app',
     data : {
       message : 'Hello Vue'
     }
   })
   ```

3. 配置别名

   npm安装的vue有两种，runtime-only和runtime-compiler，默认使用runtime-only

   但是，使用runtime-only的代码中不可以有任何template（模板），所以我们需要使用别名，指定我们需要的runtime-compiler版本

   ```javascript
   // 使用node的path，获取当前项目的绝对路径
   const path = require('path')
   
   module.exports = {
     // 配置入口文件
     entry:'./src/main.js',
     output:{
       // 将绝对路径与dist包相加，得到导出的目录
       path:path.resolve(__dirname,'dist'),
       // 配置导出的文件名
       filename:'bundle.js'
     },
     resolve:{
       // 别名
       alias: {
         'vue$':'vue/dist/vue.esm.js',
         //使用@就表示在src文件夹下寻找文件
         '@':'src'
       }
     }
   }
   ```

4. 在HTML文件中引入打包后的文件，使用vue的数据

   ```html
   <body>
     <div id="app">
      <h2>{{message}}</h2>
     </div>
     hello
     <script src="./dist/bundle.js"></script>
   </body>
   ```

## el和template的区别

正常运行后我们考虑另外的问题：

- 我国我们希望data中的数据显示在界面中，就必须修改index.html文件
- 如果我们自定义了组件，也必须修改index来使用组件
- 但html模板在之后的开发中，我们并不希望手动的频繁修改

如果同时出现el和template，template会将el中的东西替换掉，例如：

```html
<body>
  <div id="app">
   <h2>{{message}}</h2>
   <button @click="btnClick">按钮</button>
  </div>
  hello
  <script src="./dist/bundle.js"></script>
</body>
```

```javascript
// 导入vue
import Vue from "vue"

// 使用Vue
const app = new Vue({
  el : '#app',
  template:`
  <div>
    <h2>{{message}}</h2>
    <button @click="btnClick">按钮</button>
    <h2>{{name}}</h2>
  </div>
  `,
  data : {
    message : 'Hello Vue',
    name : 'yyb'
  },methods: {
    btnClick(){
    }
  }
})
```

显示：

![image-20210729105555877](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210729105555877.png)

## 分离template中的代码和Vue实例

将template中的代码注册成组件,然后在template中使用组件的代码，可以达到同样的效果

```javascript
// 导入vue
import Vue from "vue"

//组件
const app = {
  template : `
  <div>
    <h2>{{message}}</h2>
    <button @click="btnClick">按钮</button>
    <h2>{{name}}</h2>
  </div>
  `,
  data(){
    return {
      message : 'Hello Vue',
      name : 'yyb'
    }
  },
  methods: {
    btnClick(){}
  }
}


// 使用Vue
new Vue({
  el : '#app',
  template : `<app/>`
  ,
  components: {
    app
  }
})
```

## 分离组件的Vue实例，将其放到不同的文件，减少Vue实例的代码量

创建app.js文件

```javascript
//导出组件
export default {
  template : `
  <div>
    <h2>{{message}}</h2>
    <button @click="btnClick">按钮</button>
    <h2>{{name}}</h2>
  </div>
  `,
  data(){
    return {
      message : 'Hello Vue',
      name : 'yyb'
    }
  },
  methods: {
    btnClick(){}
  }
}
```

在vue实例中使用组件

```javascript
// 导入vue
import Vue from "vue"
// 导入组件
import app from "./js/app.js"

// 使用Vue
new Vue({
  el : '#app',
  template : `<app/>`
  ,
  // 注册组件
  components: {
    app
  }
})
```

## 使用 .Vue文件，分离app.js文件中的模板和对象

创建app.vue

```vue
<template >
    <div>
    <h2 id="title">{{message}}</h2>
    <button @click="btnClick">按钮</button>
    <h2>{{name}}</h2>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return {
      message : 'Hello Vue',
      name : 'yyb'
    }
  },
  methods: {
    btnClick(){}
  }
}
</script>

<!--使用style样式需要添加关于css的loader-->
<style scoped>
#title {
  color: red;
}
</style>
```

在vue实例中使用app.vue

```javascript
// 导入vue
import Vue from "vue"
// 导入vue文件
import app from "./vue/app.vue"

// 使用Vue
new Vue({
  el : '#app',
  template : `<app/>`
  ,
  // 注册组件
  components: {
    app
  }
})
```

配置解析 .vue 文件的loader

需要 vue-loader（加载vue文件），vue-template-compiler（编译vue文件）

注意：vue-template-compiler不需要在 webpack.config.js 中配置 module

使用命令安装：`npm install vue-loader vue-template-compiler --save-dev`

配置webpack.config.js文件：

```javascript
//vue-loader@15.x.x 版本的需要添加插件，不想配置插件的话可以降低vue-loader的版本号
const VueLoaderPlugin = require('vue-loader/lib/plugin')

module.exports = {
  module: {
    rules:[
      {
        test:/\.vue$/,
        use : ['vue-loader']
      }
    ],
  },
  plugins:[
    //vue-loader@15.x.x 版本的需要添加插件
    new VueLoaderPlugin()
  ]
}
```

# 搭建本地node服务器[^webpack-dev-server]

webpack提供了一个基于node搭建的，可选的本地开发服务器，内部使用express框架，可以实现我们想要的浏览器自动刷新显示我们修改后的效果

它不是一个单独的模块，需要在webpack中安装它：`npm install --save-dev webpack-dev-server`

devserver作为一个webpack的选项，本身可以设置如下属性：

contentBase：为哪一个文件夹通过本地服务，默认是根文件夹，我们需要改成 `./dist`

port：端口号，默认8080

inline：页面实时刷新

historyApiFallback：在SPA页面中，依赖HTML5的history模式

## 步骤：

1. 安装

   命令：npm install --save-dev webpack-dev-server

2. 配置webpack.config.js

   ```javascript
   module.exports = {
     devServer:{
       contentBase:'./dist',
       inline:true,
     }
   }
   ```

3. 运行服务

   注意，报错`npm ERR! Failed at the vue@1.0.0 server script.`需要降低webpack-cli的版本，使用命令` npm i webpack-cli@3.3.12 -D`

   **方式一**

   找到 webpack-dev-server 命令所在目录，并执行

   命令：`.\node_modules\.bin\webpack-dev-server`

   **方式二**

   在package.js中配置命令

   ```json
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "build": "webpack",
       //启动服务命令， --open表示自动打开浏览器
       "server": "webpack-dev-server --open"
     },
   ```

   然后运行`npm run service`启动服务

# 将开发时的配置文件和发布时的配置文件抽离

## 步骤：

### 创建三个配置文件：

![image-20210730133859807](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210730133859807.png)

**base.config.js**

```javascript
// 使用node的path，获取当前项目的绝对路径
const path = require('path')
const VueLoaderPlugin = require('vue-loader/lib/plugin')

const webpack = require('webpack')
// const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  // 配置入口文件
  entry:'./src/main.js',
  output:{
    // 将绝对路径与dist包相加，得到导出的目录
    path:path.resolve(__dirname,'dist'),
    // 配置导出的文件名
    filename:'bundle.js'
  },
  resolve:{
    // 别名
    alias: {
      'vue$':'vue/dist/vue.esm.js'
    }
  },
  module: {
    rules:[
      {
        test:/\.vue$/,
        use : ['vue-loader']
      }
    ],
  },
  plugins:[
    new VueLoaderPlugin(),
    new webpack.BannerPlugin('hello 请给我版权费！！！'),
    new HtmlWebpackPlugin({
      template : 'index.html'
    }),
    // new UglifyjsWebpackPlugin(),
  ],
  // devServer:{
  //   contentBase:'./dist',
  //   inline:true,
  // }
}
```

**dev.config.js**

```javascript
const webpackMerge = require('webpack-merge')
const baseConfig = require('./base.config')

module.exports = webpackMerge.merge(baseConfig,{
  devServer:{
    contentBase:'./dist',
    inline:true,
  }
})
```

**prod.config.js**

```javascript
const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin')

const webpackMerge = require('webpack-merge')
const baseConfig = require('./base.config')
module.exports = webpackMerge.merge(
  baseConfig, {
    plugins:[
      new UglifyjsWebpackPlugin(),
    ],
  }
)

```

### 安装webpack-merge

命令；`npm install webpack-merge --save-dev  `

### 配置命令

使用 --config 指定配置文件路径

```json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --config ./build/prod.config.js",
    "server": "webpack-dev-server --open --config ./build/dev.config.js"
  },
```

# CLI脚手架

## 什么是脚手架

CLI是Command-Lone-Interface，翻译为命令行界面，俗称脚手架

Vue CLI是一个官方发布的vue.js项目脚手架

使用Vue-cli可以快速的搭建Vue开发环境以及应对webpack配置

## Vue CLI

使用Vue.js开发大型项目时，我们需要考虑代码的结构，项目的结构和部署，热加载，代码单元测试等等事情，手动完成效率较低，所以我们们会使用脚手架工具来帮助我们完成这些事情

使用Vue-Cli的前提：依赖node，全局webpack

Vue-Cli使用了webpack，模板，对所有的资源会进行压缩，优化等操作，它在开发过程中提供了一套完整的功能，能够是我们的开发过程变得高效

## 安装Vue-cli脚手架 3

官网：[https://cli.vuejs.org/zh](https://cli.vuejs.org/zh)

全局安装Vue-cli@3：`npm install @vue/cli@3.12.0 -g`

使用命令`vue --version`查看安装的版本

在 cli 3 中拉取 cli 2 的模板：`npm install -g @vue/cli-init`

安装成功后可以用脚手架3和脚手架2

## 初始化项目

**注意：项目名称不能用中文**

### 脚手架2 ： `vue init webpack 项目名称`

**下载完模板后，需要进行项目的配置：**

|                           配置名称                           |                             作用                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                    Project name (VueCli2)                    |        项目名称（括号中的是默认值，不能包含大写字母）        |
|            Project description (A Vue.js project)            |                             描述                             |
|           Author (Allure <zym2894290453@163.com>)            |                             作者                             |
|                          Vue build                           |               选择vue的版本，compiler或者only                |
|                  Install vue-router? (Y/n)                   |                  是否安装vue-router（路由）                  |
|             Use ESLint to lint your code? (Y/n)              | 是否对ES（javascript）代码作限制，就是保证js代码规范，不然编译器会报错 |
|            Pick an ESLint preset (Use arrow keys)            |                         选择ES的规范                         |
|                   Set up unit tests (Y/n)                    |                       是否集成单元测试                       |
|            Setup e2e tests with Nightwatch? (Y/n)            |                      是否集成端到端测试                      |
| Should we run `npm install` for you after the project has been created? (recommended) (Use arrow keys) |                  准备用npm还是yarn管理项目                   |

**示例：**

![image-20210730161646636](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210730161646636.png)

### 脚手架3 ： `vue creat 项目名称`

**下载完模板后，需要进行项目的配置：**

|                           配置名称                           |                             作用                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| Your connection to the default npm registry seems to be slow.<br/>   Use https://registry.npm.taobao.org for faster installation? (Y/n) |                是否使用淘宝的npm镜像进行下载                 |
|                     Please pick a preset                     |                 选择一个配置，默认或手动选择                 |
| Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection) |     选择需要的配置，空格是选择和取消，a是全选，i反向选择     |
| Choose a version of Vue.js that you want to start the project with (Use arrow keys) |                        选择vue的版本                         |
| Where do you prefer placing config for Babel, ESLint, etc.? (Use arrow keys) | 将某些配置（Babel, ESLint, ）放在独立文件中或者是package.json中 |
|       Save this as a preset for future projects? (y/N)       | 你要把刚才的选择保存吗？保存后下次可以在Please pick a preset中选择，在C:\Users\86159\.vuerc中可以删除之前保存的配置 |

**示例：**

![image-20210731114328229](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210731114328229.png)

### cli3与cli2的区别

vue-cli3基于webpack4打造，vue-cli2是webpack3

vue-cli3的设计原则是 0 配置，溢出根目录下的build和config配置

vue-cli3提供了vue ui命令，提供了可视化配置

移除了static文件夹，新增public文件夹，并将index.html文件移动到public文件夹中

## cli2的目录结构

|     文件      |              作用              |
| :-----------: | :----------------------------: |
|     buoid     |        webpack相关配置         |
|    config     |        webpack相关配置         |
| node_modules  |          node相关模块          |
|      src      |              源码              |
|    static     |            静态资源            |
|   .babelrc    |         ES代码相关配置         |
| .editorconfig |        项目文本相关配置        |
| .eslintignore | 用于设置忽略哪些文件的语法检查 |
| .eslintrc.js  |         设置eslint规则         |
|  .gitignore   |    git仓库忽略的文件夹配置     |
| .postcssrc.js |        css相关转化配置         |
|  index.html   |          首页入口文件          |
| package.json  |          配置项目文件          |
|   README.md   |          项目说明文档          |

![image-20210730175904559](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210730175904559.png)

## Runtime-compiler和Runtime-only

### Vue程序的运行过程

![image-20210731103458940](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210731103458940.png)

### runtime-compiler

template ——> ast ——> render ——> vdom ——> UI

```javascript
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
})
```

### runtime-only：

render ——> vdom ——> UI

性能更高，代码量更少

```javascript
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  render: h => h(App)
    /*
    	render:h=>h()其实是一个函数
    	render:function (createElement) {
    		1、普通用法
    		return createElement('标签'，{属性}，[‘值’])
    		例如：return createElement('h2'，{id:'test'}，[‘哈哈哈’])；相当于<h2 id="test">哈哈哈</h2>替换 #app
    		2、传入组件
    		return createElement(App)
    		相当于 components: { App },template: '<App/>' 两步
    	}
    */
})
```

<font color='red'>runtime-only在导入App.vue时，webpack的vue-template-compiler就已经将App.vue里面的template编译成了render函数</font>

## Vue-cli3配置文件的查看和修改

### 配置文件在哪里

- UI方面的配置

  > 启动配置服务器命令：vue ui

- 配置文件

  > node_modules/@vue/cli-service/lib

### 修改配置

在项目根目录创建文件：vue.config.js

```javascript
module.exports = {}
```

## 打包

vue.config.js中添加配置

```javascript
module.exports = {
  publicPath : './'
}
```

index.js中注释history模式

```javascript
const router = new VueRouter({
  // mode: 'history',
  routes
})
```

执行命令 npm run build 完成打包

将dist目录部署到服务器

# vue-router

Vue-router是Vue官方的路由插件，它和Vue.js是深度集成的，适合构建单页面的应用

官方网站[https://router.vuejs.org/zh/](https://router.vuejs.org/zh/)

vue-router是基于路由和组件的，路由用于设定访问路径，将路径和组件映射起来

在vue-router的单页面应用中，页面路径的改变就是组件的切换

## 路由

网络工程中的一个术语

路由就是通过互联的网络把信息从原地址传到目的地址的活动

路由表：路由表的本质上就是一个映射表，决定了数据包的指向

## 前端渲染和后端渲染

**前端渲染：**

浏览器中显示的大部分网页中的大部分类容都是由前端js代码在浏览器中执行，渲染出来的        

**后端渲染：**

就是jsp/php之类的，后端服务器直接将页面渲染，把数据放到正确的位置，然后将html和css代码返回浏览器

## 前端路由和后端路由

**后端路由：**浏览器在地址栏中切换不同的url时，每次都向后台服务器发出请求，服务器响应请求，在后台拼接html文件传给前端显示, 返回不同的页面。（输入不同的url由后端决定显示什么内容）

**前端路由：**当地址栏的url改变，不会刷新页面，而是使用js代码架构需要的内容显示出来。（输入不同的url，显示的内容由前端决定）

## URL的hash

url的hash也就是锚点，本质上是改变window.location的href属性

我们可以直接赋值location.hash来改变href，但是页面不发生刷新

HTML5里面的history的pushState进行修改，也可以不刷新页面

history.back() 可以退回一个页面

history.go( number ) 可以指定显示到当前页面的前或后几个页面

history.back()等于hiytory.go(-1)

history.forward() 相当于history.go(1)

## vue-router的使用

1. 安装：`npm install vue-router --save`

2. 导入路由对象，并且调用Vue.use

3. 创建路由实例，并且传入路由映射配置

4. 创建路由组件

   Home.vue

   ```vue
   <template>
     <div>
       <h2>哈喽啊，我是Home</h2>
       <p>hhhhhhhhhhhhhhhhhhhhhhh</p>
     </div>
   </template>
   <script>
   export default {
     name : 'Home'
   }
   </script>
   ```

   About.vue

   ```vue
   <template>
     <div>
       <h2>我是About</h2>
       <p>jjjjjjjjjjjjjjjjjjjj</p>
     </div>
   </template>
   <script>
   export default {
     name : 'About'
   }
   </script>
   ```

5. 在Vue实例中挂载创建的路由

   配置路由映射：组件和路径的映射关系

   1. 

   ```javascript
   //配置路由相关的信息
   import VueRouter from 'vue-router'
   import Vue from 'vue'
   
   //导入模块
   import Home from '../views/Home.vue'
   import About from '../views/About.vue'
   
   //1.通过Vue.use（插件），安装插件
   Vue.use(VueRouter)
   
   //2.创建路由对象
   const routes = [
     //配置映射关系  
     { path: '/home', component: Home },
     { path: '/about', component: About }
   ]
   const router = new VueRouter({
     //配置路由和组件之间的对应关系
     routes
   })
   
   //3.将router对象挂载到Vue实例中
   export default router
   
   ```

6. 使用路由：通过`<router-link>`和`<router-view>`

   App.vue

   ```vue
   <template>
     <div>
       <router-link to="/home">Home</router-link>
       |
       <router-link to="/about">About</router-link>
       <br>
       <!--<router-view>所在的地方就是路由组件显示的地方-->
       <router-view></router-view>
     </div>
   </template>
   
   <script>
   export default {
     name : 'App'
   }
   </script>
   ```
   
   

<font color="red">**注意：$router 和 $route是两个不同的东西，router就是vue-router对象，route是当前活跃的路由对象**</font>

## 路由的默认设置

实现：

默认情况下，进入网站首页，我们希望`<router-vue>`直接显示首页的内容

在index.js中添加配置：将默认的地址重定向到 /home

```javascript
/创建路由对象
const routes = [
  //配置映射关系  
  { path: '/home', component: Home },
  { path: '/about', component: About },
  //将默认的地址重定向到 /home
  { path: '/', redirect: '/home' }
]
```

## 修改为HTML5的history模式

在index.js中修改：

```javascript
const router = new VueRouter({
  //配置路由和组件之间的对应关系
  routes,
  //修改为history模式
  mode:'history'
})
```

## router-link

|     属性     |                             作用                             |
| :----------: | :----------------------------------------------------------: |
|      to      |                         指定跳转路径                         |
|     tag      |          默认显示<a.标签，tag用于将其渲染成其他标签          |
|   replace    |          使用replace模式，浏览器不能返回之前的页面           |
| active-class | 指定点击后的class属性名称，可以在index.js中通过LinkActiveClass修改 |

```html
<template>
  <div id="a">
    <router-link to="/home" tag="button" replace active-class="active">Home</router-link>
    |
    <router-link to="/about">About</router-link>
    <br />
    <router-view></router-view>
  </div>
</template>
```

index.js中修改LinkActiveClass

```javascript
const router = new VueRouter({
  //配置路由和组件之间的对应关系
  routes,
  mode:'history',
  linkActiveClass:'active'
})
```

## 通过代码跳转路由

```javascript
 methods: {
    homeClick(){
      //可以退回
      this.$router.push('/home')
    },
    aboutClick(){
      //不能退回
      this.$router.replace('/about')
    }
  }
```

## 动态路由

某些情况下，一个页面的path路径可能是不确定的，比如我们进入用户页面时，希望是如下路径：

/user/tom 或 /user/lisi

步骤：

配置路由：传入的数据名称叫做 userId

```javascript
  { path: '/user/:userId', component: User }
```

使用动态路由：

```html
<template>
  <div id="a">
    <router-link to="/home" replace active-class="active">Home</router-link>
    |
    <router-link to="/about">About</router-link>
    |
    //获取data中的name添加进路由
    <router-link :to="'/user/'+name">User</router-link>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: "App",
  data(){
    return {
      name : 'lisi'
    }
  }
};
</script>
```

获取路由中的数据 userId：

```html
<template>
  <div>
    <h2>我是用户界面</h2>
    <p>我是用户信息，，，，，，，，，，，，，，，</p>
    <p>我是:  {{userId}}</p>
  </div>
</template>
<script>
export default {
  name : 'User',
  computed: {
    userId(){
      //通过$route，获取数据userId
      return this.$route.params.userId
    }
  }
}
</script>
```

## 路由的懒加载

**官方解释：**

当打包构建时，JavaScript包会变得非常大，影响也面加载

我们把能把不同的路由对应的代码块分割成不同的代码块，然后当路由被访问时才加载对应的组件，这样效率就提高了

**使用方法：**在映射关系中导入组件

 ```javascript
 //推荐先使用变量保存然后添加进映射关系
 canst Home = () => import('../views/Home')
 canst About = () => import('../views/About') 
 创建路由对象
 const routes = [
   //配置映射关系  
   { path: '/', redirect: '/home' },
   { path: '/home', component: Home },
   { path: '/about', component: About},
   { path: '/user/:userId', component: () => import('../views/User') }
 ]
 ```

## 嵌套路由

在home页面中，我们希望能够 /home/news 和 /home/message 可以展示不同的内容

一个路径映射一个组件，这两个路径会分别映射两个组件

![image-20210801174759129](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210801174759129.png)

### 实现嵌套循环的步骤：

1. 创建对应的子组件

   HomeNews.vue

   ```vue
   <template>
     <div>
       <h2>新闻</h2>
       <ul>
         <li>aaaaaaaaaa</li>
         <li>aaaaaaaaaa</li>
         <li>aaaaaaaaaa</li>
         <li>aaaaaaaaaa</li>
       </ul>
     </div>
   </template>
   
   <script>
   export default {
     name : 'HomeNews'
   }
   </script>
   ```

   HomeMessage.vue

   ```vue
   <template>
     <div>
       <h2>message</h2>
       <p>hahahahahahahhaahhahahahahahah</p>
     </div>
   </template>
   
   <script>
   export default {
   
   }
   </script>
   ```

2. 在映射关系中配置对应的路由

   index.js

   ```javascript
   //创建路由对象
   const routes = [
     //配置映射关系  
     { path: '/', redirect: '/home' },
     //配置home的子路由
     {
       path: '/home',
       component: () => import('../views/Home.vue'),
       children: [
         { path: '', redirect: 'news' },
         { path: 'news', component: () => import('../views/HomeNews.vue') },
         { path: 'message', component: () => import('../views/HomeMessage.vue') }
       ]
     },
     { path: '/about', component: () => import('../views/About.vue') },
     { path: '/user/:userId', component: () => import('../views/User.vue') }
   ]
   
   const router = new VueRouter({
     //配置路由和组件之间的对应关系
     routes,
     mode: 'history'
   })
   ```

3. 在组件内部使用`<router-view>`标签

   Home.vue

   ```vue
   <template>
     <div>
       <h2>哈喽啊，我是Home</h2>
       <p>hhhhhhhhhhhhhhhhhhhhhhh</p>
       <!--路径写全-->
       <router-link to="/home/news">HomeNews</router-link>|
       <router-link to="/home/message">HomeMessage</router-link>
       <router-view></router-view>
     </div>
   </template>
   <script>
   export default {
     name : 'Home'
   }
   </script>
   ```


## 传递参数

传递参数的主要方式有：params（动态路由）和query，数据量大都用query，只有一个数据可以使用params

params前面有，这里讲query

1. 创建组件页面：

   ```vue
   <template>
     <div>
       <h2>我是档案</h2>
       <p>
         用户：{{user.name}}
         <br>
         <!--获取参数-->
         年龄：{{$route.query.age}}
       </p>
     </div>
   </template>
   
   <script>
   export default {
     name : 'porfile',
     computed: {
       user(){
         //获取参数，query中的所有数据
         return this.$route.query
       }
     }
   }
   </script>
   ```

2. 配置路由，与普通的路由配置一样

   ```javascript
   { path: '/profile', component: () => import('../views/Profile.vue') }
   ```

3. 在App.vue中使用组件

   ```vue
   <!--方式一：在router-link中传递参数-->
   <router-link :to="{path:'/profile',query:{name:'yyb',age:18}}">档案</router-link>
   
   
   <!--方式二：使用其他标签，如button，传递参数-->
   <button @click="profileClick()">档案</button>
   <script>
   export default {
     name: "App",
     methods: {
       profileClick(){
   	  //跳转页面并且传递参数
         this.$router.push({
           path : '/profile',
           query:{
             name : 'pink',
             age : 99
           }
         })
       }
     }
   };
   </script>
   ```


## 导航守卫

在一个单页面应用中，然后改变页面的标题呢？

- 网页的标题通过`<title>`来显示，但SPA页面只有一个固定的html，所以切换不同的页面时，标题并不会改变
- 但是，我们可以通过JavaScript来修改title的内容，` document.title = '新的标题'`
- 在Vue项目中，我们就可以通过全局导航守卫来修改

### 前置全局守卫，在跳转前回调

使用步骤：

1. 在配置routes路由的时候，在meta函数中指定当前路由的title

   ```javascript
   //创建路由对象
   const routes = [
     //配置映射关系  
     { path: '/', redirect: '/home' },
     {
       path: '/home',
       component: () => import('../views/Home.vue'),
       children: [
         { path: '', redirect: 'news' },
         { path: 'news', component: () => import('../views/HomeNews.vue')},
         { path: 'message', component: () => import('../views/HomeMessage.vue')}
       ], meta: { title: '首页' }
     },
     { path: '/about', component: () => import('../views/About.vue'), meta: { title: '关于' } },
     { path: '/user/:userId', component: () => import('../views/User.vue'), meta: { title: '用户' } },
     { path: '/profile', component: () => import('../views/Profile.vue'), meta: { title: '档案' } }
   ]
   ```

2. 配置全局导航守卫

   ```javascript
   //使用全局导航守卫
   router.beforeEach((to, from, next) => {
     //从from页面跳转到to页面时,使用to的title
     //将页面的title修改为to里面的title，有嵌套路由时，需要获取到第一个metched里面的meta，否则找不到to页面的title
     document.title = to.matched[0].meta.title
     next()
   })
   ```

meta：原数据（描述数据的数据）

### 后置全局守卫，在跳转后回调

使用方法：

```javascript
router.afterEach( (to,from) => {
  console.log('------------');
})
```

### 路由独享守卫

### 组件内守卫

https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#%E5%AF%BC%E8%88%AA%E5%AE%88%E5%8D%AB

**等等其他守卫可以进入官网查看使用方式**

## keep-alive

keep-alive是Vue的一个内置组件，可以使被包含的组件保留状态，或避免重新渲染

router-view也是一个组件，如果直接包裹在keep-alive里面，所有的路径匹配到的视图组件都会被缓存

需要使用生命周期函数activated和组件守卫beforeRouteLeave

使用方法：

```vue
  <!--使用 <keep-alive>包含组件-->
	<keep-alive>
      <router-view></router-view>
    </keep-alive>
```

在需要保留状态的组件中设置path数据，用于保留状态

```vue
<script>
export default {
  name : 'Home',
  data(){
    return {
      //保留状态
      path : '/home/news'
    }
  }
  ,
  //声明周期函数，用于在组件活跃时显示之前的状态
  activated () {
    this.$router.push(this.path)
  }
  ,
  //组件内守卫，用于在离开时记录状态
  beforeRouteLeave(to, from, next) {
    this.path = this.$route.path
    next()
  }
}
</script>
```

### 属性

incloud：字符串或正则表达式，只有匹配的组件会被缓存

exclude：字符串或正则表达式，任何匹配的组件都不会保存

注意：属性值不要随便加空格

```vue
<script>
export default {
  name : 'porfile',
}
</script>
```

```vue
<keep-alive exclude="porfile,User">
	<router-view></router-view>
</keep-alive>
```

# Promise

promise是ES6中非常好用的新特性，是**异步编程**的一种解决方案

一般情况下，有异步操作就能使用Promise对异步操作进行封装

## Promise的基本使用

```javascript
    /*
    Promise函数：1、保存一些状态信息，2、执行传入的函数 
    new Peomise((resolve，reject) => {执行传入的函数})
    执行传入的函数时，会传入两个参数，resolve，reject
    链式编程
    */
    new Promise((resolve,reject) => {
      //异步请求获取数据
      setTimeout(() => {
        const data = 'hello'
        //请求成功，调用resolve函数处理数据
        resolve(data)
        //请求失败，调用reject函数
        reject('error message')
      },1000)
    }).then((data) => {
      //处理数据的代码
      console.log(data);
    }).catch((error) => {
      console.log(error);
    })
```

## 第二种写法

```javascript
new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve('hello world')
          reject('hello vue')
        }, 1000)
        //在then中写两个函数，请求成功执行resolve就调用第一个函数，请求失败执行reject就执行第二个函数
      }).then(
        y => console.log(y) , 
        n => console.log(n) 
      )
```

## 使用Promise的三种状态

> pending：等待状态，比如我们正在进行网络请求
>
> fulfill：满足状态，请求成功，我们回调了resolve时，就处于满足状态
>
> reject：拒绝状态，请求失败，我们回调了reject函数时，就处于该状态，并且会回调catch()

## 链式请求

```javascript
    new Promise((resolve, reject) => {
      // 第一次请求
      setTimeout(() => {
        resolve()
      }, 1000)
      // 第一次处理结果
    }).then(() => {
      console.log('hello world');
      return new Promise((resolve,reject) => {
        // 第二次请求
        setTimeout(() => {
          resolve()
        }, 1000)
        // 第二次处理结果
      }).then(() => {
        console.log('hello vue');
        return new Promise((resolve,reject) => {
          // 第三次请求
          setTimeout(() => {
            resolve()
          },1000)
          // 第三次处理结果
        }).then(() => {
          console.log('----------------');
        })
      })
    })
```

## Promise.all

例如需求本身依赖两个请求，需要两个请求都拿到数据才进行之后的步骤

```javascript
      Promise.all([
        new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve('hello world')
          }, 1000)
        }),
        new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve('hello vue')
          }, 1000)
        })
      //当两个请求都成功，就会对应then中的函数，results是两个resolve的数据集合
      ]).then(results => {
        console.log(results);
      })
```

# Vuex

## 官方解释

Vuex是一个专门为Vue.js应用程序开发的状态管理模式

它采用**集中式的存储管理**应用的所有组件的状态，并已相应的规则保证状态以种了预测的方式发生变化

Vuex也集成到Vue的官方调试工具devtools extension，提供了诸如零配置的time-travel调试，状态快照导入导出等高级的调试功能

**主要是管理需要在多个组件之间共享的状态**

## Vue单界面状态自管理应用包含三个部分：

- **state**：即状态，我们可以把它当中data中的属性。（驱动应用的数据源）
- **view**：即视图层（我们的template模板），可以针对State的变化，显示不同的信息（以声明方式将 state 映射到视图）
- **actions**：即交互行为，这里的Actions主要是指用户的各种操作：例如鼠标点击，键盘输入等，都会导致状态(State)的改变.（响应在 view 上的用户输入导致的状态变化。）

![image-20210804112446042](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210804112446042.png)

## 多界面状态管理图例

![image-20210804115655392](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210804115655392.png)

## Vuex的基本使用

安装：`npm install vuex --save`

配置：

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

//安装插件
Vue.use(Vuex)

//插件对象
const store = new Vuex.Store({
  //保存状态
  state: {
    count :100
  },
  mutations: {
  },
  actions: {
  },
  modules: {
  }
})

export default store
```

挂载：

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'
//引用Vuex
import store from './store'

Vue.config.productionTip = false

new Vue({
  router,
  //挂载
  store,
  render: h => h(App)
}).$mount('#app')
```

使用：在所有组件中，都可以通过$store获取vuex中的数据

```vue
<template>
  <div>
    count是：{{$store.state.count}}
  </div>
</template>
```

## 使用官方推荐的方法

需要修改数据的时候只在mutations中进行

配置vuex对象

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

//安装插件
Vue.use(Vuex)

//插件对象
const store = new Vuex.Store({
  //保存状态
  state: {
    count :100
  },
  //操作state中数据的方法
  mutations: {
    increment(state){
      state.count ++
    },
    decrement(state){
      state.count --
    }
  },
  // 异步操作
  actions: {
  },
  modules: {
  }
})

export default store
```

使用Vuex

```javascript
<template>
  <div id="app">
   {{message}}
   <br>
   //获取数据
   {{$store.state.count}}
   <br>
   <button @click="add()">+</button>
   <button @click="sub()">-</button>
   <hello ></hello>
  </div>
</template>
<script>
import hello from './components/hello.vue'
export default {
  name : 'App',
  data () {
    return {
      message : '我是App组件',
    }
  },
  components : {
      hello
  },
  methods: {
    add(){
      //使用mutations中的方法修改数据
      this.$store.commit('increment')
    },
    sub(){
      this.$store.commit('decrement')
    }
  }
}
</script>
```

## 使用devtools浏览器扩展程序

![image-20210804122003266](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210804122003266.png)

**可以对mutations中修改的数据进行查看**

![image-20210804122139759](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210804122139759.png)

## Vuex的核心概念

Store目录结构：

> store
>
> > index.js                       导入核心，组装模块导出store
> >
> > actions.js                    store的action
> >
> > mutations.js               store的mutations
> >
> > modules                     module
> >
> > > moduleA                 模块A
> > >
> > > moduleB                 模块B

### State：存放状态相关信息

 单一状态树（SIngle Source of Truth）也可以翻译称为单一数据源

在一个项目中只建立一个Store，对应一个State

### Getters：相当于计算属性

##### 设置getters：

```javascript
getters: {
    powerCount(state){
      return state.count * state.count
    }
  }
```

##### 使用getters：

```vue
<h2>{{$store.getters.powerCount}}</h2>
```

##### 在getter中使用getter

```javascript
getters: {
    powerCount1(state){
      return state.count * state.count
    },
    powerCount2(state,getters){
      return getters.powerCount1 + 1
    }
  }
```

##### 为getter传递参数

```javascript
getters: {
    powerCount(state){
      return function( number ){
          return number
      }
    }
  }
```

##### 使用

```html
<h2>{{$store.getters.powerCount( 123 )}}</h2>
```

### Mutation：Vuex的store状态更新的唯一方式`提交mutation`

mutation里面的代码必须是同步的

mutation主要包括两个部分：

- 字符串的时间类型（type）
- 一个回调函数（handler），该回调函数的第一个参数就是state

更新数据时我们有时候希望传入一些参数

- 参数被称为mutation的荷载（Payload）

##### mutation的定义方式：

```javascript
mutations: {
    increment(state){
      state.count ++
    }
}
```

##### 使用方式：

```javascript
//方法一
methods: {
    add(){
      //使用mutations中的方法修改数据
      this.$store.commit('increment')
    }
}

//方法二:取值时时一个对象
methods: {
    add(){
      //使用mutations中的方法修改数据
      this.$store.commit({
          type:'increment',
          count
      })
    }
}
```

##### 传入参数：有多个参数一般传入一个对象

```javascript
//定义
mutations: {
    increment(state,number){
      state.count += number
    }
}

//使用
methods: {
    add(number){
      //使用mutations中的方法修改数据
      this.$store.commit('increment',number)
    }
}
```

##### mutation的响应规则：

 Vuex的store中的state是响应式的，当state中的数发生改变时，Vue组件会自动更新

这就需要我们遵守一些Vuex的规则：

> 提前在store中初始化号所需要的属性
>
> 当给state对象添加新的属性时，使用下面的方式
>
> - 方式一：使用`Vue.set(obj,'newProp',123)`
> - 用新对象给就旧对象赋值
>
> 删除属性
>
> - 使用`Vue.delete(obj,'属性名')`

##### mutation的类型常量：

定义 types.js：

```javascript
export canst Increment = 'increment'
```

使用 ： 

```javascript
import * as types from './types.js'
mutations: {
    [types.Increment](state,number){
      state.count += number
    }
}
```

```javascript
import {Increment} from './types.js'
methods: {
    add(number){
      //使用mutations中的方法修改数据
      this.$store.commit(Increment,number)
    }
}
```

### Action

是用来代替mutation进行一步操作的,在Action中只是进行一步操作，修改数据还得是使用mutation

##### 使用：

在actions里面定义方法

```javascript
  // 异步操作
  actions: {
    // context：上下文
    aupdateInfo(context){
      setTimeout(() => {
        context.commit('updateInfo')
      },1000)
    }
  },
```

使用active

```javascript
  methods: {
    butClick(){
      this.$store.dispatch('aupdateInfo')
    }
  }
```

##### 传递参数

```javascript
//方式一  
actions: {
    // context：上下文
    //payload ： 参数
    aupdateInfo(context,payload){
      setTimeout(() => {
        context.commit('updateInfo')
        console.log(payload);
      },1000)
    }
  }

//方式二  
actions: {
    aupdateInfo(context,payload){
      return new Promise((resolve,reject) => {
        setTimeout(() => {
        	context.commit('updateInfo')
            resolve()
      	},1000)
      })
    }
  },
```

```javascript
//方式一  
methods: {
    butClick(){
      this.$store.dispatch('aupdateInfo','hello')
    }
  }

//方式二
methods: {
    butClick(){
      this.$store.dispatch('aupdateInfo',{
        message : hello,
        success : () => {
       	  console.log('完成')
        }
      })
    }
  }

//方式三
methods: {
    butClick(){
      this.$store.dispatch('aupdateInfo','hello').then(res => {
      	console.log('里面完成了')
      })
    }
  }
```

### Module	

module可以分模块，在模块里面有可以定义 state，getters，mutation，action，module

##### 定义module：

```javascript
 modules: {
    moduleA : {
      state: {
        name : 'yyb'
      },getters: {
        fullname(state){
          return state.name+1111111
        }
      },
      actions: {
        // context是本模块的上下文
        aUpdateName(context){
          //context.roootGeeter:拿到根Store的getter
          //rootState ： 拿到根Store的state
          console.log(context.roootGeeter)
          context.commit('updateName','aaa')
        }
      },
      mutations: {
        updateName(state,payload){
          state.name = payload
        }
      },
      modules: {
        
      }
    },
    moduleB:{

    }
  }
```

##### 使用模块中的state：vuex会将modules编译到state里面，所以文本要拿到state，再从里面拿到moduleA里面的name

```vue
<h2>{{$store.state.moduleA.name}}</h2>
```

<font color='red'>**使用module里面的其他东西与Store里面的用法一样，只是定义的时候不能和Store里面的重名**</font>

# axios

 ## 特点：

在浏览器中发送XMLHttpRequests请求

在node.js中发送http请求

支持Promise API

拦截请求和响应

转换请请求和响应数据

## 请求方式

|        axiso(config)         |     axiso.request(config)      |
| :--------------------------: | :----------------------------: |
|   axiso.get(url[,config])    |   axiso.delete(url[,config])   |
|   axiso.head(url[,config])   | axiso.post(url[,data,config])  |
| axiso.put(url[,data,config]) | axiso.patch(url[,data,config]) |

## 使用方法

安装： `npm install axios --save`

### 基本使用

```javascript
//导入模块
import axios from 'axios'

//发起请求
axios({
  url: 'http://123.207.32.32:8000/home/multidata',
//请求成功后调用then里面的函数，res是返回的数据
}).then((res) => {
  console.log(res);
})
```

### get请求拼接参数[^params]

```javascript
axios({
  url:'http://123.207.32.32:8000/home/data',
  //get参数拼接，参数名 ：参数值
  params:{
    type : 'pop',
    page : 1
  }
}).then((res) => {
  console.log(res);
})
```

## 并发请求，相当于Promise.all

```javascript
// 并发请求
axios.all([
  axios({
    url:'http://123.207.32.32:8000/home/multidata'
  }),
  axios({
    url:'http://123.207.32.32:8000/home/data',
    params: {
      type : 'pop',
      page : 1
    }
  })
]).then(results => {
  console.log(results);
  console.log(results[0]);
  console.log(results[1]);
})


//使用axios.spread分别得到第一个请求的结果res1，和第二个请求的结果res2
.then(axios.spread((res1, res2) => {
  console.log(res1);
  console.log(res2);
}))
```

## 利用axios全局配置，对固定的参数配置进行抽取

```javascript
axios.defaults.baseURL = 'http://123.207.32.32:8000'
axios.defaults.timeout = 5000

  axios({
    url:'/home/multidata'
  }).then(res => {
  console.log(res);
}))
```

### 常见的配置选项：

|               配置               | 作用                                                         |
| :------------------------------: | :----------------------------------------------------------- |
|               url                | （必写）请求地址                                             |
|              method              | 请求方法，默认是get                                          |
|             baseURL              | （常用）baseURL会添加到url前（url是绝对地址除外）。          |
| transformRequest [:function(){}] | transformRequest选项允许我们在请求发送到服务器之前对请求的数据做出一些改动  , 该选项只适用于以下请求方式：put/post/patch |
| transformResponse[:function(){}] | transformResponse选项允许我们在数据传送到then/catch方法之前对数据进行改动 |
|             headers              | （常用，如设置请求头json类型）  自定义请求头信息             |
|              params              | （常用），**只有get请求设置params**，其他请求需设置params,即只有get的请求参数位于url后，其他请求参数都在请求体中 |
|              params              | 选项是要随请求一起发送的请求参数----一般链接在URL后面        |
|               data               | （常用）  data选项是作为一个请求体而需要被发送的数据，该选项只适用于方法：put/post/patch，  在浏览器上data只能是FormData,  File, Blob格式 |
|             timeout              | （常用） 超时时间                                            |
|         withCredentials          | 选项表明了是否是跨域请求、默认是default                      |
|         onUploadProgress         | 上传进度事件                                                 |
|        onDownloadProgress        | 下载进度的事件                                               |
|         maxContentLength         | 相应内容的最大值                                             |

## axios的实例

针对不同的实例可以配置不同的固定参数

```javascript
//创建axios实例
const axios1 = axios.create({
  baseURL : 'http://123.207.32.32:8000',
  timeout : 5000
})
//使用实例
axios1({
  url: '/home/multidata'
}).then( res => {
  console.log(res);
})
```

## 对网络模块进行封装

封装：request.js

```javascript
import axios from 'axios'

export function instance(config) {
  // 创建实例
  const axios_01 = axios.create({
    baseURL: 'http://123.207.32.32:8000',
    timeout: 5000
  })
  return axios_01(config)
}
```

使用：

```javascript
//导入封装的模块
import { instance} from '@/network/request.js'

//使用axios的实例instance
instance({
  url: '/home/multidata'
}).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})
```

## 拦截器

定义：

```javascript
import axios from 'axios'

export function instance(config) {
  // 创建实例
  const axios_01 = axios.create({
    baseURL: 'http://123.207.32.32:8000',
    timeout: 5000
  })

  //拦截器
  //为axios_01实例拦截请求
  axios_01.interceptors.request.use(
    // 请求的config信息
    config => {
      console.log(config);
      // 可以添加配置信息，
      // 返回请求信息,才能进行下一步操作
      return config
    },
    //请求失败
    err => {
      console.log(err);
    }
  );
  //为axios_01实例拦截响应
  axios_01.interceptors.response.use(
    //响应成功
    res => {
      console.log(res);
      return res.data;
    },
    //响应失败
    err => {
      console.log(err);
    }
  );

  return axios_01(config)
}
```

使用：在使用对应的实例时会自动使用拦截器

```javascript
instance({
  url: '/home/multidata'
}).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})
```

# 注意

## 当遇到计算属性报错，程序可以正常运行时：

![image-20210803145052952](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210803145052952.png)

是因为在computed 里面定义的属性 只有一个return返回。因为在其他地方进行了**赋值**操作，导致提示此错误

只需要在报错的计算属性添加其set方法

```javascript
computed: {
    isActive:{
      get(){
        return this.$route.path.indexOf(this.paths)!==-1
      },
      set(){
      }
  },
```

## 报错`GET http://192.168.31.58:8080/sockjs-node/info?t=1627975254600 net::ERR_CONNECTION_TIMED_OUT`

![image-20210803152654545](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210803152654545.png)

网上找到的原因说是：sockjs-node是一个JavaScript库，提供跨浏览器JavaScript的API，创建了一个低延迟、全双工的浏览器和web服务器之间通信通道。在项目运行以后，network会一直调用这个接口。如果没有使用，那么就一直会报这个异常。

解决方法：

1）找到/node_modules/sockjs-client/dist/sockjs.js文件
2）在1606行左右，注释掉self.xhr.send(payload);这一行，然后就可以解决了

![image-20210803152956047](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210803152956047.png)

## axios跨域请求报错

![image-20210806141005728](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210806141005728.png)

需要在后台处理跨域问题，以springmvc为例：

使用**@CrossOrigin**注解处理跨域请求，该注解可以定义在controller类上，也可以定义在方法上

可以在里面指定允许访问的域名，也可以不指定，默认所有域名均可访问

示例：

```java
@Controller
//@CrossOrigin
public class indexController {

    @RequestMapping("index")
    @ResponseBody
    @CrossOrigin
    public Object index(){
        return "hello";
    }
}
```

![image-20210825161835032](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210825161835032.png)

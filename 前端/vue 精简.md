# 插值语法

`{{js 表达式}}` 可以直接获取 vm 中的属性 

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

# 指令语法

## 基本指令

| **指令** |                           **作用**                           |
| :------: | :----------------------------------------------------------: |
|  v-once  |              禁止其显示的值根据数据的变化而变化              |
|  v-for   |       循环遍历数组`movies`中的数据，放入新的`\<li\>`中       |
|  v-html  |                将数据转换成html格式，进行显示                |
|  v-text  | 显示文本（一般不使用，直接使用mustache比较方便），会将标签本来要显示的数据覆盖掉 |
|  v-pre   | 将数据标签中的数据原封不动的展示，例如{{message}}不显示hello，就显示大括号这样的格式 |
| v-cloak  | vue对象中没有此数据就不显示，有才显示；防止闪现, 与css 配合: [v-cloak] { display: none } |

```html
<div id='app' v-once>{{message}}</div>

<li v-for="item in movies">{{item}}</li>

<h2 v-html="url">{{url}}</h2>

<hz v-text="message"></hz>

<h2 v-pre>{{message}}</h2>


<style>
    [v-cloak]{
        display:none;
    }
</style>
<h2 v-cloak>{{message}}</h2>
```

## 数据绑定

| **指令** |                      **作用**                      |
| :------: | :------------------------------------------------: |
|  v-bind  |        数据单向绑定到 html 属性，语法糖 `:`        |
| v-model  | 表单绑定（双向），修改表单或者数据，对方会同时改变 |
|   v-on   |                事件处理，语法糖 `@`                |

class 与 style 的绑定也是通过 v-bind 实现，参考官方文档 [Class 与 Style 绑定](https://cn.vuejs.org/guide/essentials/class-and-style.html)

## 事件修饰符

| 修饰符                 | 作用                                                   | 使用方法                                                     |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| .stop                  | 调用event.stopPropagation()，阻止事件冒泡              | `<button @click.stop="btnClick2">按钮1</button>`             |
| .prevent               | 调用event.preventDefault()，阻止吗，默认事件           | `<a href="www.baidu.com" @click.prevent="btnClick3">百度</a>` |
| .{KeyCode \| KeyAlias} | 可以写按键的编码或者简写，当从特定按键触发时，调用函数 | `<input type="text" @keyup.enter="keyUp">`                   |
| .once                  | 只触发一次回调函数                                     | `<button @click.once="btnClick3">按钮3</button>`             |

# 计算属性 computed

要显示的数据不存在，要通过计算得来。

在 computed 对象中定义计算属性。

在页面中使用 {{方法名}} 来显示计算的结果

```html
<body>
<div id="app">
    {{money}}
</div>
<script>
  const vm = new Vue({
    data:{
      number:12,
    },
      computed:{
        money(){
            return "$" + this.number
        }
      }
  })
  vm.$mount("#app")
</script>
</body>
```



# 侦听属性 watch

通过通过 vm 对象的 $watch() 或 watch 配置来监视指定的属性

当属性变化时, 回调函数自动调用, 在函数内部进行计算

```html
<body>
<div id="app">
    {{number}}
</div>
<script>
  const vm = new Vue({
    data:{
      number:12,
    },
      watch:{
          number(newNumber,oldNumber){
              alert(oldNumber+"更改为"+newNumber)
          }
      }
  })
  vm.$mount("#app")

</script>
</body>
```

深度监视：

- Vue 中的 watch 默认不监测对象内部值的改变（一层）。
- 配置 deep:true 可以监测对象内部值改变（多层）。

备注：

- Vue 自身可以监测对象内部值的改变，但 Vue 提供的 watch 默认不可以！
- 使用 watch 时根据数据的具体结构，决定是否采用深度监视。

```javascript
  watch: {
    todoList: {
      deep:true,
      handler() {
        this.isOkTask = this.todoList.filter(todo => {
          if (todo.isOk === true) {
            return todo
          }
        })
      }

    }
  }
}
```

# 条件渲染

| **指令** |                           **作用**                           |
| :------: | :----------------------------------------------------------: |
|   v-if   | 根据表达式判断此标签是否显示，如果为 false，此标签中的内容不会创建 |
|  v-show  | 根据表达式判断此标签是否显示，如果为 false，此标签的 display 属性为 none |

比较：如果需要频繁切换 v-show 较好，当条件不成立时, v-if 的所有子节点不会解析(项目中使用)

# 过滤器 filter

通过管道符使用，对要显示的数据进行特定格式化后再显示，不改变原有数据，产生新的数据给模板使用

定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）。

语法：

1. 注册过滤器：Vue.filter(name,callback) 或 new Vue{filters:{}}
2. 使用过滤器：{{ xxx | 过滤器名}}  或  v-bind:属性 = "xxx | 过滤器名"

备注：

1. 过滤器也可以接收额外参数、多个过滤器也可以串联
2. 并没有改变原本的数据, 是产生新的对应的数据

```html
<div id="app">
    {{number | formatData}} <!--13-->
</div>
<script>
  const vm = new Vue({
    data:{
      number:12
    },
      filters:{
        formatData(number){
            return number + 1
        }
      }
  })
  vm.$mount("#app")
</script>
```

# 修改数组中的数据不能实时更新的问题

Vue 的数据监视是通过 getter 和 setter 实现的，当修改数据就会调用 setter 方法，里面会调用更新 DOM 的方法，如下：

```html
<script type="text/javascript" >

 let data = {
  name:'尚硅谷',
  address:'北京',
 }

 //创建一个监视的实例对象，用于监视data中属性的变化
 const obs = new Observer(data)  
 console.log(obs) 

 //准备一个vm实例对象
 let vm = {}
 vm._data = data = obs

 function Observer(obj){
  //汇总对象中所有的属性形成一个数组
  const keys = Object.keys(obj)
  //遍历
  keys.forEach((k)=>{
   Object.defineProperty(this,k,{
    get(){
     return obj[k]
    },
    set(val){
     console.log(`${k}被改了，我要去解析模板，生成虚拟DOM.....我要开始忙了`)
     obj[k] = val
    }
   })
  })
 }
</script>
```

当我们修改数组中的对象或添加原来不存在的属性时，不会有 setter 方法，所以不会修改 DOM。

但是修改数组中对象的属性时，会有 setter 方法，会更新 DOM。

直接修改对象或添加原来不存在的属性时，可以使用 Vue.set() 方法，让 Vue 侦听到这个数据的变动，并去更新 DOM，使用方法如下：

```html
<script type="text/javascript">
 Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

 const vm = new Vue({
  el:'#root',
  data:{
   student:{
    name:'tom',
    age:{
     rAge:40,
     sAge:29,
    },
  },
  methods: {
   addSex(){
    // Vue.set(this.student,'sex','男')
    this.$set(this.student,'sex','男')
   }
  }
 })
</script>
```

## Vue监视数据的原理：
 1. vue 会监视 data 中所有层次的数据。

 2. 如何监测对象中的数据？

     通过 setter 实现监视，且要在 new Vue 时就传入要监测的数据。

     (1).对象中后追加的属性，Vue 默认不做响应式处理

     (2).如需给后添加的属性做响应式，请使用如下 API：
     ```
     Vue.set(target，propertyName/index，value)
     vm.$set(target，propertyName/index，value)
     ```

 3. 如何监测数组中的数据？

      通过包裹数组更新元素的方法实现，本质就是做了两件事：

      - 调用原生对应的方法对数组进行更新。
      - 重新解析模板，进而更新页面。

 4. 在Vue修改数组中的某个元素一定要用如下方法：

      - 使用这些API：push()、pop()、shift()、unshift()、splice()、sort()、reverse()
      - Vue.set() 或 vm.$set()

> 特别注意：Vue.set() 和 vm.$set() 不能给 vm 或 vm 的根数据对象 添加属性！！！

# 响应式原理

## vue2.x的响应式

实现原理：

- 对象类型：通过 `Object.defineProperty()` 对属性的读取、修改进行拦截（数据劫持）。

- 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。

    ```js
    Object.defineProperty(data, 'count', {
        get () {}, 
        set () {}
    })
    ```

存在问题：

- 新增属性、删除属性, 界面不会更新。
- 直接通过下标修改数组, 界面不会自动更新。

## Vue3.0的响应式

实现原理: 

- 通过 Proxy（代理）:  拦截对象中任意属性的变化, 包括：属性值的读写、属性的添加、属性的删除等。

- 通过 Reflect（反射）:  对源对象的属性进行操作。

- MDN 文档中描述的 Proxy 与 Reflect：

    - Proxy：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy

    - Reflect：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

        ```js
        new Proxy(data, {
        	// 拦截读取属性值
            get (target, prop) {
            	return Reflect.get(target, prop)
            },
            // 拦截设置属性值或添加新属性
            set (target, prop, value) {
            	return Reflect.set(target, prop, value)
            },
            // 拦截删除属性
            deleteProperty (target, prop) {
            	return Reflect.deleteProperty(target, prop)
            }
        })
        
        proxy.name = 'tom'   
        ```

# 自定义指令 directives

> 注意：尽可能地使用 `v-bind` 这样的内置指令来声明式地使用模板，这样更高效，也对服务端渲染更友好。

## 两种定义方式

函数：在 bind 和 update 时该指令会生效（Vue3 是在 mounted 和 update 时生效）

对象：可以指定生效的位置（钩子函数）

```html
<div id="app">
  <button v-fbind>{{name}}</button>
</div>
<script>
    //全局指令
    Vue.directive('my-directive', function(el, binding){
    	console.log(el)
    })

  const vm = new Vue({
    el: "#app",
    data: {
      name: 'yyb'
    },
    //局部指令
    directives: {
      fbind: {
        //指令与元素成功绑定时（一上来）
        bind(element, binding) {},
        //指令所在元素被插入页面时
        inserted(element, binding) {},
        //指令所在的模板被重新解析时
        update(element, binding) {}
      }
    }
  })

</script>
```

## 钩子指令

> 注意：Vue2 中只有 bind、insert、update 三个钩子

```javascript
//指令与元素成功绑定时（一上来）
bind(el, binding, vnode, prevVnode) {},
//指令所在元素被插入页面时
inserted(el, binding, vnode, prevVnode) {},
//指令所在的模板被重新解析时
update(el, binding, vnode, prevVnode) {},
// 在绑定元素的 attribute 前，或事件监听器应用前调用
created(el, binding, vnode, prevVnode) {},
// 在元素被插入到 DOM 前调用
beforeMount(el, binding, vnode, prevVnode) {},
// 在绑定元素的父组件，及他自己的所有子节点都挂载完成后调用
mounted(el, binding, vnode, prevVnode) {},
// 绑定元素的父组件更新前调用
beforeUpdate(el, binding, vnode, prevVnode) {},
// 在绑定元素的父组件，及他自己的所有子节点都更新后调用
updated(el, binding, vnode, prevVnode) {},
// 绑定元素的父组件卸载前调用
beforeUnmount(el, binding, vnode, prevVnode) {},
// 绑定元素的父组件卸载后调用
unmounted(el, binding, vnode, prevVnode) {}
```

## 钩子参数

指令的钩子会传递以下几种参数：

- `el`：指令绑定到的元素。这可以用于直接操作 DOM。
- `binding`：一个对象，包含以下属性。
    - `value`：传递给指令的值。例如在 `v-my-directive="1 + 1"` 中，值是 `2`。
    - `oldValue`：之前的值，仅在 `beforeUpdate` 和 `updated` 中可用。无论值是否更改，它都可用。
    - `arg`：传递给指令的参数 (如果有的话)。例如在 `v-my-directive:foo` 中，参数是 `"foo"`。
    - `modifiers`：一个包含修饰符的对象 (如果有的话)。例如在 `v-my-directive.foo.bar` 中，修饰符对象是 `{ foo: true, bar: true }`。
    - `instance`：使用该指令的组件实例。
    - `dir`：指令的定义对象。
- `vnode`：代表绑定元素的底层 VNode。
- `prevNode`：之前的渲染中代表指令所绑定元素的 VNode。仅在 `beforeUpdate` 和 `updated` 钩子中可用。

# 生命周期

## 常用的生命周期钩子：

1. mounted: 发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。
2. beforeDestroy: 清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】。

## 关于销毁Vue实例

1. 销毁后借助Vue开发者工具看不到任何信息。
2. 销毁后自定义事件会失效，但原生DOM事件依然有效。
3. 一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了。

## 下面是实例生命周期的图表。

### Vue3

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/lifecycle.16e4c08e.png" alt="组件生命周期图示" style="zoom: 50%;" />

### Vue2

![](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

# 组件化

## 非单位件组件

### 定义

使用 Vue.extend(options) 创建，其中 options 和 new Vue(options) 时传入的那个 options 几乎一样，但也有点区别；

区别如下：

- el不要写，为什么？ ——— 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
- data必须写成函数，为什么？ ———— 避免组件被复用时，数据存在引用关系。

> 注意：使用 template 可以配置组件结构。

### 注册

1. 局部注册：靠 new Vue 的时候传入 components 选项
2. 全局注册：靠 Vue.component('组件名',组件)

### 使用

编写组件标签：`<school></school>`

### 注意点

1. 关于组件名:

    一个单词组成：

    - 第一种写法(首字母小写)：school

    - 第二种写法(首字母大写)：School

    多个单词组成：

    - 第一种写法(kebab-case命名)：my-school

    - 第二种写法(CamelCase命名)：MySchool (需要Vue脚手架支持)

    备注：

    - 组件名尽可能回避HTML中已有的元素名称，例如：h2、H2都不行。
    - 可以使用name配置项指定组件在开发者工具中呈现的名字。

2. 关于组件标签:

    第一种写法：`<school></school>`

    第二种写法：`<school/>`

    备注：不用使用脚手架时，`<school/>` 会导致后续组件不能渲染。

3. 一个简写方式：

    const school = Vue.extend(options) 可简写为：const school = options

### 案例

> 注意：组件中也可以通过 components 定义组件

```html
<body>
<div id="app">
  <hello></hello>
</div>
<script>

  const hello = Vue.extend({
    template: `
      <div>
      {{ msg }}
      </div>
    `,
    data() {
      return {
        msg: 'hello'
      }
    }
  })

  new Vue({
    el: "#app",
    components: {
      hello
    }

  })
</script>

</body>
```

## 单文件组件

> 注意：单文件组件必须在服务器上使用，直接通过文件使用会报错

### 三个部分组成

- template：页面模板
- script：js 模块
- style：页面样式

```vue
<template>

</template>

<script>
export default {
  name: "my"
}
</script>

<style scoped>

</style>
```

### 使用组件

```vue
<template>
 <div>
  <!--使用组件-->
  <School></School>
  <Student></Student>
 </div>
</template>

<script>
 //引入组件
 import School from './School.vue'
 import Student from './Student.vue'

 export default {
  name:'App',
  // 注册组件
  components:{
   School,
   Student
  }
 }
</script>
```

## VueComponent

1. school 组件本质是一个名为 VueComponent 的构造函数，且不是程序员定义的，是 Vue.extend 生成的。

2. 我们只需要写 `<school/>` 或 `<school></school>`，Vue 解析时会帮我们创建 school 组件的实例对象，

    即 Vue 帮我们执行的：new VueComponent(options)。

3. 特别注意：每次调用 Vue.extend，返回的都是一个全新的 VueComponent！！！！

4. 关于 this 指向：

    - 组件配置中：data 函数、methods 中的函数、watch 中的函数、computed 中的函数 它们的 this 均是【VueComponent实例对象】。
    - new Vue(options)配置中：data 函数、methods 中的函数、watch 中的函数、computed 中的函数 它们的 this 均是【Vue实例对象】。

5. VueComponent的实例对象，以后简称 vc（也可称之为：组件实例对象）。Vue 的实例对象，以后简称 vm。

> 注意：
>
> 一个重要的内置关系：`VueComponent.prototype.__proto__ === Vue.prototype`
>
> 为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue 原型上的属性、方法。

# 脚手架 Vue-cli

## 安装

官网：[https://cli.vuejs.org/zh](https://cli.vuejs.org/zh)

全局安装 Vue-cli@3：`npm install @vue/cli@3.12.0 -g`

使用命令 `vue --version` 查看安装的版本

在 cli 3 中拉取 cli 2 的模板：`npm install -g @vue/cli-init`

安装成功后可以用脚手架3和脚手架2

## Vue3 项目结构

```
├── node_modules
├── public
│ ├── favicon.ico: 页签图标
│ └── index.html: 主页面
├── src
│ ├── assets: 存放静态资源
│ │ └── logo.png
│ │── component: 存放组件
│ │ └── HelloWorld.vue
│ │── App.vue: 汇总所有组件
│ │── main.js: 入口文件
├── .gitignore: git 版本管制忽略的配置
├── babel.config.js: babel 的配置文件
├── package.json: 应用包配置文件
├── README.md: 应用描述文件
├── package-lock.json：包版本控制文件
```

## Vue2 目录结构

```
├── buoid：webpack相关配置
├── config：webpack相关配置
├── node_modules：node相关模块
├── src：源码
│ ├── assets: 存放静态资源
│ │ └── logo.png
│ │── component: 存放组件
│ │ └── HelloWorld.vue
│ │── router: 存放路由配置文件
│ │── App.vue: 汇总所有组件
│ │── main.js: 入口文件
├── static：静态资源
├── .babelrc：ES代码相关配置
├── babel.config.js: babel 的配置文件
├── .editorconfig：项目文本相关配置
├── .eslintignore：用于设置忽略哪些文件的语法检查
├── .eslintrc.js：设置eslint规则
├── .gitignore：git仓库忽略的文件夹配置
├── .postcssrc.js：css相关转化配置
├── index.html：首页入口文件
├── package.json：配置项目文件
├── README.md：项目说明文档
```

# ref 和 props

## ref

在标签上添加 ref 属性，即可通过 vue 实例的 `$refs` 属性得到其 DOM 元素或 VueComponent 实例

```vue
<template>
 <div>
  <h1 v-text="msg" ref="title"></h1>
  <button ref="btn" @click="showDOM">点我输出上方的DOM元素</button>
  <School ref="sch"/>
 </div>
</template>

<script>
 //引入School组件
 import School from './components/School'

 export default {
  name:'App',
  components:{School},
  data() {
   return {
    msg:'欢迎学习Vue！'
   }
  },
  methods: {
   showDOM(){
    console.log(this.$refs.title) //真实DOM元素
    console.log(this.$refs.btn) //真实DOM元素
    console.log(this.$refs.sch) //School组件的实例对象（vc）
   }
  },
 }
</script>
```

## props

用于在使用组件时，父组件向子组件传递参数，可以接收由父组件传递进来的函数。

> 注意：不能直接修改接收到的数据

### 三种定义方式

```vue
<template>
	<div>
		<h1>{{msg}}</h1>
		<h2>学生姓名：{{name}}</h2>
		<h2>学生性别：{{sex}}</h2>
		<h2>学生年龄：{{myAge+1}}</h2>
		<button @click="updateAge">尝试修改收到的年龄</button>
	</div>
</template>
<script>
	export default {
		name:'Student',
		data() {
			console.log(this)
			return {
				msg:'我是一个尚硅谷的学生',
				myAge:this.age
			}
		},
		methods: {
			updateAge(){
				this.myAge++
			}
		},
		//简单声明接收
		// props:['name','age','sex'] 

		//接收的同时对数据进行类型限制
		/* props:{
			name:String,
			age:Number,
			sex:String
		} */

		//接收的同时对数据：进行类型限制+默认值的指定+必要性的限制
		props:{
			name:{
				type:String, //name的类型是字符串
				required:true, //name是必要的
			},
			age:{
				type:Number,
				default:99 //默认值
			},
			sex:{
				type:String,
				required:true
			}
		}
	}
</script>
```

### 使用

在使用组件时，通过属性名的方式将数据传递给子组件，直接写数据，接收到的就是字符串，通过 `v-bind` 绑定即可得到表达式数据

```vue
<template>
 <div>
  <Student name="李四" sex="女" :age="18"/>
 </div>
</template>

<script>
 import Student from './components/Student'

 export default {
  name:'App',
  components:{Student}
 }
</script>
```

# 混入 mixin

可以把多个组件共用的配置（data、method、filter、生命周期函数等）提取成一个混入对象，在需要使用这些功能的组件中混入这个对象，即可使用这个对象中的数据或方法。

## 定义混入对象

```javascript
export const hunhe = {
 methods: {
  showName(){
   alert(this.name)
  }
 },
 mounted() {
  console.log('你好啊！')
 },
}
```

## 使用混入

全局混入：`Vue.mixin(xxx)`

局部混入：

```vue
<template>
 <div>
  <h2 @click="showName">学校名称：{{name}}</h2>
  <h2>学校地址：{{address}}</h2>
 </div>
</template>

<script>
 //引入一个hunhe
 import {hunhe,hunhe2} from '../mixin'

 export default {
  name:'School',
  data() {
   return {
    name:'尚硅谷',
    address:'北京',
    x:666
   }
  },
  mixins:[hunhe,hunhe2],
 }
</script>
```

# 插件

插件通常用来为 Vue 添加全局功能。插件的功能范围没有严格的限制。

其本质就是包含 install 方法的一个对象，install 方法的第一个参数是 Vue，第二个以后的参数是插件使用者传递的数据。

## 定义插件

```javascript
export default {
 install(Vue,x,y,z){
  console.log(x,y,z)
  //全局过滤器
  Vue.filter('mySlice',function(value){
   return value.slice(0,4)
  })

  //定义全局指令
  Vue.directive('fbind',{
   //指令与元素成功绑定时（一上来）
   bind(element,binding){
    element.value = binding.value
   },
   //指令所在元素被插入页面时
   inserted(element,binding){
    element.focus()
   },
   //指令所在的模板被重新解析时
   update(element,binding){
    element.value = binding.value
   }
  })

  //定义混入
  Vue.mixin({
   data() {
    return {
     x:100,
     y:200
    }
   },
  })

  //给Vue原型上添加一个方法（vm和vc就都能用了）
  Vue.prototype.hello = ()=>{alert('你好啊')}
 }
}
```

## 使用插件

```javascript
//引入插件
import plugins from './plugins'
import App from './App.vue'

//应用（使用）插件
Vue.use(plugins,1,2,3)
//创建vm
new Vue({
 el:'#app',
 render: h => h(App)
})
```

# style 标签的属性

## scoped

让 style 中的样式只在当前组件生效，防止冲突

```vue
<style scoped>
 .demo{
  background-color: skyblue;
 }
</style>
```

## lang

指定 css 的写法，不写默认是原生 css

```vue
<style lang="less" scoped>
 .demo{
  background-color: pink;
  .atguigu{
   font-size: 40px;
  }
 }
</style>
```

# 组件的自定义事件

子组件向父组件传递数据的方式，事件需要在子组件中定义，回调则写在父组件中。

子组件发送自定义事件：`this.$emit('事件名称',数据)`

绑定自定义事件：

- 第一种方式，在父组件中：```<Demo @事件名称="test"/>```  或 ```<Demo v-on:事件名称="test"/>```

- 第二种方式，在父组件中：

    ```html
    <Demo ref="demo"/>
    ......
    mounted(){
    	this.$refs.xxx.$on('atguigu',this.test)
    }     
    ```

若想让自定义事件只能触发一次，可以使用 ```once``` 修饰符，或 ```$once``` 方法。

解绑自定义事件 ```this.$off('atguigu')```

组件上也可以绑定原生 DOM 事件，需要使用 ```native``` 修饰符。

注意：通过 ```this.$refs.xxx.$on('atguigu',回调)``` 绑定自定义事件时，回调<span style="color:red">要么配置在 methods 中</span>，<span style="color:red">要么用箭头函数</span>，否则 this 指向会出问题！

# 全局事件总线

依据 `VueComponent.prototype.__proto__ === Vue.prototype` 的原理，将 `Vue.prototype.$bus` 修改为 vm 实例

```javascript
new Vue({
	el:'#app',
	render: h => h(App),
	beforeCreate() {
		Vue.prototype.$bus = this //安装全局事件总线
	},
})
```

这样可以使所有组件都能够访问到 `$bus` ，在接受方的生命周期函数 `mounted` 中绑定事件，`beforeDestroy` 中将事件解绑

```javascript
<script>
	export default {
		name:'School',
		data() {
			return {
				name:'尚硅谷',
				address:'北京',
			}
		},
		mounted() {
			this.$bus.$on('hello',(data)=>{
				console.log('我是School组件，收到了数据',data)
			})
		},
		beforeDestroy() {
			this.$bus.$off('hello')
		},
	}
</script>
```

发送方通过 `this.$bus.$emit` 触发事件

```javascript
<script>
 export default {
  name:'Student',
  data() {
   return {
    name:'张三',
    sex:'男',
   }
  },
  methods: {
   sendStudentName(){
    this.$bus.$emit('hello',this.name)
   }
  },
 }
</script>
```

# 发布订阅

与全局事件总线极度相似，所以不推荐使用，因为需要使用第三方包，例如 `pubsub-js`

安装 pubsub：```npm i pubsub-js```

引入: ```import pubsub from 'pubsub-js'```

接收数据：A组件想接收数据，则在A组件中订阅消息，订阅的<span style="color:red">回调留在A组件自身。</span>

   ```js
   methods(){
     demo(data){......}
   }
   ......
   mounted() {
     this.pid = pubsub.subscribe('xxx',this.demo) //订阅消息
   }
   ```
提供数据：`pubsub.publish('xxx',数据)`

最好在beforeDestroy钩子中，用 `PubSub.unsubscribe(pid)` 去<span style="color:red">取消订阅。</span>

# nextTick 函数回调

作用：在下一次 DOM 更新结束后执行其指定的回调。

什么时候用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行。

# Vue 封装的过度与动画

在插入、更新或移除 DOM 元素时，在合适的时候给元素添加样式类名。在开发中可以直接使用开源的的动画库

准备好样式：
- 元素进入的样式：
  1. v-enter：进入的起点，过度
  2. v-enter-active：进入过程中，动画，需要绑定 `@keyframes`
  3. v-enter-to：进入的终点，过度
  
- 元素离开的样式：
  1. v-leave：离开的起点，过度
  
  2. v-leave-active：离开过程中，动画，需要绑定 `@keyframes`

  3. v-leave-to：离开的终点，过度
  
    使用```<transition>```包裹要过度的元素，并配置name属性：
  
    ```vue
    <transition name="hello" appear>
     <h1 v-show="isShow">你好啊！</h1>
    </transition>
    ```

> 注意：
>
> 若有多个元素需要过度，则需要使用：`<transition-group>`，且每个元素都要指定 `key` 值。
>
> transition 标签上需要添加 appear 属性才会让页面第一次加载时显示动画，否则需要在标签状态改变时才会触发

```vue
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <transition name="hello" appear>
      <h1 v-show="isShow">你好啊！</h1>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'Test',
  data() {
    return {
      isShow: true
    }
  },
}
</script>

<style scoped>
h1 {
  background-color: orange;
}

.hello-enter-active {
  animation: atguigu 0.5s linear;
}

.hello-leave-active {
  animation: atguigu 0.5s linear reverse;
}

@keyframes atguigu {
  from {
    transform: translateX(-50%);
  }
  to {
    transform: translateX(0px);
  }
}
</style>
```

# 代理服务器，解决跨域问题

在 `vue.config.js` 中添加 `devServer` 配置

开启代理服务器（方式一）：

​	访问前端服务地址 `http://localhost:8000` 会直接跳转到指定服务器，如果当前服务器有对应资源则不会发起请求到后端服务器

```
module.exports = {
	devServer: {
		proxy: 'http://localhost:5000'
	} 
}
```

开启代理服务器（方式二）：

​	访问前端服务地址 `http://localhost:8000/atguigu` 会直接跳转到指定的 `http://localhost:5000/atguigu` 服务器

​	`pathRewrite:{'^/atguigu':''}` 表示将对应后台地址中的 `/atguigu` 替换成空字符串，使结果成为 `http://localhost:5000/`

```javascript
module.exports = {
    devServer: {
        proxy: {
            '/atguigu': {
                target: 'http://localhost:5000',
                pathRewrite:{'^/atguigu':''},
                // ws: true, //用于支持 websocket
                // changeOrigin: true //用于控制请求头中的host值
            },
            '/demo': {
                target: 'http://localhost:5001',
                pathRewrite:{'^/demo':''},
                // ws: true, //用于支持 websocket
                // changeOrigin: true //用于控制请求头中的host值
            }
        }
    }
}
```

# vue-resourse

vue 用来与后台发起 ajax 请求的工具，与 axios 类似，使用时将 axios 替换成 http 即可

> 注意：这是一个插件，需要在 main.js 进行安装
>
> ```javascript
> //引入插件
> import vueResource from 'vue-resource'
> //使用插件
> Vue.use(vueResource)
> ```

```javascript
methods: {
    searchUsers(){
        this.$http.get(`https://api.github.com/search/users?q=${this.keyWord}`).then(
            response => {
                console.log('请求成功了')
            },
            error => {
                this.$bus.$emit('updateListData',{isLoading:false,errMsg:error.message,users:[]})
            }
        )
    }
},
```

# 插槽 slot

## 默认插槽

在组件中定义插槽

```vue
<template>
 <div class="category">
  <!-- 定义一个插槽（挖个坑，等着组件的使用者进行填充） -->
  <slot>我是一些默认值，当使用者没有传递具体结构时，我会出现</slot>
 </div>
</template>
```

使用组件时可以向插槽中传递指定内容，即可在同一组件显示不同的内容，`但一个插槽只能传入一个主体`

```vue
<template>
 <div class="container">
  <Category title="美食" >
   <img src="https://s3.ax1x.com/2021/01/16/srJlq0.jpg" alt="">
  </Category>

  <Category title="游戏" >
   <ul>
    <li v-for="(g,index) in games" :key="index">{{g}}</li>
   </ul>
  </Category>

  <Category title="电影">
   <video controls src="http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"></video>
  </Category>
 </div>
</template>

<script>
 import Category from './components/Category'
 export default {
  name:'App',
  components:{Category},
  data() {
   return {
    foods:['火锅','烧烤','小龙虾','牛排'],
    games:['红色警戒','穿越火线','劲舞团','超级玛丽'],
    films:['《教父》','《拆弹专家》','《你好，李焕英》','《尚硅谷》']
   }
  },
 }
</script>
```

## 具名插槽

具名插槽就是在定义插槽的时候指定 `name` 属性，使用时可以通过 `slot` 属性将内容放到对应插槽中

插槽的定义

```vue
<template>
 <div class="category">
  <h3>{{title}}分类</h3>
  <!-- 定义一个插槽（挖个坑，等着组件的使用者进行填充） -->
  <slot name="center">我是一些默认值，当使用者没有传递具体结构时，我会出现1</slot>
  <slot name="footer">我是一些默认值，当使用者没有传递具体结构时，我会出现2</slot>
 </div>
</template>
```

插槽的使用

```vue
<template>
 <div class="container">
  <Category title="美食" >
   <img slot="center" src="https://s3.ax1x.com/2021/01/16/srJlq0.jpg" alt="">
   <a slot="footer" href="http://www.atguigu.com">更多美食</a>
  </Category>
 </div>
</template>

<script>
 import Category from './components/Category'
 export default {
  name:'App',
  components:{Category},
  data() {
   return {
    foods:['火锅','烧烤','小龙虾','牛排']
   }
  },
 }
</script>
```

## 作用域插槽

作用域插槽适合数据写在子组件的情况，需要将数据传给 `slot` ，这样在使用插槽的时候即可得到子组件的数据

定义组件：

```vue
<template>
  <div>
    {{name}}<br>
    <slot name="a" :user="user">我是slot插槽</slot><br>
    <slot name="b" :user="user">我是slot插槽</slot><br>
    <slot name="c" :user="user">我是slot插槽</slot>
  </div>
</template>

<script>
export default {
  name: "MySlot",
  data() {
    return {
      // 数据
      name:'slot插槽',
      user:{
        name:'yyb',
        age:18,
        color:'pink'
      }
    }
  }
}
</script>
```

使用组件：

可以通过 `#c="{user}"` 得到绑定的 `slot` 中的 `user` 数据

如果不使用 `#name` 的简化写法，即可通过 `scop(已弃用，会报警告)` 或 `slot-scope` 获取到数据分装到 `c` 中，

```vue
<template>
  <div id="app">
    <MySlot>
      <template #a="{user}">
        <h1>{{user.name}}</h1>
      </template>
      <template #b="{user}">
        <h1>{{user.age}}</h1>
      </template>
      <template slot="c" slot-scope="c">
        <h1>{{c.user.color}}</h1>
      </template>
    </MySlot>
  </div>
</template>

<script>
import CreatingATask from './components/CreatingATask'
import TaskList from './components/TaskList'
import ToDoFooter from "@/components/ToDoFooter";

import MySlot from "@/components/MySlot";

export default {
  name: 'App',
  components: {
    MySlot
  }
</script>
```

## 通过 template 使用插槽的用法

通过 `template` 使用插槽时可以传入多个主题，如下面的 `div` 和 `h4`

通过 `template` 使用具名插槽时，通过 `v-slot:name` 的形式指定插槽名称

`v-slot:name` 也可简写成 `#name`

```vue
<Category title="电影">
   <video slot="center" controls src="http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"></video>
   <template v-slot:footer>
    <div class="foot">
     <a href="http://www.atguigu.com">经典</a>
     <a href="http://www.atguigu.com">热门</a>
     <a href="http://www.atguigu.com">推荐</a>
    </div>
    <h4>欢迎前来观影</h4>
   </template>
  </Category>
```

# Vuex

官网：https://vuex.vuejs.org/zh/guide/

在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

多个组件需要实现数据共享时可以考虑使用 Vuex

## 流程介绍

![image-20221118103529277](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20221118103529277.png)

## 创建 Vuex

创建文件：`src/store/index.js`

```javascript
//引入Vue核心库
import Vue from 'vue'
//引入Vuex
import Vuex from 'vuex'
//应用Vuex插件
Vue.use(Vuex)

//准备actions对象——响应组件中用户的动作
const actions = {
    //响应组件中加的动作
    jia(context,value){
        // console.log('actions中的jia被调用了',miniStore,value)
        context.commit('JIA',value)
    },
}
//准备mutations对象——修改state中的数据
const mutations = {
    //执行加
    JIA(state,value){
        // console.log('mutations中的JIA被调用了',state,value)
    }
}
//准备state对象——保存具体的数据
const state = {
    sum:0
}
   
//创建并暴露store
export default new Vuex.Store({
   actions,
   mutations,
   state
})
```

## 配置 Store

```javascript
//引入store
import store from './store'

//创建vm
new Vue({
   el:'#app',
   render: h => h(App),
   store
})
```

## 使用

```javascript
methods: {
    increment(){
        this.$store.commit('JIA',this.n)
    },
    decrement(){
        this.$store.commit('JIAN',this.n)
    },
    incrementOdd(){
        this.$store.dispatch('jiaOdd',this.n)
    },
    incrementWait(){
        this.$store.dispatch('jiaWait',this.n)
    },
},
```

组件中读取 vuex 中的数据：```$store.state.sum```

组件中修改 vuex 中的数据：`$store.dispatch('action中的方法名',数据)` 或 `$store.commit('mutations中的方法名',数据)`

   >  备注：没有网络请求或其他业务逻辑，组件中也可以越过 actions，即不写 `dispatch`，直接编写 `commit`

## getters 的使用

当 state 中的数据需要经过加工后再使用时，可以使用 getters 加工。

相当于计算属性

在 `store.js` 中追加 `getters` 配置

```javascript
const getters = {
    bigSum(state){
    	return state.sum * 10
    }
}

//创建并暴露store
export default new Vuex.Store({
    ......
    getters
})
```

组件中读取数据：`$store.getters.bigSum` 

## mapState、mapGetters、mapActions、mapMutations 的使用

使用之前需要在组件中引入对应的方法 `import {mapState,mapGetters,mapMutations,mapActions} from 'vuex'`

1. <strong>mapState方法：</strong>用于帮助我们映射```state```中的数据为计算属性

   ```js
   computed: {
       //借助mapState生成计算属性：sum、school、subject（对象写法）
        ...mapState({sum:'sum',school:'school',subject:'subject'}),
            
       //借助mapState生成计算属性：sum、school、subject（数组写法）
       ...mapState(['sum','school','subject']),
   },
   ```

2. <strong>mapGetters方法：</strong>用于帮助我们映射```getters```中的数据为计算属性

   ```js
   computed: {
       //借助mapGetters生成计算属性：bigSum（对象写法）
       ...mapGetters({bigSum:'bigSum'}),
   
       //借助mapGetters生成计算属性：bigSum（数组写法）
       ...mapGetters(['bigSum'])
   },
   ```

3. <strong>mapActions方法：</strong>用于帮助我们生成与```actions```对话的方法，即：包含```$store.dispatch(xxx)```的函数

   ```js
   methods:{
       //靠mapActions生成：incrementOdd、incrementWait（对象形式）
       ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   
       //靠mapActions生成：incrementOdd、incrementWait（数组形式）
       ...mapActions(['jiaOdd','jiaWait'])
   }
   ```

4. <strong>mapMutations方法：</strong>用于帮助我们生成与```mutations```对话的方法，即：包含```$store.commit(xxx)```的函数

   ```js
   methods:{
       //靠mapActions生成：increment、decrement（对象形式）
       ...mapMutations({increment:'JIA',decrement:'JIAN'}),
       
       //靠mapMutations生成：JIA、JIAN（对象形式）
       ...mapMutations(['JIA','JIAN']),
   }
   ```

> 注意：mapActions 与 mapMutations 使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。
>
> ```javascript
> <button @click="increment(n)">+</button>
> ```

## 模块化

> 注意：模块化后使用 mapXXX 方法时，需要开启名称空间，`namespaced:true`

修改 `store.js`，将不同类型的数据写在不同的模块，并通过 `modules` 添加到 vuex 中

   ```javascript
   const countAbout = {
     namespaced:true,//开启命名空间
     state:{x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){
          return state.sum * 10
       }
     }
   }
   
   const personAbout = {
     namespaced:true,//开启命名空间
     state:{ ... },
     mutations: { ... },
     actions: { ... }
   }
   
   const store = new Vuex.Store({
     modules: {
       countAbout,
       personAbout
     }
   })
   ```

读取数据：

组件中读取state数据：

   ```js
   //方式一：自己直接读取
   this.$store.state.personAbout.list
   //方式二：借助mapState读取：
   ...mapState('countAbout',['sum','school','subject']),
   ```

组件中读取getters数据：

```js
//方式一：自己直接读取
this.$store.getters['personAbout/firstPersonName']
//方式二：借助mapGetters读取：
...mapGetters('countAbout',['bigSum'])
```

组件中调用dispatch

```js
//方式一：自己直接dispatch
this.$store.dispatch('personAbout/addPersonWang',person)
//方式二：借助mapActions：
...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
```

组件中调用commit

```js
//方式一：自己直接commit
this.$store.commit('personAbout/ADD_PERSON',person)
//方式二：借助mapMutations：
...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
```

# 路由 vue-router

插件，需要通过 Vue.use() 进行安装

## Vue2

### 编写配置

新建 `src/router/index.js` 文件，编写如下内容：

```javascript
// 该文件专门用于创建整个应用的路由器
import VueRouter from 'vue-router'
//引入组件
import About from '../components/About'
import Home from '../components/Home'

//创建并暴露一个路由器
export default new VueRouter({
	routes:[
		{
			path:'/about',
			component:About
		},
		{
			path:'/home',
			component:Home
		}
	]
})
```

在 `main.js` 中进行 `router` 的配置

```javascript
//引入Vue
import Vue from 'vue'
//引入App
import App from './App.vue'
//引入VueRouter
import VueRouter from 'vue-router'
//引入路由器
import router from './router'

//关闭Vue的生产提示
Vue.config.productionTip = false
//应用插件
Vue.use(VueRouter)

//创建vm
new Vue({
 el:'#app',
 render: h => h(App),
 router:router
})
```

### 使用

通过 `<router-link>` 和 `<router-view>` 使用路由，

`<router-link>` 负责控路由地址，`<router-view>` 表示对应组件显示的位置，如下

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

> 注意：
>
> 路由组件通常存放在 `pages` 文件夹，一般组件通常存放在 `components` 文件夹。
>
> 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载。
>
> 每个组件都有自己的 `$route` 属性，里面存储着自己的路由信息。
>
> 整个应用只有一个 `router`，可以通过组件的 `$router` 属性获取到。

## 默认路由

默认情况下，进入网站首页，我们希望`<router-vue>`直接显示首页的内容

在 `/router/index.js` 中添加配置：将默认的地址重定向到 `/home`

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

## 多级路由

配置路由规则，使用 `children` 配置项，在其父级路由中配置。
```javascript
routes:[
    {
        path:'/about',
        component:About,
    },
    {
        path:'/home',
        component:Home,
        children:[ //通过children配置子级路由
            {
                path:'news', //此处一定不要写：/news
                component:News
            },
            {
                path:'message',//此处一定不要写：/message
                component:Message
            }
        ]
    }
]
```

跳转时要写完整路径：

```vue
<router-link to="/home/news">News</router-link>
```

## 路由的名称（name）

可以简化路由的跳转，需要先通过 `name` 属性对路由进行命名，如下：
```js
{
    path:'/demo',
        component:Demo,
            children:[
                {
                    path:'test',
                    component:Test,
                    children:[
                        {
                            name:'hello' //给路由命名
                            path:'welcome',
                            component:Hello,
                        }
                    ]
                }
            ]
}
```

简化跳转：

```vue
  <!--简化前，需要写完整的路径 -->
  <router-link to="/demo/test/welcome">跳转</router-link>
  
  <!--简化后，直接通过名字跳转 -->
  <router-link :to="{name:'hello'}">跳转</router-link>
  
  <!--简化写法配合传递参数 -->
  <router-link 
   :to="{
      name:'hello',
      query:{
         id:666,
              title:'你好'
      }
   }"
  >跳转</router-link>
```

## 路由的参数

路由支持 `query` 和 `param` 两种类型的参数

### 路由的 query 参数

跳转时直接在路径后通过 `?` 和 `$` 进行拼接，传递参数。如下：

 ```vue
 <!-- 跳转并携带query参数，to的字符串写法 -->
 <router-link :to="/home/message/detail?id=666&title=你好">跳转</router-link>
 
 <!-- 跳转并携带query参数，to的对象写法 -->
 <router-link 
 	:to="{
 	         path:'/home/message/detail',
 	         query:{
 	             id:666,
 	             title:'你好'
 	         }
 	     }"
 	>跳转</router-link>
 ```

接收参数时，通过 `$route.query` 获取：

   ```js
   $route.query.id
   $route.query.title
   ```

### 路由的 params 参数

配置路由时，需要在 `path` 属性中通过 `:name` 的形式声明需要传递的参数名称，如下：

   ```js
   {
       path:'/home',
       component:Home,
       children:[
          {
             path:'news',
             component:News
          },
          {
             component:Message,
             children:[
                {
                   name:'xiangqing',
                   path:'detail/:id/:title', //使用占位符声明接收params参数
                   component:Detail
                }
             ]
          }
       ]
   }
   ```

传递参数时有两种写法，第一种类似 `rest` 风格，第二种和 `query` 参数的第二种类似

   ```vue
   <!-- 跳转并携带params参数，to的字符串写法 -->
   <router-link :to="/home/message/detail/666/你好">跳转</router-link>
                
   <!-- 跳转并携带params参数，to的对象写法 -->
   <router-link 
       :to="{
          name:'xiangqing',
          params:{
             id:666,
               title:'你好'
          }
       }"
   >跳转</router-link>
   ```

   > 特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！

接收参数：

   ```js
   $route.params.id
   $route.params.title
   ```

## 路由的 props 配置

作用：让路由组件更方便的收到参数

在路由定义时，通过 `props` 属性进行配置，组件中即可通过 `props` 进行接收。

```js
{
   name:'xiangqing',
   path:'detail/:id',
   component:Detail,

   //第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
   // props:{a:900}

   //第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件
   // props:true
   
   //第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
   props(route){
      return {
         id:route.query.id,
         title:route.query.title
      }
   }
}
```

接收 `props`

```vue
<script>
 export default {
  name:'Detail',
  props:['id','title'],
 }
</script>
```

## \<router-link> 的 replace 属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为 ```push``` 和 ```replace```，```push ``` 是追加历史记录，```replace``` 是替换当前记录。路由跳转时候默认为 ```push```
3. 如何开启 ```replace``` 模式：```<router-link replace .......>News</router-link>```

## 编程式路由导航

作用：不借助```<router-link> ```实现路由跳转，让路由跳转更加灵活

具体编码：

   ```js
   //$router的两个API
   this.$router.push({
       name:'xiangqing',
          params:{
             id:xxx,
             title:xxx
          }
   })
   
   this.$router.replace({
       name:'xiangqing',
          params:{
             id:xxx,
             title:xxx
          }
   })
   this.$router.forward() //前进
   this.$router.back() //后退
   this.$router.go() //可前进也可后退
   ```

## 缓存路由组件

作用：让不展示的路由组件保持挂载，不被销毁。

具体编码：include 属性表示需要缓存的`组件名`，有多个也可以写成数组形式 `:include="['a','b]"`

   ```vue
   <keep-alive include="News"> 
       <router-view></router-view>
   </keep-alive>
   ```

## 两个新的生命周期钩子

路由组件所独有的两个钩子，用于捕获路由组件的激活状态。

具体名字：

   1. ```activated```路由组件被激活时触发。
   2. ```deactivated```路由组件失活时触发。

## 路由守卫

主要作用：对路由进行权限控制

分类：全局守卫、独享守卫、组件内守卫

### 全局守卫

   ```js
   //全局前置守卫：初始化时执行、每次路由切换前执行
   router.beforeEach((to,from,next)=>{
       console.log('beforeEach',to,from)
       if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
          if(localStorage.getItem('school') === 'atguigu'){ //权限控制的具体规则
             next() //放行
          }else{
             alert('暂无权限查看')
             // next({name:'guanyu'})
          }
       }else{
          next() //放行
       }
   })
   
   //全局后置守卫：初始化时执行、每次路由切换后执行
   router.afterEach((to,from)=>{
       console.log('afterEach',to,from)
       if(to.meta.title){ 
          document.title = to.meta.title //修改网页的title
       }else{
          document.title = 'vue_test'
       }
   })
   ```

### 独享守卫

需要配置在路由对象的属性中

   ```js
   {
   	name:'zhuye',
   	path:'/home',
   	component:Home,
   	meta:{title:'主页'},
   	children:[
   		{
   			name:'xinwen',
   			path:'news',
   			component:News,
   			meta:{isAuth:true,title:'新闻'},
   			beforeEnter: (to, from, next) => {
   				console.log('独享路由守卫',to,from)
   				if(to.meta.isAuth){ //判断是否需要鉴权
   					if(localStorage.getItem('school')==='atguigu'){
   						next()
   					}else{
   						alert('学校名不对，无权限查看！')
   					}
   				}else{
   					next()
   				}
   			}
   		},
   ```

### 组件内守卫

类似生命周期函数，写在组件内

   ```js
   //进入守卫：通过路由规则，进入该组件时被调用
   beforeRouteEnter (to, from, next) {
   },
   //离开守卫：通过路由规则，离开该组件时被调用
   beforeRouteLeave (to, from, next) {
   }
   ```

## 路由器的两种工作模式

对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。

hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。

### hash模式：

      1. 地址中永远带着#号，不美观 。
      2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
      3. 兼容性较好。

### history模式：

      1. 地址干净，美观 。
      2. 兼容性和hash模式相比略差。
      3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。    

# Vue3

## 工程创建

`Vue3` 的工程可以通过 `vue-cli` 和 `vite` 进行创建，`vue-cli` 的创建方式与 `vue2` 类似，但需要确保 `@vue/cli` 版本在 `4.5.0` 以上

通过 vite 创建 Vue3 工程的步骤如下：

官方文档：

```
## 创建工程
npm init vite-app <project-name>
## 进入工程目录
cd <project-name>
## 安装依赖
npm install
## 运行
npm run dev
```

## Composition（组合） API

### setup

Vue3.0 中一个新的配置项，值为一个函数。setup 是所有 <strong style="color:#DD5145">Composition API（组合API）</strong><i style="color:gray;font-weight:bold">“ 表演的舞台 ”</i>。组件中所用到的数据、方法等等，均要配置在 setup 中。

setup 函数的两种返回值：

1. 若返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用。（重点关注！）
2. <span style="color:#aad">若返回一个渲染函数：则可以自定义渲染内容。（了解）</span>

注意点：
    1. 尽量不要与 Vue2.x 配置混用
        - Vue2.x 配置（data、methos、computed...）中<strong style="color:#DD5145">可以访问到</strong> setup 中的属性、方法。
        - 但在 setup 中<strong style="color:#DD5145">不能访问到</strong> Vue2.x 配置（data、methos、computed...）。
        - 如果有重名, setup 优先。
       2. setup 不能是一个 async 函数，因为返回值不再是 return 的对象, 而是 promise, 模板看不到 return 对象中的属性。（后期也可以返回一个Promise 实例，但需要 Suspense 和异步组件的配合）

```vue
<template>
 <h1>一个人的信息</h1>
 <h2>姓名：{{name}}</h2>
 <h2>年龄：{{age}}</h2>
 <h2>性别：{{sex}}</h2>
 <h2>a的值是：{{a}}</h2>
 <button @click="sayHello">说话(Vue3所配置的——sayHello)</button>
 <br>
 <br>
 <br>
 <br>
 <button @click="test2">测试一下在Vue3的setup配置中去读取Vue2中的数据、方法</button>

</template>

<script>
 export default {
  name: 'App',
  //此处只是测试一下setup，暂时不考虑响应式的问题。
  setup(){
   //数据
   let name = '张三'
   let age = 18
   let a = 200

   //方法
   function sayHello(){
    alert(`我叫${name}，我${age}岁了，你好啊！`)
   }
   function test2(){
    console.log(name)
    console.log(age)
    console.log(sayHello)
    console.log(this.sex)
    console.log(this.sayWelcome)
   }

   //返回一个对象（常用）
   return {
    name,
    age,
    sayHello,
    test2,
    a
   }

   //返回一个函数（渲染函数）
   // return ()=> h('h1','尚硅谷')
  }
 }
</script>
```

### ref 和 reactive 函数

如果在 setup 中定义的数据需要是响应式的，则需要使用这两个函数。

#### ref

语法: ```const xxx = ref(initValue)``` 

- 创建一个包含响应式数据的<strong style="color:#DD5145">引用对象（reference对象，简称ref对象）</strong>。
- JS中操作数据： ```xxx.value```
- 模板中读取数据: 不需要.value，直接：```<div>{{xxx}}</div>```

备注：

- 接收的数据可以是：基本类型、也可以是对象类型。
- 基本类型的数据：响应式依然是靠 `Object.defineProperty()` 的 `get` 与 `set` 完成的。
- 对象类型的数据：内部 <i style="color:gray;font-weight:bold">“ 求助 ”</i> 了 Vue3.0 中的一个新函数—— `reactive` 函数。

#### reactive

作用: 定义一个<strong style="color:#DD5145">对象类型</strong>的响应式数据（基本类型不要用它，要用```ref```函数）

语法：`const 代理对象= reactive(源对象)` 接收一个对象（或数组），返回一个<strong style="color:#DD5145">代理对象（Proxy的实例对象，简称proxy对象）</strong>

reactive定义的响应式数据是“深层次的”。

内部基于 ES6 的 Proxy 实现，通过代理对象操作源对象内部数据进行操作。

#### reactive 对比 ref

从定义数据角度对比：

-  ref用来定义：<strong style="color:#DD5145">基本类型数据</strong>。
-  reactive用来定义：<strong style="color:#DD5145">对象（或数组）类型数据</strong>。
-  备注：ref也可以用来定义<strong style="color:#DD5145">对象（或数组）类型数据</strong>, 它内部会自动通过```reactive```转为<strong style="color:#DD5145">代理对象</strong>。

从原理角度对比：

-  ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）。
-  reactive通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据。

从使用角度对比：

-  ref定义的数据：操作数据<strong style="color:#DD5145">需要</strong>```.value```，读取数据时模板中直接读取<strong style="color:#DD5145">不需要</strong>```.value```。
-  reactive定义的数据：操作数据与读取数据：<strong style="color:#DD5145">均不需要</strong>```.value```。

### setup 的两个注意点

setup 执行的时机

- 在 beforeCreate 之前执行一次，this 是 undefined。

setup 的参数

- props：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性。
- context：上下文对象
    - attrs: 值为对象，包含：组件外部传递过来，但没有在 props 配置中声明的属性, 相当于 ```this.$attrs```。
    - slots: 收到的插槽内容, 相当于 ```this.$slots```。
    - emit: 分发自定义事件的函数, 相当于 ```this.$emit```。

### 计算属性

与 Vue2.x 中 computed 配置功能一致

写法

```js
import {computed} from 'vue'

setup(){
    ...
	//计算属性——简写
    let fullName = computed(()=>{
        return person.firstName + '-' + person.lastName
    })
    //计算属性——完整
    let fullName = computed({
        get(){
            return person.firstName + '-' + person.lastName
        },
        set(value){
            const nameArr = value.split('-')
            person.firstName = nameArr[0]
            person.lastName = nameArr[1]
        }
    })
}
```

### watch函数

与 Vue2.x 中 watch 配置功能一致

两个小“坑”：

- 监视 reactive 定义的响应式数据时：oldValue 无法正确获取、强制开启了深度监视（deep配置失效）。
- 监视 reactive 定义的响应式数据中某个属性时：deep 配置有效。

```js
//情况一：监视ref定义的响应式数据
watch(sum,(newValue,oldValue)=>{
	console.log('sum变化了',newValue,oldValue)
},{immediate:true})

//情况二：监视多个ref定义的响应式数据
watch([sum,msg],(newValue,oldValue)=>{
	console.log('sum或msg变化了',newValue,oldValue)
}) 

/* 情况三：监视reactive定义的响应式数据
			若watch监视的是reactive定义的响应式数据，则无法正确获得oldValue！！
			若watch监视的是reactive定义的响应式数据，则强制开启了深度监视 
*/
watch(person,(newValue,oldValue)=>{
	console.log('person变化了',newValue,oldValue)
},{immediate:true,deep:false}) //此处的deep配置不再奏效

//情况四：监视reactive定义的响应式数据中的某个属性
watch(()=>person.job,(newValue,oldValue)=>{
	console.log('person的job变化了',newValue,oldValue)
},{immediate:true,deep:true}) 

//情况五：监视reactive定义的响应式数据中的某些属性
watch([()=>person.job,()=>person.name],(newValue,oldValue)=>{
	console.log('person的job变化了',newValue,oldValue)
},{immediate:true,deep:true})

//特殊情况
watch(()=>person.job,(newValue,oldValue)=>{
    console.log('person的job变化了',newValue,oldValue)
},{deep:true}) //此处由于监视的是reactive素定义的对象中的某个属性，所以deep配置有效
```

### watchEffect函数

watch 的套路是：既要指明监视的属性，也要指明监视的回调。

watchEffect 的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性。

watchEffect 有点像 computed：

- 但 computed 注重的计算出来的值（回调函数的返回值），所以必须要写返回值。
- 而 watchEffect 更注重的是过程（回调函数的函数体），所以不用写返回值。

```js
//watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
watchEffect(()=>{
    const x1 = sum.value
    const x2 = person.age
    console.log('watchEffect配置的回调执行了')
})
```

### 组合式生命周期

```vue
<script>
 import {ref,onBeforeMount,onMounted,onBeforeUpdate,onUpdated,onBeforeUnmount,onUnmounted} from 'vue'
 export default {
  name: 'Demo',
  
  setup(){
   console.log('---setup---')
   //数据
   let sum = ref(0)

   //通过组合式API的形式去使用生命周期钩子
   onBeforeMount(()=>{
    console.log('---onBeforeMount---')
   })
   onMounted(()=>{
    console.log('---onMounted---')
   })
   onBeforeUpdate(()=>{
    console.log('---onBeforeUpdate---')
   })
   onUpdated(()=>{
    console.log('---onUpdated---')
   })
   onBeforeUnmount(()=>{
    console.log('---onBeforeUnmount---')
   })
   onUnmounted(()=>{
    console.log('---onUnmounted---')
   })

   //返回一个对象（常用）
   return {sum}
  },
  //通过配置项的形式使用生命周期钩子
  //#region 
  beforeCreate() {
   console.log('---beforeCreate---')
  },
  created() {
   console.log('---created---')
  },
  beforeMount() {
   console.log('---beforeMount---')
  },
  mounted() {
   console.log('---mounted---')
  },
  beforeUpdate(){
   console.log('---beforeUpdate---')
  },
  updated() {
   console.log('---updated---')
  },
  beforeUnmount() {
   console.log('---beforeUnmount---')
  },
  unmounted() {
   console.log('---unmounted---')
  },
  //#endregion
 }
</script>
```

### 自定义 hook 函数

hook 本质是一个函数，把 setup 函数中使用的 Composition API 进行了封装。、

定义

```javascript
import {reactive,onMounted,onBeforeUnmount} from 'vue'
export default function (){
 //实现鼠标“打点”相关的数据
 let point = reactive({
  x:0,
  y:0
 })

 //实现鼠标“打点”相关的方法
 function savePoint(event){
  point.x = event.pageX
  point.y = event.pageY
  console.log(event.pageX,event.pageY)
 }

 //实现鼠标“打点”相关的生命周期钩子
 onMounted(()=>{
  window.addEventListener('click',savePoint)
 })

 onBeforeUnmount(()=>{
  window.removeEventListener('click',savePoint)
 })

 return point
}
```

使用

```vue
<template>
 <h2>我是Test组件</h2>
 <h2>当前点击时鼠标的坐标为：x：{{point.x}}，y：{{point.y}}</h2>
</template>

<script>
 import usePoint from '../hooks/usePoint'
 export default {
  name:'Test',
  setup(){
   const point = usePoint()
   return {point}
  }
 }
</script>
```

### toRef

- 作用：创建一个 ref 对象，其value值指向另一个对象中的某个属性。
- 语法：```const name = toRef(person,'name')```
- 应用:   要将响应式对象中的某个属性单独提供给外部使用时。


- 扩展：```toRefs``` 与```toRef```功能一致，但可以批量创建多个 ref 对象，语法：```toRefs(person)```


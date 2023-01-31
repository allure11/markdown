# 高级

## 数据类型

### 分类(2大类)

基本(值)类型

- Number: 任意数值

- String: 任意文本

- Boolean: true/false

- undefined: undefined

    undefined代表没有赋值

- null: null

    null代表赋值了, 只是值为null

    赋值为 null 的情况

    ```
    var a = null // a 将指向一个对象, 但对象此时还没有确定
    a = null // 让 a 指向的对象成为垃圾对象，则一段时间后，原对象会被垃圾回收器收回
    ```

对象(引用)类型

- Object: 任意对象
- Array: 特别的对象类型(下标/内部数据有序)
- Function: 特别的对象类型(可执行)

### 判断

typeof:

- 可以区别: 数值, 字符串, 布尔值, undefined, function
- 不能区别: null与对象, 一般对象与数组

instanceof：专门用来判断对象数据的类型: Object, Array与Function

- 可以判断: undefined和null

### 严格区别变量类型与数据类型

js的变量本身是没有类型的, 变量的类型实际上是变量内存中数据的类型

变量类型:

- 基本类型: 保存基本类型数据的变量
- 引用类型: 保存对象地址值的变量

数据类型

- 基本类型
- 对象类型

## 对象

### 什么是对象?

代表现实中的某个事物, 是该事物在编程中的抽象

多个数据的集合体(封装体)

用于保存多个数据的容器

### 为什么要用对象?
便于对多个数据进行统一管理

### 对象的组成

属性

- 代表现实事物的状态数据
- 由属性名和属性值组成
- 属性名都是字符串类型, 属性值是任意类型

方法

- 代表现实事物的行为数据
- 是特别的属性==>属性值是函数

### 如何访问对象内部数据?

对象.属性名: 编码简单, 但有时不能用

对象['属性名']: 编码麻烦, 但通用

当属性名`不是合法的标识名`或者`属性名不确定`时，必须使用['属性名']的方式?

## 函数

函数是具有特定功能的 n 条语句的封装体，只有函数是可执行的, 其它类型的数据是不可执行的。==函数也是对象==

函数的作用：提高代码复用，便于阅读和交流

### 定义函数
函数声明

```
function f1 () {
	console.log('f1()')
}
```

表达式

```
var f2 = function () {
	console.log('f2()')
}
```

### 调用(执行)函数
test()

new test()

obj.test()

test.call/apply(obj)

### 回调函数

程序员定义的没有直接调用，但最终它执行了(在特定条件或时刻)的函数

常见的回调函数有：DOM 事件函数、定时器函数、ajax 回调函数、生命周期回调函数

```javascript
// 1. DOM事件函数
var btn = document.getElementById('btn')
btn.onclick = function () {
	alert(this.innerHTML)
}

// 2. 定时器函数
setInterval(function () {
    alert('到点啦!')
}, 2000)
```

### 匿名函数自动调用（IIFE）

全称: Immediately-Invoked Function Expression 立即调用函数表达式

作用：隐藏内部实现、不污染外部命名空间

```javascript
<script type="text/javascript">
  // 直接大括号括起来，不用谢函数名，在后面加括号直接执行
  (function (i) {
    var a = 4
    function fn() {
      console.log('fn ', i+a)
    }
    fn()
  })(3)
</script>
```

### 函数中的 this 关键字

本质上任何函数在执行时都是通过某个对象调用的

是一个内置的引用变量，在函数中都可以直接使用 this，this 代表调用函数的当前对象。在定义函数时，this 还没有确定，只有在执行时才动态确定(绑定)的

如何确定this的值?
test()         obj.test()        new test()          test.call(obj)





## 数据、类型、变量

### 什么是数据?

存储于内存中代表特定信息的'东东', 本质就是（0101） 二进制。其具有可读和可传递的基本特性，万物(一切)皆数据，函数也是数据

程序中所有操作的目标: 数据、算术运算、逻辑运算、赋值、调用函数传参...

### 什么是内存?

内存条通电后产生的存储空间(临时的)

产生和死亡: 内存条(集成电路板) => 通电 => 产生一定容量的存储空间 => 存储各种数据 => 断电 => 内存全部消失

内存的空间是临时的, 而硬盘的空间是持久的

一块内存包含2个数据

- 内部存储的数据(一般数据/地址数据)
- 内存地址值数据

内存分类

- 栈: 全局变量, 局部变量 (空间较小)
- 堆: 对象 (空间较大)

### 什么是变量?

值可以变化的量, 由变量名与变量值组成

一个变量对应一块小内存, 变量名用来查找到内存, 变量值就是内存中保存的内容

### 内存,数据, 变量三者之间的关系

内存是一个容器, 用来存储程序运行需要操作的数据

变量是内存的标识, 我们通过变量找到对应的内存, 进而操作(读/写)内存中的数据

### 关于引用变量赋值问题

2 个引用变量指向同一个对象, 通过一个引用变量修改对象内部数据, 另一个引用变量也看得见

2 个引用变量指向同一个对象,让一个引用变量指向另一个对象, 另一个引用变量还是指向原来的对象

### 在 js 调用函数时传递变量参数时, 是值传递还是引用传递

只有值传递, 没有引用传递, 传递的都是变量的值, 只是这个值可能是基本数据, 也可能是地址(引用)数据

如果后一种看成是引用传递, 那就值传递和引用传递都可以有

### JS 引擎如何管理内存?

1. 内存生命周期

  分配需要的内存

  使用分配到的内存

  不需要时将其释放/归还

2. 释放内存

    为执行函数分配的栈空间内存: 函数执行完自动释放

    存储对象的堆空间内存: 当内存没有引用指向时, 对象成为垃圾对象, 垃圾回收器后面就会回收释放此内存

## 原型

### 函数的 prototype 属性(图)

每个函数都有一个 prototype 属性, 它默认指向一个 Object 空对象(即称为: 原型对象)

原型对象中有一个属性 constructor, 它指向函数对象

```
function fn() {
}
console.log(fn.prototype)
```



![image-20221026093019916](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20221026093019916.png)

### 给原型对象添加属性(一般都是方法)

作用: 函数的所有实例对象自动拥有原型中的属性(方法)

```javascript
给原型对象添加属性(一般都是方法)
function F() {

}
F.prototype.age = 12 //添加属性
F.prototype.setAge = function (age) { // 添加方法
  this.age = age
}
```

### 显示原型与隐式原型

1. 每个函数function都有一个prototype，即显式原型

2. 每个实例对象都有一个 `__proto__`，可称为隐式原型

3. 对象的隐式原型的值为其对应构造函数的显式原型的值

4. 内存结构

    ![image-20221026093414883](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20221026093414883.png)

5. 总结:

    函数的 prototype 属性: 在定义函数时自动添加的, 默认值是一个空 Object 对象

    对象的 `__proto__` 属性: 创建对象时自动添加的, 默认值为构造函数的 prototype 属性值

    程序员能直接操作显式原型, 但不能直接操作隐式原型(ES6之前)

## 原型链

别名: 隐式原型链

作用: 查找对象的属性(方法)

访问一个对象的属性时，先在自身属性中查找，找到返回；

如果没有, 再沿着 `__proto__` 这条链向上查找, 找到返回；

如果最终没找到, 返回 `undefined`

> 注意：
>
> 读取对象的属性值时: 会自动到原型链中查找
> 设置对象的属性值时: 不会查找原型链, 如果当前对象中没有此属性, 直接添加此属性并设置其值
> 方法一般定义在原型中, 属性一般通过构造函数定义在对象本身上

### instanceof是如何判断的?

表达式: A instanceof B

如果 B 函数的显式原型对象在 A 对象的原型链上, 返回 true, 否则返回 false

Function 是通过 new 自己产生的实例

## 变量和函数提示

### 变量声明提升

通过var定义(声明)的变量, 在定义语句之前就可以访问到

值: undefined

### 函数声明提升

通过function声明的函数, 在之前就可以直接调用

值: 函数定义(对象)

问题: 变量提升和函数提升是如何产生的?

### 代码分类(位置)

全局代码

函数(局部)代码

## 执行上下文和执行上下文栈

### 全局执行上下文

在执行全局代码前将window确定为全局执行上下文

对全局数据进行预处理

- var定义的全局变量 => undefined, 添加为window的属性
- function声明的全局函数 => 赋值(fun), 添加为window的方法
- this => 赋值(window)

开始执行全局代码

### 函数执行上下文

在调用函数, 准备执行函数体之前, 创建对应的函数执行上下文对象(虚拟的, 存在于栈中)

对局部数据进行预处理

- 形参变量 => 赋值(实参) => 添加为执行上下文的属性
- arguments => 赋值(实参列表), 添加为执行上下文的属性
- var定义的局部变量 => undefined, 添加为执行上下文的属性
- function声明的函数 => 赋值(fun), 添加为执行上下文的方法
- this => 赋值(调用函数的对象)

开始执行函数体代码

### 执行上下文栈

1. 在全局代码执行前, JS引擎就会创建一个栈来存储管理所有的执行上下文对象
2. 在全局执行上下文(window)确定后, 将其添加到栈中(压栈)
3. 在函数执行上下文创建后, 将其添加到栈中(压栈)
4. 在当前函数执行完后,将栈顶的对象移除(出栈)
5. 当所有的代码执行完后, 栈中只剩下window

```html
<!--
1. 依次输出什么?
  gb: undefined
  fb: 1
  fb: 2
  fb: 3
  fe: 3
  fe: 2
  fe: 1
  ge: 1
2. 整个过程中产生了几个执行上下文?  5
-->
<script type="text/javascript">
  console.log('gb: '+ i)
  var i = 1
  foo(1)
  function foo(i) {
    if (i == 4) {
      return
    }
    console.log('fb:' + i)
    foo(i + 1) //递归调用: 在函数内部调用自己
    console.log('fe:' + i)
  }
  console.log('ge: ' + i)
</script>
```

## 作用域与作用域链

### 作用域

1. 理解

  就是一块"地盘", 一个代码段所在的区域

  它是静态的(相对于上下文对象), 在编写代码时就确定了

2. 分类

  全局作用域

  函数作用域

  没有块作用域(ES6有了)

3. 作用

  隔离变量，不同作用域下同名变量不会有冲突

### 作用域链

1. 理解

  多个上下级关系的作用域形成的链, 它的方向是从下向上的(从内到外)

  查找变量时就是沿着作用域链来查找的

2. 查找一个变量的查找规则

  在当前作用域下的执行上下文中查找对应的属性, 如果有直接返回, 否则进入2

  在上一级作用域的执行上下文中查找对应的属性, 如果有直接返回, 否则进入3

  再次执行2的相同操作, 直到全局作用域, 如果还找不到就抛出找不到的异常

## 闭包

### 如何产生闭包?
当一个嵌套的内部(子)函数引用了嵌套的外部(父)函数的变量(函数)时, 就产生了闭包
### 闭包到底是什么?
使用chrome调试查看

理解一: 闭包是嵌套的内部函数(绝大部分人)

理解二: 包含被引用变量(函数)的对象(极少数人)

注意: 闭包存在于嵌套的内部函数中

### 产生闭包的条件?
函数嵌套

内部函数引用了外部函数的数据(变量/函数)

### 常见的闭包

```javascript
<script type="text/javascript">
  // 1. 将函数作为另一个函数的返回值
  function fn1() {
    var a = 2
    function fn2() {
      a++
      console.log(a)
    }
    return fn2
  }
  var f = fn1()
  f() // 3
  f() // 4

  // 2. 将函数作为实参传递给另一个函数调用
  function showDelay(msg, time) {
    setTimeout(function () {
      alert(msg)
    }, time)
  }
  showDelay('atguigu', 2000)

</script>
```

### 闭包的作用域问题

作用：

- 使用函数内部的变量在函数执行完后, 仍然存活在内存中(延长了局部变量的生命周期)
- 让函数外部可以操作(读写)到函数内部的数据(变量/函数)

问题:

- 函数执行完后, 函数内部声明的局部变量是否还存在?  一般是不存在, 存在于闭中的变量才可能存在
- 在函数外部能直接访问函数内部的局部变量吗? 不能, 但我们可以通过闭包让外部操作它

### 闭包的生命周期

产生: 在嵌套内部函数定义执行完时就产生了(不是在调用)

死亡: 在嵌套的内部函数成为垃圾对象时

```javascript
<script type="text/javascript">
  function fn1() {
    //此时闭包就已经产生了(函数提升, 内部函数对象已经创建了)
    var a = 2
    function fn2 () {
      a++
      console.log(a)
    }
    return fn2
  }
  var f = fn1()
  f() // 3
  f() // 4
  f = null //闭包死亡(包含闭包的函数对象成为垃圾对象)
</script>
```

### 闭包的应用

#### 定义JS模块：方式一

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>05_闭包的应用_自定义JS模块</title>
</head>
<body>
<!--
闭包的应用2 : 定义JS模块
  * 具有特定功能的js文件
  * 将所有的数据和功能都封装在一个函数内部(私有的)
  * 只向外暴露一个包信n个方法的对象或函数
  * 模块的使用者, 只需要通过模块暴露的对象调用方法来实现对应的功能
-->
<script type="text/javascript" src="myModule.js"></script>
<script type="text/javascript">
  var module = myModule()
  module.doSomething()
  module.doOtherthing()
</script>
</body>
</html>
```

```javascript
function myModule() {
  //私有数据
  var msg = 'My atguigu'
  //操作数据的函数
  function doSomething() {
    console.log('doSomething() '+msg.toUpperCase())
  }
  function doOtherthing () {
    console.log('doOtherthing() '+msg.toLowerCase())
  }

  //向外暴露对象(给外部使用的方法)
  return {
    doSomething: doSomething,
    doOtherthing: doOtherthing
  }
}
```

#### 定义JS模块：方式二

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>05_闭包的应用_自定义JS模块2</title>
</head>
<body>
<!--
闭包的应用2 : 定义JS模块
  * 具有特定功能的js文件
  * 将所有的数据和功能都封装在一个函数内部(私有的)
  * 只向外暴露一个包信n个方法的对象或函数
  * 模块的使用者, 只需要通过模块暴露的对象调用方法来实现对应的功能
-->
<script type="text/javascript" src="myModule2.js"></script>
<script type="text/javascript">
  myModule2.doSomething()
  myModule2.doOtherthing()
</script>
</body>
</html>
```

```javascript
(function () {
  //私有数据
  var msg = 'My atguigu'
  //操作数据的函数
  function doSomething() {
    console.log('doSomething() '+msg.toUpperCase())
  }
  function doOtherthing () {
    console.log('doOtherthing() '+msg.toLowerCase())
  }

  //向外暴露对象(给外部使用的方法)
  window.myModule2 = {
    doSomething: doSomething,
    doOtherthing: doOtherthing
  }
})()
```

### 闭包的缺点

缺点：函数执行完后, 函数内的局部变量没有释放, 占用内存时间会变长，容易造成内存泄露

解决：能不用闭包就不用，及时释放

```javascript
<script type="text/javascript">
  function fn1() {
    var arr = new Array[100000]
    function fn2() {
      console.log(arr.length)
    }
    return fn2
  }
  var f = fn1()
  f()

  f = null //让内部函数成为垃圾对象-->回收闭包
</script>
```

## 对象的创建模式

### Object 构造函数模式

套路: 先创建空Object对象, 再动态添加属性/方法

适用场景: 起始时不确定对象内部数据

问题: 语句太多

```javascript
<script type="text/javascript">
  /*
  	一个人: name:"Tom", age: 12
   */
  // 先创建空Object对象
  var p = new Object()
  p = {} //此时内部数据是不确定的
  // 再动态添加属性/方法
  p.name = 'Tom'
  p.age = 12
  p.setName = function (name) {
    this.name = name
  }

  //测试
  console.log(p.name, p.age)
  p.setName('Bob')
  console.log(p.name, p.age)
</script>
```

### 对象字面量模式
套路: 使用{}创建对象, 同时指定属性/方法

适用场景: 起始时对象内部数据是确定的

问题: 如果创建多个对象, 有重复代码

```javascript
<script type="text/javascript">
  var p = {
    name: 'Tom',
    age: 12,
    setName: function (name) {
      this.name = name
    }
  }

  //测试
  console.log(p.name, p.age)
  p.setName('JACK')
  console.log(p.name, p.age)

  var p2 = {  //如果创建多个对象代码很重复
    name: 'Bob',
    age: 13,
    setName: function (name) {
      this.name = name
    }
  }

</script>
```

### 工厂模式
套路: 通过工厂函数动态创建对象并返回

适用场景: 需要创建多个对象

问题: 对象没有一个具体的类型, 都是Object类型

```javascript
<script type="text/javascript">
  function createPerson(name, age) { //返回一个对象的函数===>工厂函数
    var obj = {
      name: name,
      age: age,
      setName: function (name) {
        this.name = name
      }
    }
    return obj
  }

  // 创建2个人
  var p1 = createPerson('Tom', 12)
  var p2 = createPerson('Bob', 13)

  // p1/p2是Object类型

  function createStudent(name, price) {
    var obj = {
      name: name,
      price: price
    }
    return obj
  }
  var s = createStudent('张三', 12000)
  // s也是Object
</script>
```

### 自定义构造函数模式

套路: 自定义构造函数, 通过new创建对象

适用场景: 需要创建多个类型确定的对象

问题: 每个对象都有相同的数据, 浪费内存

```javascript
<script type="text/javascript">
  //定义类型
  function Person(name, age) {
    this.name = name
    this.age = age
    this.setName = function (name) {
      this.name = name
    }
  }
  var p1 = new Person('Tom', 12)
  p1.setName('Jack')
  console.log(p1.name, p1.age)
  console.log(p1 instanceof Person)

  function Student (name, price) {
    this.name = name
    this.price = price
  }
  var s = new Student('Bob', 13000)
  console.log(s instanceof Student)

  var p2 = new Person('JACK', 23)
  console.log(p1, p2)
</script>
```

### 构造函数+原型的组合模式

套路: 自定义构造函数, 属性在函数中初始化, 方法添加到原型上

适用场景: 需要创建多个类型确定的对象

```javascript
<script type="text/javascript">
  function Person(name, age) { //在构造函数中只初始化一般函数
    this.name = name
    this.age = age
  }
  Person.prototype.setName = function (name) {
    this.name = name
  }

  var p1 = new Person('Tom', 23)
  var p2 = new Person('Jack', 24)
  console.log(p1, p2)

</script>
```

## 继承模式

### 原型链继承

套路

- 定义父类型构造函数
- 给父类型的原型添加方法
- 定义子类型的构造函数
- 创建父类型的对象赋值给子类型的原型
- 将子类型原型的构造属性设置为子类型
- 给子类型原型添加方法
- 创建子类型的对象: 可以调用父类型的方法

关键

- 子类型的原型为父类型的一个实例对象

```javascript
//父类型
function Supper() {
  this.supProp = 'Supper property'
}
Supper.prototype.showSupperProp = function () {
  console.log(this.supProp)
}

//子类型
function Sub() {
  this.subProp = 'Sub property'
}

// 子类型的原型为父类型的一个实例对象
Sub.prototype = new Supper()
// 让子类型的原型的 constructor 指向子类型
Sub.prototype.constructor = Sub
Sub.prototype.showSubProp = function () {
  console.log(this.subProp)
}

var sub = new Sub()
sub.showSupperProp()
// sub.toString()
sub.showSubProp()

console.log(sub)  // Sub
```

### 借用构造函数继承(假的)
套路:
- 定义父类型构造函数
- 定义子类型构造函数
- 在子类型构造函数中调用父类型构造

关键:

- 在子类型构造函数中通用call()调用父类型构造函数

```javascript
function Person(name, age) {
  this.name = name
  this.age = age
}
function Student(name, age, price) {
  Person.call(this, name, age)  // 相当于: this.Person(name, age)
  /*this.name = name
  this.age = age*/
  this.price = price
}

var s = new Student('Tom', 20, 14000)
console.log(s.name, s.age, s.price)
```

### 原型链+借用构造函数的组合继承
1. 利用原型链实现对父类型对象的方法继承
2. 利用super()借用父类型构建函数初始化相同属性

```javascript
function Person(name, age) {
  this.name = name
  this.age = age
}
Person.prototype.setName = function (name) {
  this.name = name
}

function Student(name, age, price) {
  Person.call(this, name, age)  // 为了得到属性
  this.price = price
}
Student.prototype = new Person() // 为了能看到父类型的方法
Student.prototype.constructor = Student //修正constructor属性
Student.prototype.setPrice = function (price) {
  this.price = price
}

var s = new Student('Tom', 24, 15000)
s.setName('Bob')
s.setPrice(16000)
console.log(s.name, s.age, s.price)
```

## 线程机制

1. 进程：程序的一次执行, 它占有一片独有的内存空间

2. 线程： CPU的基本调度单位, 是程序执行的一个完整流程

3. 进程与线程

  一个进程中一般至少有一个运行的线程: 主线程

  一个进程中也可以同时运行多个线程, 我们会说程序是多线程运行的

  一个进程内的数据可以供其中的多个线程直接共享

  多个进程之间的数据是不能直接共享的

4. 浏览器运行是单进程还是多进程?

  有的是单进程

  ​	firefox

  ​	老版IE

  有的是多进程

  ​	chrome

  ​	新版IE

5. 如何查看浏览器是否是多进程运行的呢?

  任务管理器==>进程

6. 浏览器运行是单线程还是多线程?

  都是多线程运行的

### 浏览器内核

1. 什么是浏览器内核?

  支持浏览器运行的最核心的程序

2. 不同的浏览器可能不太一样

  Chrome, Safari: webkit

  firefox: Gecko

  IE: Trident

  360,搜狗等国内浏览器: Trident + webkit

3. 内核由很多模块组成

  html,css文档解析模块 : 负责页面文本的解析

  dom/css模块 : 负责dom/css在内存中的相关处理

  布局和渲染模块 : 负责页面的布局和效果的绘制

  布局和渲染模块 : 负责页面的布局和效果的绘制

  定时器模块 : 负责定时器的管理

  网络请求模块 : 负责服务器请求(常规/Ajax)

  事件响应模块 : 负责事件的管理

### 定时器

1. 定时器真是定时执行的吗?

  定时器并不能保证真正定时执行

  一般会延迟一丁点(可以接受), 也有可能延迟很长时间(不能接受)

2. 定时器回调函数是在分线程执行的吗?

  在主线程执行的, js是单线程的

3. 定时器是如何实现的?

  事件循环模型(后面讲)

### js 的单线程

1. 如何证明js执行是单线程的?

  setTimeout()的回调函数是在主线程执行的

  定时器回调函数只有在运行栈中的代码全部执行完后才有可能执行

2. 为什么js要用单线程模式, 而不用多线程模式?

  JavaScript的单线程，与它的用途有关。

  作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。

  这决定了它只能是单线程，否则会带来很复杂的同步问题

3. 代码的分类:

  初始化代码

  回调代码

4. js引擎执行代码的基本流程

  先执行初始化代码: 包含一些特别的代码   回调函数(异步执行)

  ​	设置定时器

  ​	绑定事件监听

  ​	发送ajax请求

  后面在某个时刻才会执行回调代码

## 事件机制

### 事件循环模型

1. 所有代码分类

  初始化执行代码(同步代码): 包含绑定dom事件监听, 设置定时器, 发送ajax请求的代码

  回调执行代码(异步代码): 处理回调逻辑

2. js引擎执行代码的基本流程:

  初始化代码===>回调代码

3. 模型的2个重要组成部分:

  事件(定时器/DOM事件/Ajax)管理模块

  回调队列

4. 模型的运转流程

  执行初始化代码, 将事件回调函数交给对应模块管理

  当事件发生时, 管理模块会将回调函数及其数据添加到回调列队中

  只有当初始化代码执行完后(可能要一定时间), 才会遍历读取回调队列中的回调函数执行

### Web Workers

1. H5规范提供了js分线程的实现, 取名为: Web Workers

2. 相关API

  Worker: 构造函数, 加载分线程执行的js文件

  Worker.prototype.onmessage: 用于接收另一个线程的回调函数

  Worker.prototype.postMessage: 向另一个线程发送消息

3. 不足

  worker内代码不能操作DOM(更新UI)

  不能跨域加载JS

  不是每个浏览器都支持这个新特性

## Object.defineProperty

直接在对象上定义新属性，或者修改对象上的现有属性，并返回对象。

```javascript
let data = {
 name:'尚硅谷',
 address:'北京',
}

//创建一个监视的实例对象，用于监视data中属性的变化
const obs = new Observer(data)  
console.log(obs) 

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
```

# ES6

## 数据声明

### let：声明变量

用法与 var 类似

```javascript
let a;
let b,c,d;
let e = 100;
let f = 521, g = 'iloveyou', h = [];
```

特点：

1. 变量不能重复声明
2. 块儿级作用域  全局, 函数, eval
3. 不存在变量提升
4. 不影响作用域链

### const：声明常量

1. 一定要赋初始值

    ```
    const A;
    ```

2. 一般常量使用大写(潜规则)
  ```
  const a = 100;
  ```
3. 常量的值不能修改
  ```
  SCHOOL = 'ATGUIGU';
  ```
4. 块儿级作用域
5. 对于数组和对象的元素修改, 不算做对常量的修改, 不会报错
  ```
  const TEAM = ['UZI','MXLG','Ming','Letme'];
  TEAM.push('Meiko');
  ```

## 结构赋值

ES6 允许按照一定模式从数组和对象中提取值，对变量进行赋值，这被称为解构赋值。

1. 数组的结构

```
const F4 = ['小沈阳','刘能','赵四','宋小宝'];
let [xiao, liu, zhao, song] = F4;
console.log(xiao);
console.log(liu);
console.log(zhao);
console.log(song);
```

2. 对象的解构

```
const zhao = {
    name: '赵本山',
    age: '不详',
    xiaopin: function(){
    	console.log("我可以演小品");
    }
};

let {name, age, xiaopin} = zhao;
console.log(name);
console.log(age);
console.log(xiaopin);
```

## 模板字符串

```
ES6 引入新的声明字符串的方式 `abc`
```

1. 声明

    ```
    let str = `我也是一个字符串哦!`;
    // console.log(str, typeof str);
    ```

2. 内容中可以直接出现换行符

    ```
    let str = `<ul>
                <li>沈腾</li>
                <li>玛丽</li>
                <li>魏翔</li>
                <li>艾伦</li>
                </ul>`;
    ```

3. 变量拼接

    ```
    let lovest = '魏翔';
    let out = `${lovest}是我心目中最搞笑的演员!!`;
    console.log(out);
    ```

## 给函数的参数赋默认值

1. 形参初始值 具有默认值的参数, 一般位置要靠后(潜规则)

    ```
    function add(a,c=10,b) {
        return a + b + c;
    }
    let result = add(1,2);
    console.log(result);
    ```

2. 与解构赋值结合

    ```
    function connect({host="127.0.0.1", username,password, port}){
        console.log(host)
        console.log(username)
        console.log(password)
        console.log(port)
    }
    connect({
        host: 'atguigu.com',
        username: 'root',
        password: 'root',
        port: 3306
    })
    ```

## 对象的简化写法

ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁

```
let name = '尚硅谷';
let change = function(){
    console.log('我们可以改变你!!');
}

const school = {
    name,
    change,
    improve(){
        console.log("我们可以提高你的技能");
    }
}
console.log(school);
```

## 箭头函数

ES6 允许使用「箭头」（=>）定义函数。

```
// 声明一个函数
let fn = function(){

}
let fn = (a,b) => {
    return a + b;
}
// 调用函数
let result = fn(1, 2);
console.log(result);
```

**注意点 :**

- 如果形参只有一个，则小括号可以省略
- 函数体如果只有一条语句，则花括号可以省略的 返回值为该函数体如果只有一条语句，则花括号可以省略的 返回值为该函数体如果只有一条语句，则花括号可以省略的 返回值为该执行结果
- ==箭头函数 this 指向声明时所在作用域下 this 的值==
- 箭头函数不能作为构造实例化
- 不能使用 arguments

> 注意：箭头函数不会更改 注意：箭头函数不会更改 注意：箭头函数不会更改 this指向，用来定回调函数会非常合适 指向，用来定回调函数会非常合适 指向，用来定回调函数会非常合适 指向，用来定回调函数会非常合适 指向，用来定回调函数会非常合适 指向，用来定回调函数会非常合适

## rest 参数

ES6引入 rest参数，用于获取 参数，用于获取 函数的实参，用来代替 arguments

rest 参数非常适合不定个函的场景

```javascript
/** 作用与 arguments 类似 **/ 
function add(...args){ 
	console.log(args); 
} 
add(1,2,3,4,5); 
/** rest 参数必须是最后一个形参 **/ 
function minus(a,b,...args){ 
	console.log(a,b,args); 
} 
minus(100,1,2,3,4,5,19);
```

## spread 扩展运算符

扩展运算符（ spread）也是三个点，它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参序列，对进行解包。

```javascript
//声明一个数组 ...
const tfboys = ['易烊千玺','王源','王俊凯'];
// => '易烊千玺','王源','王俊凯'

// 声明一个函数
function chunwan(){
    console.log(arguments);
}

chunwan(...tfboys);// chunwan('易烊千玺','王源','王俊凯')
```

## Symbol

ES6 引入了一种新的原始数据类型 Symbol，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，是一似于字符串的数据类型。

> 注意：遇到唯一性的场景时要想 Symbol

特点：

- Symbol 的值是唯一，用来解决命名冲突问题 的值是唯一，用来解决命名冲突问题
- Symbol 值不能与其他数据进行运算 值不能与其他数据进行运算
- Symbol 定义的对象属性不能使用 `for…in` 循环遍历，但是可以使用 Reflect.ownKeys 来获取对象的所有键名 来

```javascript
//创建Symbol
let s = Symbol();
// console.log(s, typeof s);
let s2 = Symbol('尚硅谷');
let s3 = Symbol('尚硅谷');
//Symbol.for 创建
let s4 = Symbol.for('尚硅谷');
let s5 = Symbol.for('尚硅谷');
```

### Symbol内置属性

除了定义自己使用的 Symbol 值以外， ES6 还提供了还提供了 11个内置的 Symbol值，指向语言内部使用的方法。可以称这些方法为魔术方法，因它们会在特定的场景下自动执行。

![image-20221105234422108](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20221105234422108.png)

## 迭代器

遍历器 (Iterator) 就是一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作。

> 注意：需要自定义遍历数据的时候要想到迭代器

ES6 创造了一种新的遍历命令 for.of 循环，Iterator 接口主要供 for.of 消费

原生具备 iterator 接口的数据（可用for of遍历）：Array、Arguments、Set、Map、String、TypedArray、NodeList

### 工作原理

创建一个指针对象，指向当前数据结构的起始位置

第一次调用对象的 next 方法，指针自动指向数据结构的第一个成员

接下来不断调用 next 方法，指针一直往后移动，直到指向最后一个成员

每调用 next 方法返回一个包含 value 和 done 属性的对象

## 生成器

生成器函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同

生成器其实就是一个特殊的函数

```javascript
function * gen(){
    // console.log(111);
    yield '一只没有耳朵';
    // console.log(222);
    yield '一只没有尾部';
    // console.log(333);
    yield '真奇怪';
    // console.log(444);
}

let iterator = gen();
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

代码说明：

\* 的位置没有限制。

生成器函数返回的结果是迭代器对象，调用迭代器对象的 next 方法可以得到 yield 语句后的值。

yield 相当于函数的暂停标记，也可以认为是函数的分隔符，每调用一次 next 方法，执行一段代码。

next 方法可以传递实参，作为 yield 语句的返回值。

## Promise

Promise 是 ES6 引入的异步编程的新解决方案.语法上 Promise 是一个构造函数，用来封装异步操作并可以获取其成功或失败的结果。

调用 then 方法，then方法的返回结果是 Promise 对象, 对象状态由回调函数的执行结果决定，catch 得到失败的值

如果回调函数中返回的结果是非 promise 类型的属性, 状态为成功, 返回值为对象的成功的值

```html
<script>
    //实例化 Promise 对象
    const p = new Promise(function(resolve, reject){
        setTimeout(function(){
            //
            // let data = '数据库中的用户数据';
            // resolve
            // resolve(data);

            let err = '数据读取失败';
            reject(err);
        }, 1000);
    });

    //调用 promise 对象的 then 方法
    p.then(function(value){
        console.log(value);
    }, function(reason){
        console.error(reason);
    })
    
    
    
    const p = new Promise((resolve, reject)=>{
            setTimeout(()=>{
                //设置 p 对象的状态为失败, 并设置失败的值
                reject("出错啦!");
            }, 1000)
    });
    p.catch(function(reason){
        console.warn(reason);
    });
</script>
```

## Set 集合

ES6 提供了新的数据结构 Set(集合)。它类似于数组，但成员的值都是唯一的，集合实现了 iterator 接口，所以可以使用扩展运算符和 `for...of.` 进行遍历

集合的属性和方法：

- size：返回集合的元素个数
- add：增加一个新元素，返回当前集合
- delete：删除元素，返回boolean值
- has：检测集合中是否包含某个元素，返回boolean值
- clear：清空集合，返回undefined

## Map

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合。但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

Map 也实现了 iterator 接口，所以可以使用「扩展运算符」和for...of.』进行遍历。

Map 的属性和方法：

- size：返回Map的元素个数
- set：增加一个新元素，返回当前Map
- get：返回键名对象的键值
- has：检测Map中是否包含某个元素，返回boolean值
- clear：清空集合，返回undefined

## Class 类

ES6 提供了更接近传统语言的写法，引入了 Class(类) 这个概念，作为对象的模板。通过 class 关键宇可以定义类。基本上 ES6 的 class 可以看作只是
一个语法糖，它的绝大部分功能，ES5 都可以做到，新的 class 写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。

用法：

- class 声明类
- constructor 定义构造函数初始化
- extends 继承父类
- super 调用父级构造方法
- static 定义静态方法和属性
- 父类方法可以重写

```javascript
//手机
function Phone(brand, price){
    this.brand = brand;
    this.price = price;
}

//添加方法
Phone.prototype.call = function(){
    console.log("我可以打电话!!");
}

//实例化对象
let Huawei = new Phone('华为', 5999);
Huawei.call();
console.log(Huawei);

//class
class Shouji{
    //构造方法 名字不能修改
    constructor(brand, price){
        this.brand = brand;
        this.price = price;
    }

    //方法必须使用该语法, 不能使用 ES5 的对象完整形式
    call(){
        console.log("我可以打电话!!");
    }
    
    get price(){
        console.log("价格属性被读取了");
        return 'iloveyou';
    }

    set price(newVal){
        console.log('价格属性被修改了');
    }
    
    //静态属性
    static name = '手机';
    static change(){
        console.log("我可以改变世界");
    }
}

let onePlus = new Shouji("1+", 1999);

console.log(onePlus);
```

类的继承：

```javascript
<script>
    class Phone{
        //构造方法
        constructor(brand, price){
            this.brand = brand;
            this.price = price;
        }
        //父类的成员属性
        call(){
            console.log("我可以打电话!!");
        }
    }

    class SmartPhone extends Phone {
        //构造方法
        constructor(brand, price, color, size){
            super(brand, price);// Phone.call(this, brand, price)
            this.color = color;
            this.size = size;
        }

        photo(){
            console.log("拍照");
        }

        playGame(){
            console.log("玩游戏");
        }

        call(){
            console.log('我可以进行视频通话');
        }
    }

    const xiaomi = new SmartPhone('小米',799,'黑色','4.7inch');
    xiaomi.call();
    xiaomi.photo();
    xiaomi.playGame();
</script>
```

## 数值的扩展

```javascript
//0. Number.EPSILON 是 JavaScript 表示的最小精度，EPSILON 属性的值接近于 2.2204460492503130808472633361816E-16
function equal(a, b){
    if(Math.abs(a-b) < Number.EPSILON){
        return true;
    }else{
        return false;
    }
}
console.log(0.1 + 0.2 === 0.3);
console.log(equal(0.1 + 0.2, 0.3))
//1. 二进制和八进制
let b = 0b1010;
let o = 0o777;
let d = 100;
let x = 0xff;
console.log(x);
//2. Number.isFinite  检测一个数值是否为有限数
console.log(Number.isFinite(100));
//3. Number.isNaN 检测一个数值是否为 NaN 
console.log(Number.isNaN(123)); 
//4. Number.parseInt Number.parseFloat字符串转整数
console.log(Number.parseInt('5211314love'));
console.log(Number.parseFloat('3.1415926神奇'));
//5. Number.isInteger 判断一个数是否为整数
console.log(Number.isInteger(5));
//6. Math.trunc 将数字的小数部分抹掉  
console.log(Math.trunc(3.5));
//7. Math.sign 判断一个数到底为正数 负数 还是零
console.log(Math.sign(100));
```

## 对象的扩展

Object.is 判断两个值是否完全相等 

```javascript
console.log(Object.is(120, 120));// === 
console.log(Object.is(NaN, NaN));// === 
console.log(NaN === NaN);// === 
```

Object.assign 对象的合并

```javascript
const config1 = {
    host: 'localhost',
    port: 3306,
    name: 'root',
    pass: 'root',
    test: 'test'
};
const config2 = {
    host: 'http://atguigu.com',
    port: 33060,
    name: 'atguigu.com',
    pass: 'iloveyou',
    test2: 'test2'
}
console.log(Object.assign(config1, config2));
```

Object.setPrototypeOf 设置原型对象  Object.getPrototypeof

```javascript
const school = {
 name: '尚硅谷'
}
const cities = {
 xiaoqu: ['北京','上海','深圳']
}
Object.setPrototypeOf(school, cities);
console.log(Object.getPrototypeOf(school));
console.log(school);
```

## 模块化

模块化是指将一个大的程序文件，拆标分成许多小的文件，然后将小文件组合起来。

优势：

- 防止命名冲突
- 代码复用
- 高维护性

语法：模块功能主要由两个命令构成：export 和 import。

- export 命令用于规定模块的对外接口
- import 命令用于输入其他模块提供的功能

### 提供对外接口：

分别暴露

```
export let school = '尚硅谷';

export function teach() {
    console.log("我们可以教给你开发技能");
}
```

统一暴露

```
let school = '尚硅谷';

function findJob(){
    console.log("我们可以帮助你找工作!!");
}

export {school, findJob};
```

默认暴露

```
export default {
    school: 'ATGUIGU',
    change: function(){
        console.log("我们可以改变你!!");
    }
}
```

### 模块引入

```javascript
import * as m1 from "./m1.js";
import * as m2 from "./m2.js";
import * as m3 from "./m3.js";

m1.teach();
m2.findJob();
m3.default.change();
```

# ES7

## 数组的 includes 方法

includes 方法用来检测数组中是否包含某个元素，返回布尔类型值

```
const mingzhu = ['西游记','红楼梦','三国演义','水浒传'];
console.log(mingzhu.includes('西游记'));
```

## 指数操作符

在 ES7中引入指数运算符「**」，用来实现幂运算功能与 Math.pow 相同

```
console.log(2 ** 10);
console.log(Math.pow(2, 10));
// 这两个功能一样
```

# ES8

## async 和 await

async 和 await 两种语法结合可以让异步代码像同一样

### async 函数

async 函数的返回值为 promise 对象，
promise 对象的结果由 async 函数执行的返回值决定

```javascript
//async 函数
async function fn(){
    // 返回一个字符串
    // return '尚硅谷';
    // 返回的结果不是一个 Promise 类型的对象, 返回的结果就是成功 Promise 对象
    // return;
    //抛出错误, 返回的结果是一个失败的 Promise
    // throw new Error('出错啦!');
    //返回的结果如果是一个 Promise 对象
    return new Promise((resolve, reject)=>{
        resolve('成功的数据');
        // reject("失败的错误");
    });
}

const result = fn();

//调用 then 方法
result.then(value => {
    console.log(value);
}, reason => {
    console.warn(reason);
})
```

### await 表达式

await 必须写在 async 函数中

await 右侧的表达式一般为 promise 对象

await 返回的是 promise 成功的值

await 的 promise 失败了，就会抛出异常，需要通过 try...catch，捕获处理

```javascript
//创建 promise 对象
const p = new Promise((resolve, reject) => {
    // resolve("用户数据");
    reject("失败啦!");
})

// await 要放在 async 函数中.
async function main() {
    try {
        let result = await p;
        console.log(result);
    } catch (e) {
        console.log(e);
    }
}
//调用函数
main();
```

## Object 对象的方法

获取对象所有的键：Object.keys(school)

获取对象所有的值：Object.values(school)

获取对象所有的键值对：Object.entries(school)

获取对象属性的描述对象：Object.getOwnPropertyDescriptors(school)

```
//声明对象
const school = {
    name:"尚硅谷",
    cities:['北京','上海','深圳'],
    xueke: ['前端','Java','大数据','运维']
};

//获取对象所有的键
console.log(Object.keys(school));
//获取对象所有的值
console.log(Object.values(school));
//entries
console.log(Object.entries(school));
//创建 Map
const m = new Map(Object.entries(school));
console.log(m.get('cities'));

//对象属性的描述对象
console.log(Object.getOwnPropertyDescriptors(school));

// const obj = Object.create(null, {
//     name: {
//         //设置值
//         value: '尚硅谷',
//         //属性特性
//         writable: true,
//         configurable: true,
//         enumerable: true
//     } 
// });
```

# ES9

## Rest Spread 属性

Rest 参数与 spread 扩展运算符在 ES6 中已经引入，不过 ES6 中只针对于数组，在 ES9 中为对象提供了像数组一样的 rest 参数和扩展运算符

```html
<script>
    //rest 参数
    function connect({host, port, ...user}){
        console.log(host);
        console.log(port);
        console.log(user);
    }

    connect({
        host: '127.0.0.1',
        port: 3306,
        username: 'root',
        password: 'root',
        type: 'master'
    });


    //对象合并
    const skillOne = {
        q: '天音波'
    }
    const skillTwo = {
        w: '金钟罩'
    }
    const skillThree = {
        e: '天雷破'
    }
    const skillFour = {
        r: '猛龙摆尾'
    }
    const mangseng = {...skillOne, ...skillTwo, ...skillThree, ...skillFour};
    console.log(mangseng)
    // ...skillOne   =>  q: '天音波', w: '金钟罩'

</script>
```

## 正则表达式命名捕获组

```javascript
//声明一个字符串
let str = '<a href="http://www.atguigu.com">尚硅谷</a>';

//提取 url 与 『标签文本』
const reg = /<a href="(.*)">(.*)<\/a>/;

//执行
const result = reg.exec(str);

console.log(result);
console.log(result[1]);
console.log(result[2]);
```

## 正则表达式反向断言

```
<script>
    //声明字符串
    let str = 'JS5211314你知道么555啦啦啦';
    
    //正向断言
    const reg = /\d+(?=啦)/;
    const result = reg.exec(str);

    //反向断言
    const reg = /(?<=么)\d+/;
    const result = reg.exec(str);
    console.log(result);
</script>
```

## 正则表达式 dotAll 模

正则表达式中点 `.` 匹配除回车外的任何单字符，标记『s』改变这种行为，允许终止符出现

```html
<script>
    //dot  .  元字符  除换行符以外的任意单个字符
    let str = `
    <ul>
        <li>
            <a>肖生克的救赎</a>
            <p>上映日期: 1994-09-10</p>
        </li>
        <li>
            <a>阿甘正传</a>
            <p>上映日期: 1994-07-06</p>
        </li>
    </ul>`;
    //声明正则
    // const reg = /<li>\s+<a>(.*?)<\/a>\s+<p>(.*?)<\/p>/;
    const reg = /<li>.*?<a>(.*?)<\/a>.*?<p>(.*?)<\/p>/gs;
    //执行匹配
    let result;
    let data = [];
    while(result = reg.exec(str)){
        data.push({title: result[1], time: result[2]});
    }
    //输出结果
    console.log(data);


</script>
```

# ES10

## Object.fromEntries

```javascript
//二维数组
const result = Object.fromEntries([
    ['name','尚硅谷'],
    ['xueke', 'Java,大数据,前端,云计算']
]);

//Map
const m = new Map();
m.set('name','ATGUIGU');
const result = Object.fromEntries(m);
```

## trimStart 和 trimEnd 

删除前后多余空格

```
<script>    
    // trim
    let str = '   iloveyou   ';

    console.log(str);
    console.log(str.trimStart());
    console.log(str.trimEnd());
</script>
```

## flat 和 flatMap

```
//flat 将多维数组转化为低位数组
const arr = [1,2,3,4,[5,6]];
const arr = [1,2,3,4,[5,6,[7,8,9]]];
//参数为深度 是一个数字
console.log(arr.flat(2));  

//flatMap
const arr = [1,2,3,4];
const result = arr.flatMap(item => [item * 10]);
console.log(result);
```

## description

输出 Symbol 的值

```
//创建 Symbol
let s = Symbol('尚硅谷');

console.log(s.description);
```

# ES11

## 对象的私有属性

```html
<script>
    class Person{
        //公有属性
        name;
        //私有属性
        #age;
        #weight;
        //构造方法
        constructor(name, age, weight){
            this.name = name;
            this.#age = age;
            this.#weight = weight;
        }

        intro(){
            console.log(this.name);
            console.log(this.#age);
            console.log(this.#weight);
        }
    }

    //实例化
    const girl = new Person('晓红', 18, '45kg');

    // console.log(girl.name);
    // console.log(girl.#age);
    // console.log(girl.#weight);

    girl.intro();
</script>
```

## Promise.allSettled

Promise 同时成功，得到返回值

```html
<script>
    //声明两个promise对象
    const p1 = new Promise((resolve, reject)=>{
        setTimeout(()=>{
            resolve('商品数据 - 1');
        },1000)
    });

    const p2 = new Promise((resolve, reject)=>{
        setTimeout(()=>{
            resolve('商品数据 - 2');
            // reject('出错啦!');
        },1000)
    });

    //调用 allsettled 方法
    // const res = Promise.allSettled([p1, p2]);
    
    const res = Promise.all([p1, p2]);

    console.log(res);

</script>
```

## matchAll

```html
<script>
    let str = `<ul>
        <li>
            <a>肖生克的救赎</a>
            <p>上映日期: 1994-09-10</p>
        </li>
        <li>
            <a>阿甘正传</a>
            <p>上映日期: 1994-07-06</p>
        </li>
    </ul>`;

    //声明正则
    const reg = /<li>.*?<a>(.*?)<\/a>.*?<p>(.*?)<\/p>/sg

    //调用方法
    const result = str.matchAll(reg);
    const arr = [...result];
    console.log(arr);
</script>
```

## 可选链操作符

```html
<script>
    // ?.
    function main(config){
        // const dbHost = config && config.db && config.db.host;
        const dbHost = config?.cache?.username;		//admin

        console.log(dbHost)
    }

    main({
        db: {
            host:'192.168.1.100',
            username: 'root'
        },
        cache: {
            host: '192.168.1.200',
            username:'admin'
        }
    })
</script>
```

## 动态 import

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>动态 import </title>
</head>
<body>
    <button id="btn">点击</button>
    <script src="./js/app.js" type="module"></script>
</body>
</html>
```

## BigInt

```
<script>
    //大整形
    // let n = 521n;
    // console.log(n, typeof(n));

    //函数
    // let n = 123;
    // console.log(BigInt(n));
    // console.log(BigInt(1.2));

    //大数值运算
    let max = Number.MAX_SAFE_INTEGER;
    console.log(max);
    console.log(max + 1);
    console.log(max + 2);

    console.log(BigInt(max))
    console.log(BigInt(max) + BigInt(1))
    console.log(BigInt(max) + BigInt(2))
</script>
```

## globalThis

指定为 window 对象

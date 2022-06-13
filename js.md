# JavaScript

## 1.调试

1. 浏览器控制台打印变量

```javascript
console.log(var_name)
```

## 2.数据类型

### number

```js
//不区分小数和整数
NaN //not a number
Infinity //无限大
```

### string

'abc'    "abc"

#### 转义字符

```js
\'    //转义引号
\n    //换行
\t    //制表符
\u    //unicode字符
\x    //Ascii字符
```

#### 多行打印

```js
let msg = `p1
           p2
           p3`
```

#### 模拟字符串

```js
let name = 'jqn';
let age = 3;
let msg = `你好，${name}`;    //注意不是引号
```

<mark>字符串不可变</mark>

#### 字符串截取

```js
name.substring(0, 2)    //返回jq,包头不包尾
name.indexOf("a")    //返回指定字符的索引     
```

### 布尔值

true,    false

### 逻辑运算

```js
&&
||
!
```

### 比较运算

```js
=     //赋值
==    //类型不一样，值一样，返回true
===    //绝对等于
```

<mark>NaN和所有数值都不相等，包括自己，可以使用 isNaN(变量) 判断是否为NaN</mark>

### 数组

<mark>可以包含任意数据类型</mark>

```js
var arr = [1, 2, "string", true];    //可以通过下标取值，赋值
arr.length    //赋值会改变长度
arr.indexOf(2)    //获取索引
arr.slice()    //类似substring()
push()    //尾部
pop()    //尾部
unshift()    //压入头部
shift()    //弹出头部
sort()    //排序
reverse()     //翻转
concat()    //拼接
arr = [[1, 2], [3, 4]];    //支持多维数组
```

### 对象（若干个键值对）

<mark>JS中的所有键都是字符串，值是任意对象</mark>

<mark>数组是中括号，对象是大括号</mark>

```js
//Person person = new Peson();
//Java

//JS
var person = {
    name: "jqn",
    age: 3,
    tags: ['js', 'web', '...']
}
//取值 person.name
//赋值 person.name = "j"
delete person.name    //删除对象的属性
person.score = 99    //添加属性
'age' in person    //属性是否在对象中
'toString' in person    //方法是否在对象中，可能是继承自父类的方法
```

## 3.严格模式

```js
'use strict'        //写在script脚本第一行
```

- 局部变量建议使用let代替var （ES6标准）

## 4.流程控制

<mark>if(), while(), for()和JAVA相同</mark>

**forEach循环**

```js
let age = [1, 2, 3];


age.forEach(function (value){
    console.log(value);
})
```

**for...in**

```js
//for(var index in object){}
for(var num in age){
    if(age.hasOwnProperty(num)){
        console.log(age[num)    //注意num是下标
    }
}
```

**for...of**

```js
let arr = [1,2,3];
for(let i of arr){
    console.log(i);
}
```

## 5.Map和Set

### Map

```js
var map = new Map([['tom', 100], ['jack', 90], ['jqn', 101]]);
var name = map.get('tom');
map.set('admin', 23);
map.delete('gg');
```

### Set(无序不重复的集合)

```js
let set = new Set([1, 2]);
set.add(3);
set.delete(1);
set.has(2);
```

### 遍历

<mark>for...of   (iterator)</mark>

## 6.函数

### 定义函数

> 方式一

```js
function abs(x){
    if(x > 0){
        return x;
    }else{
        return -x;
    }
}
```

<mark>没有执行return 返回undefined</mark>

> 方式二

```js
var abs = function(x){
    if(x > 0){
        return x;
    }else{
        return -x;
    }
}
```

### 调用函数

```js
abs(3)
abs(-3)
```

### 参数问题

JS函数可以不传递参数，也可以传递多个参数

```js
function abs(x){
    if(typeof x !== 'number'){    //手动抛出异常
        throw 'Not a number'
    }
    if(x > 0){
        return x;
    }else{
        return -x;
    }
}
```

### arguments关键字

**arguments关键字表示传递进来的所有参数，是一个数组**

```js
function abs(x){
    for(let i = 0; i < arguments.length; i++){
        console.log(arguments[i]);
    }
    if(x > 0){
        return x;
    }else{
        return -x;
    }
}
```

### rest关键字

用来获取已经定义的参数外的所有参数

```js
function abs(x, y, ...rest){
    console.log(rest);
    if(x > 0){
        return x;
    }else{
        return -x;
    }
}
```

<mark>rest参数必须写在最后，用... 标识</mark>

### 变量的作用域

#### 局部变量

##### var

JS中，var定义的变量有作用域

<mark>函数体内声明，函数体外不能使用</mark>

```js
function test(){
    var x = 1;
}
x = x - 1;    //报错
```

<mark>**作用域和JAVA基本相同**</mark>

> 和JAVA不同的地方

```js
function test(){
    var x = 1;
    x = x + y;
    console.log(x);
    var y = 9;
}
```

不会报错，因为提升了y的声明，但是没有提升y的赋值

建议把要使用的变量首先声明

```js
function test(){
    var x, y, z;
}
```

##### let

```js
function test(){
    for(var i = 0; i < 10; i++){
        console.log(i);
    }
    console.log(i + 1);    //i可以使用，但是值不对
}


function test(){
    for(let i = 0; i < 10; i++){
        console.log(i);
    }
    console.log(i + 1);    //会报错
}
```

<mark>建议用let定义局部作用域的变量</mark>

#### 全局变量

##### 全局对象window

```js
var x = '123';
alert(x);
alert(window.x);
```

<mark>alert()这个函数本身也是一个window变量</mark>

```js
var x = '123';
var old_alert = window.alert;

window.alert = function (){};    //重新定义了alert

window.alert(213);    //无法生效

window.alert = old_alert;    //赋初始值

window.alert(76567);    //正常
```

> 规范

由于我们的所有全局变量都会绑定到window上，如果不同的js文件，使用了相同的全局变量，就会有冲突。

```js
//唯一全局变量
var Jqn = {};

//定义全局变量
Jqn.name = 'jqn';
Jqn.add = function(a, b){
    return a + b;
}
```

<mark>把自己定义的代码全部放入自己定义的唯一空间</mark>

#### 常量

```js
const PI = 3.14;
PI = 3,4;    //报错
```

### 方法

> 方法就是把函数放在对象的里面，对象只有两个东西：属性和方法

```js
var test = {
    name: 'jqn',
    birth: 2000,
    age: function(){
        var now = new Date().getFullYear();
        return now - this.birth;
    }
}
//调用,注意有括号
test.age()
```

#### apply()

> 控制this指向

```js
function getAge(){
    var now = new Date().getFullYear();
    return now - this.birth;
}


var test = {
    name: 'jqn',
    birth: 2000,
    age: getAge
}

//test.age()    ok
getAge.apply(test, [])    //ok
```

## 7.内部对象

### Date

**基本使用**

```js
var now = new Date();    //Sat May 21 2022 14:42:46 GMT+0800 (中国标准时间)
now.getTime();    //时间戳，世界统一，1653115366487
console.log(new Date(1653115366487))    //时间戳转换为时间
```

**转换**

```js
now.toLocaleString()
'2022/5/21 14:42:46'
now.toGMTString()
'Sat, 21 May 2022 06:42:46 GMT'
```

### Json

在JS中一切皆为对象，任何JS支持的对象都可以用JSON表示

- 对象用{}

- 数组用[]

- 所有键值对都是用 key:value

JSON和JS对象的转化

```js
var test = {
    name: 'jqn',
    age: 22
}

var json = JSON.stringify(test);    //对象转换为JSON

var obj = JSON.parse('{"name":"jqn","age":22}');    //JSON转换为对象
```

## 8.面向对象

### 原型

```js
var test = {
    name: 'jqn',
    age: 22,
    run: function (){
         console.log("我可以飞")
    }
}

var jqn1 = {
    name: 'jqn1',
    age: 4
}

jqn1.__proto__ = test;

jqn1.run();
```

### class关键字（ES6）

定义一个类，属性，方法

```js
class Student{
    constructor(name){
        this.name = name;
    }
    hello(){
        alert("hello");
    }
}


var jqn = new Student("jqn");
jqn.hello()
```

### 继承

```js
class Stu extends Student{
    constructor(name, grade) {
        super(name);
        this.grade = grade;
    }

    getGrade(){
        alert(this.grade);
    }
}

var jqn = new Stu('jqn', 100);


//本质：查看对象原型 __proto__
```

___

## 9.操作BOM对象（浏览器对象）

> window 浏览器窗口
> 
> navigator 封装了浏览器信息（不建议使用，因为可以人为修改）
> 
> screen 屏幕
> 
> location 当前页面的URL信息
> 
> document 当前页面的HTML信息

```js
 <dl id={"app"}>
     <dt>1</dt>
     <dd>a</dd>
     <dd>b</dd>
 </dl>

<script>
    var dl = document.getElementById("app");
    var ck = document.cookie; 
</script>
```

> history 浏览器历史记录，back(), forward()

## 10.操作DOM对象（文档对象）

### 获得DOM结点

```js
document.getElementByTagName();
document.getElementByClassName();
document.getElementById();


.children    //子节点
```

### 修改DOM结点

```js
<div id = "id1">

</div>

//先获取
<script>
    var id1 = document.getElementById("id1");
</script>
```

```js
id1.innerText = '123';    //修改文本的值
id1.innerHTML = '<strong>aa</strong>';    //解析HTML标签
```

```js
id1.style.color = "red";    //操作CSS
```

### 删除结点

<mark>删除结点的步骤：先找到父节点，再通过父节点删除自己</mark>

```js
<div id = 'father'>
    <h1>h1</h1>
    <p id = 'p1'>p1</p>
    <p class = 'p2'>p2</p>
</div>


<script>
    var self = document.getElementById("p1");
    var father = self.parentElement;
    father.removeChild(self);

    //也可以通过数组删除,注意这样删除数组下标是变化
    father.removeChild(father.children[0]);
</script>
```

### 插入结点

```js
<p id = 'gg'>gg</p>

<div id = 'father'>
     <h1>h1</h1>
     <p id = 'p1'>p1</p>
     <p class = 'p2'>p2</p>
</div>


<script>
    var gg = document.getElementById('gg');
    var father = document.getElementById('father');
    father.appendChild(gg);
</script>
```

> 创建一个新的标签

```js
var newtag = document.createElement('p');
newtag.id = 'newtag';
newtag.innerText = 'hello';

//另一种方式
var test = document.createElement('script');
test.setAttribute('type', 'text/javascript');
```

## 11.操作表单

<mark>表单的目的：提交数据</mark>

```js
<form>
<p>
     <span>用户名：</span> <input type="text" id="username">
</p>

<p>
     <span>性别：</span>
     <input type="radio" name = 'sex' value="man" id="man">男;
     <input type="radio" name = 'sex' value="woman" id="woman">女;
</p>
</form>

<script>
    var username = document.getElementById('username');
    var mancheck = document.getElementById('man');
</script>
username.value    //得到username的值
mancheck.checked    //man是否被选中
```

### MD5加密

```html
<script src="https://cdn.bootcdn.net/ajax/libs/blueimp-md5/2.19.0/js/md5.js"></script>
```

```js
<form action="https://www.biadu.com/" method="post" onsubmit="return submitPwd()">
    <p>
           <span>用户名：</span> <input type="text" id="username" name="username">
    </p>
    <p>
           <span>密码：</span> <input type="password" id="input-password">
    </p>
    <input type="hidden" id="md5-pwd" name="password">
        <button type="submit">提交</button>
    </form>


    <script>

    function submitPwd(){
        alert('已提交');
        var uname = document.getElementById('username');
        var pwd = document.getElementById('input-password');
        var md5pwd = document.getElementById('md5-pwd');

        md5pwd.value = md5(pwd.value);
        return true;
       }

    </script>
```

<mark>network里面formdata查看MD5密码</mark>

## JQuery

> 百度CDN

```html
<script src="http://code.jquery.com/jquery-2.1.1.min.js"></script>
```

<mark>公式：$(selector).action()</mark>

```js
<a href="" id="test">点我</a>
<script>
    $('#test').click(function (){
        alert('hello');
    })
</script>
```

### 选择器

```js
$('p').click();    //标签选择器
$('#id1').click();    //id选择器
$('.class1').click();    //class选择器
```

[JQuery文档](https://jquery.cuishifeng.cn/index.html)



### 事件

鼠标事件，键盘事件

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="http://code.jquery.com/jquery-2.1.1.min.js"></script>

    <style>
        #testdiv{
            width: 500px;
            height: 500px;
            border: 1px solid coral;
        }
    </style>
</head>

<body>

    mouse:<span id="test"></span>
    <div id="testdiv">
        移动鼠标
    </div>
    <script>
        $(function (){
            $('#testdiv').mousemove(function (e){
                $('#test').text("x:"+e.pageX + "  y:"+e.pageY)
            })
        })
    </script>
</body>
```

### 操作DOM

#### 节点文本操作

```html
<body>

<ul id="test_ul">
     <li class="java">java</li>
     <li name="py">python</li>
</ul>
<script>
    $('#test_ul li[name=py]').text();    //获取
    $('#test_ul li[name=py]').text(213)    //改变
    $('#test_ul li[name=py]').html();    //获取
    $('#test_ul li[name=py]').html('<strong>333</strong>');    //改变
</script>
</body>
```

#### css操作

```js
$('#test_ul li[class=java]').css('color','red')   //改变颜色
```



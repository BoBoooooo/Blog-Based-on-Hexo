---
title: var,const,let
date: 2018-05-04 17:23:55
tags: [var,let,const]
categories: 前端
---
> ES6提供了let和const两种新的变量声明方式，使得在JS中变量的声明更像java那样。

## ES6的块级作用域
- let声明变量与var的区别
- 死区
- const与对象冻结
- let与var
- 块级作用域

>在ES5中存在一个 很经典的循环事件绑定的问题，我们可以使用数组模拟dom集合来还原这个问题：
<!--more-->
```javascript

var arr = [];

for(var i = 0; i <6; i++){
    arr.push(function () {
        console.log(i);
    });
}

arr[0]();
arr[1]();
arr[2]();
```
不难理解，arr[X]()输出的都会是5，因为在ES5中不存在块级作用域的概念，在for循环的括号中声明的变量就像在外面声明的变量那样，每执行一次循环，新的i值就会覆盖旧的i值，导致最后输出的是以后一轮循环的i值。为了更好的让你理解，请看下面的情况：

```javascript

let arr = [];

for(var i = 0; i <6; i++){
    arr.push(function () {
        console.log(i);
        i++; // 注意这里
    });
}

arr[0](); // =>6
arr[1](); // =>7
arr[2](); // =>8
```
可见，之所以上一组代码全部输出6是因为arr中的所有函数共享一个i。

### 我们可以配合闭包来解决这个问题：

```javascript

for(var i = 0; i <6; i++){
    (function(i){
        arr.push(function () {
            console.log(i);
        });
    }(i));
}
```
而在ES6中，我们可以使用let声明变量来处理这个问题。
let的用法与var类似，但是其声明的变量只在let命令所在的代码块中有效。

```javascript

let arr = [];

for(let i = 0; i <6; i++){
        arr.push(function () {
            console.log(i);
            i++;
        });
}

arr[0]();
arr[0](); // 注意这里输出1
arr[1]();
arr[2]();
```
上面的代码中，i只在本轮循环中有效，每一次循环的i其实都是一个新的变量，于是最后输出0,1,1,2。可见，不同的变量i通过闭包保存到了各个函数中。

块级作用域的出现也使得广泛使用的匿名立即执行函数不再必要了。
```javascript

(function(){
    var a = 10;
    ... ...
}())

// 等价于

{
    let a = 10;
}
```
### 阮一峰大神在ES6入门经典中举了这样一个例子
```javascript

function foo () {
    console.log('I am the outside one')
};

(function(){
    if(false){
        function foo() {
            console.log('I am the inside one')
        }
    }

    foo();
}());
```
#### ES6中，函数本身的作用域在其所在块级作用域之内，所以立即执行函数里的function虽然存在向上整体提升效果，但只能上浮到if语句块，所以最后运行结果输出inside。但在ES5中，很最后会输出outside，因为不存在if块级作用域的限制。

但这个特性很容易引起冲突，因为我们很难判断我们代码的运行环境究竟在哪里，是遵循ES5的法则还是遵循ES6的法则（即使使用babel转码，babel也很难判断按照哪个法则来）。所以当这段代码运行在nodejs环境中的时候，编译器会选择直接报错，而并不像理论上分析得到的结果那样。

我们应该尽量规避上面那种情况，使用严格模式。在严格模式下，函数必须定义在顶级作用域，定义在if，for语句中会直接报错。

## 不存在变量提升与死区
使用let声明的变量不会出现像var那样存在“变量提升”现象。但本质上，二者是相同的，它们都会在初始化时先初始化属性，再初始化逻辑，然而二者的区别在于使用let声明的变量虽然一开始就存在，但是不能使用，而使用var声明的变量则可以。一定要在声明后使用，否则将会报错。JS不像java那样对不同作用域的同名变量有严格的控制。比如下面的代码在java里无法运行，因为存在两个名字叫做foo的局部变量：
```javascript

String foo = 'foo'
if(true){
    String foo = 'foo bar';
    System.out.print(foo);
}
```
然而在JS里上面的写法却是允许的，实际上，if语句里面的foo变量不受花括号的限制，它顶替了外部的foo:
```javascript

var foo = 'foo';
if(true){
    console.log(foo); // foo
    var foo = 'foo bar'
    console.log(foo); // foo bar
}
```
但当我们使用let声明变量时，陷阱来了，请看下面的代码：
```javascript

let foo = 'foo';
if(true){
    console.log(foo);
    let foo = 'foo bar';
    console.log(foo);
}
```
下面的代码在第一次输出foo的时候会报错，提示foo没有定义，这就是死区效应。

只要块级作用域内存在let命令，它所声明的变量就绑定在这个区域，不再受外部影响。ES6明确规定，只要块级作用域中存在let命令，则这个块区对这些命令声明的变量从一开始就形成封闭的作用域。只要声明之前，使用这些变量，就会报错。这样说有些抽象，你只需要记住：在块级作用域内如果使用let声明了某个变量，那么这个变量名必须在声明它的语句后使用，即使块外部的变量有与之重名的也不行。从块开头到声明变量的语句中间的部分，称为这个变量的“暂时性死区”。

这样也意味着我们不再能使用typeof关键字检测某个变量是否被声明了：

typeof x; // 返回'undefined'，即使x没有声明

typeof x // 与let x =10。一起使用则报错。
let x = 10;
ES6之所以如此设计，是为了减少运行时错误，防止变量在声明前使用。

为了避免死区，我提供两种方法：一是像java那样在编写代码时里层和外层尽量不重名。二是像编写传统的js代码那样，把变量在块级作用域顶层进行声明，虽然let的产生实现了java中声明变量的效果，很多人推荐使用就近原则。

不允许重复声明
let不允许在相同作用域内重复声明同一个变量，即同一个作用域内不允许出现名称相同的变量。比如下面几种形式，只能出现其中一个：

```javascript
let a = 10;
let a = 5;
var a = 15; 
function a {... ...}
const a = 25;
class a {... ...}
在处理函数形参时容易掉进陷阱：

function foo(a, b){
    {
        let b = 10; // okay，因为是子作用域
    }
    let a = a+1; // 报错
}
```
形参a作为foo作用域内的局部变量不能重复声明。

## 全局对象的属性
在ES5中，全局对象的属性和全局变量是等价的。ES6规定，使用var, function声明的全局变量依旧作为全局变量的属性存在，而使用let，const，class声明的全局变量则不属于全局变量的属性。
```javascript
var foo = 'foo';
let bar = 'bar';
foo === window.foo; // =>true
bar === window.bar; // => false
```
### const命令
const与let的特性基本相同，但顾名思义，const用于声明常量，一旦声明，必须立即赋值，且以后不可更改。

注意，使用const声明对象的时候，只能保证对象的引用地址不被更改，并非此对象不被修改。
```javascript
const foo = {nickname:'John Doe'}
foo.nickname = 'Jane'; //okay
foo.age = 25; // okay
foo = {nickname:'Kyle Hu'} // 报错，因为改变了引用关系
```
如果你真的想保证你的对象绝对安全，可以使用Object.freeze方法：
```javascript
let foo = {nickname:'John Doe'};
Object.freeze(foo);
foo.nickname = 'Jane'; //no change
foo.age = 25; // no change
```
但即使这样做，当对象中的某个属性是引用数据类型的时候也必须要小心，因为它们仍然可以被改变：
```javascript

let foo = {nickname:'John Doe', bar:{gender:'boy'}};
Object.freeze(foo);
foo.nickname = 'Jane'; //no change
foo.bar = {type:'animal'}; // no change
foo.bar.gender = 'girl'; // changed
···
所以，对象的属性也应该被冻结。通过深度变量对象，可以实现对整个对象的冻结：
```javascript

let foo = {nickname: 'John Doe', bar: {gender: 'boy'}};
v
let constantize = (obj) => {
    Object.freeze(obj);
    Object.keys(obj).forEach((key) => {
        if(obj[key]&&typeof obj[key] === 'object'){
            constantize(obj[key])
        }
    });
};
```
```javascript

constantize(foo);
foo.nickname = 'Jane'; //no change
foo.bar = {type:'animal'}; // no change
foo.bar.gender = 'girl'; // no changed
console.log(foo);
```
此外，Object还提供了其它两个用来冻结对象的方法，它们的威力依次增强：Object.preventExtensions()使得对象不能增加新属性；Object.seal()使得对象既不能增加新属性也不能删除属性。当然Object.freeze()威力最强大，使得对象的属性既不能增加，也不能修改，更不能删除。

### 注意与小结
ES5只有两种声明变量的方法，即var和function。在ES6中，又新增加了4种，分别是：let，const，class和import。

let可以完全取代var，因为二者作用几乎相同，且let没有任何副作用。在let和const之间，优先使用const，尤其是只应该设置常量的全局环境。大部分的函数一旦定义就不会改变（除了使用初始化分支的方式覆写函数的时候），所以，我们一般推荐使用const来声明一个函数。最后，V8只在严格模式下支持let和const的声明方式。

## 最后再对let/const和var的区别进行一下汇总，使用let声明的变量：

- 隶属于块级作用域，块级作用域外不可见
- 不存在“变量提升”
- 同一作用域内不得存在名称相同的变量
- 当声明为全局变量时不会作为全局对象的属性
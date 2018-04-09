---
title: 对比Vue及Jquery
date: 2018-04-09 20:40:29
tags: [vue , jquery]
categories: 前端
---
>文章整理自网络
>前言：很多人说jquey和vue没有什么可比的，应该和Angular，React来比吧，我到觉得他们倒没有多大的可比性，都是基于mvvm思想设计的框架，无非就是实现的方式不一样，在不同场景下性能上会有一些差异。然而从jquery到vue或者说是到mvvm的转变则是一个思想想的转变，是将原有的直接操作dom的思想转变到操作数据上去，难道不是一个根本性的改变吗？

 

## jquery介绍
想必大家都用过jquery吧，这个曾经也是现在依然最流行的web前端js库，可是现在无论是国内还是国外他的使用率正在渐渐被其他的js库所代替，随着浏览器厂商对HTML5规范统一遵循以及ECMA6在浏览器端的实现，jquery的使用率将会越来越低

 

## vue介绍：
vue是一个兴起的前端js库，是一个精简的MVVM。从技术角度讲，Vue.js 专注于 MVVM 模型的 ViewModel 层。它通过双向数据绑定把 View 层和 Model 层连接了起来，通过对数据的操作就可以完成对页面视图的渲染。当然还有很多其他的mvmm框架如Angular，React都是大同小异，本质上都是基于MVVM的理念。 然而vue以他独特的优势简单，快速，组合，紧凑，强大而迅速崛起
<!--more-->
 

## vue和jquey对比
jQuery是使用选择器（$）选取DOM对象，对其进行赋值、取值、事件绑定等操作，其实和原生的HTML的区别只在于可以更方便的选取和操作DOM对象，而数据和界面是在一起的。比如需要获取label标签的内容：$("lable").val();,它还是依赖DOM元素的值。

Vue则是通过Vue对象将数据和View完全分离开来了。对数据进行操作不再需要引用相应的DOM对象，可以说数据和View是分离的，他们通过Vue对象这个vm实现相互的绑定。这就是传说中的MVVM。

 

## 举例说明

* 场景一：列表添加一个元素,下图为vue和jquery两种操作的代码，我们从中可以看出vue只需要向数据message里面push一条数据即可完成添加一个li标签的操作，而jquery则需要获取dom元素节点，并对dom进行添加一个标签的操作，如果dom结构特别复杂，或者添加的元素非常复杂，则代码会变得非常复杂且阅读性低

#### vue:
``` HTML
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul>
            <!--根据数组数据自动渲染页面-->
            <li v-for="item in message">{{item}}</li>
        </ul>
        <button @click="add">添加数据</button>
    </div>
</body>

<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: ["第1条数据","第2条数据"],
            i:2
        },
        methods:{
            //向数组添加一条数据即可
            add:function(){
                this.i++
                this.message.push("第"+this.i+"条数据")
            }
        }
    })
</script>
```

#### jquery:
``` HTML
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul id="list">
            <li>第1条数据</li>
            <li>第2条数据</li>
        </ul>
        <button id="add">添加数据</button>
    </div>

</body>

<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
<script>
    $(document).ready(function() {  
    var i=2;
    $('#add').click(function() { 
       i++; 
       //通过dom操作在最后一个li元素后手动添加一个标签
      $("#list").children("li").last().append("<li>第"+i+"条数据</li>")
    });  
  }); 
</script>
 ```

* 场景二：控制按钮的显示隐藏，下图为vue和jquery两种操作的代码，我们从中可以看出vue只需要控制属性isShow的值为true和false即可，而jquery则还是需要操作dom元素控制按钮的显示和隐藏

#### vue:
``` HTML
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul>
            <!--根据数组数据自动渲染页面-->
            <li v-for="item in message">{{item}}</li>
        </ul>
        <button @click="add" v-show="isShow">添加数据</button>
        <button @click="showButton">隐藏按钮</button>
    </div>
</body>

<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: ["第1条数据","第2条数据"],
            i:2,
            isShow:true
        },
        methods:{
            //向数组添加一条数据即可
            add:function(){
                this.i++
                this.message.push("第"+this.i+"条数据")
            },
            //控制isShow的值即可
            showButton:function(){
                this.isShow=false;
            }
        }
    })
</script>
 ```

#### jquery：
``` HTML
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul id="list">
            <li>第1条数据</li>
            <li>第2条数据</li>
        </ul>
        <button id="add">添加数据</button>
        <button id="showButton">隐藏按钮</button>
    </div>

</body>

<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
<script>
    $(document).ready(function() {  
    var i=2;
    $('#add').click(function() { 
       i++; 
       //通过dom操作在最后一个li元素后手动添加一个标签
      $("#list").children("li").last().append("<li>第"+i+"条数据</li>")
    });  
    //需要手动隐藏dom元素
    $("#showButton").click(function(){
        $("#add").hide()
    })
  }); 
</script>
```

# 总结：
内容讲的比较浅，主要就是分析一下vue和jquey对比的区别，上面两个例子只是做了一个简单的说明，然而vue能解决的问题远比这些要多的多，复杂的多。

vue适用的场景：复杂数据操作的后台页面，表单填写页面

jquery适用的场景：比如说一些html5的动画页面，一些需要js来操作页面样式的页面

然而二者也是可以结合起来一起使用的，**vue侧重数据绑定**，**jquery侧重样式操作**，动画效果等，则会更加高效率的完成业务需求
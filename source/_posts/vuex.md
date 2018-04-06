---
title: vuex相关
date: 2018-04-06 22:23:46
tags: [vuex, vue]
categories: 前端
---
# Vuex是什么
* * *
首先对于vuex是什么，我先引用下官方的解释。


> Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。


就我的直观理解 vuex类似于维护了一个全局的 Map对象。你可以往里存放 key-value。然后所有的state数据操作都方法化，保证操作的可追踪和数据的干净。

# Vuex应用场景
* * *

> 其实对于vuex的应用场景一开始我有点误区，因为我把它当做了一个从始至终类似于 localstorage的存在。后来发现一刷新页面，vuex中的state存放的数据会丢失。因为它只是在当前页面初始化生成的一个实例，你一刷新页面所有数据重新生成，数据就没了。

**所以，vuex只能用于单个页面中不同组件（例如兄弟组件）的数据流通。**
<!--more-->
想必大家在想项目中啥情况会用到vuex吧。
官方是说到了时间你自然知道啥时候用了，因为小项目加入vuex，代码成本比较高，你得写各种action，mutation，dispatch交互。你自个儿都会恶心掉。

只有项目大了，组件多了，你需要一个状态机来解决同一个页面内不同组件之间的数据交流。
就比如说我下面例子中的 todolist中，todo输入框是一个组件，todolist展示框也是一个组件，他们同属于一个页面，你用传统的 event bus是很不方便的解决这个问题的。

还有就是子组件想改变父组件的情况下，就比如我们最近做的一个项目里的动态表单，其中一个是做了弹出框选择职业类，选完还得回填到父组件，以前的方式，你可能需要写很多的event bus去拦截事件，现在你可以用vuex去很清晰的解决这个问题，修改vuex里的值，父组件自动更新。

# Vuex基础概念
vuex中涉及的概念主要有下面几点，下面做个简单的介绍和理解。

> Vuex 官方文档：https://vuex.vuejs.org/zh-cn/

## State

vuex的单一状态树，使用一个对象就包含了应用层的所有状态。
我的理解是，state是vuex自己维护的一份状态数据。数据的格式需要你根据业务去设计哟~~
下面是我简单设计的todolist的state状态树。
![State](https://sfault-image.b0.upaiyun.com/179/255/1792558890-58d9ed10daec1)

## Getters
> getters属性主要是对于state中数据的一种过滤，属于一种加强属性。比如你在做一个todolist，对于已完成的，你可以写一个doneTodoList的属性，在外面直接调用。其实他就是对于action和mutations的一个简化。不然你写一个doneTodoList功能，你还得写对应的action和mutation，费劲啊。

**所以，总结一下，一些简单或通用的操作可以抽取到getters上来，方便在应用中引用。**

![Getters](https://sfault-image.b0.upaiyun.com/346/112/3461121646-58d9edbe30718)

## Actions
action,动作。
对于store中数据的修改操作动作在action中提交。
其实action和mutation类似，但是action提交是mutation，并不直接修改数据，而是触发mutation修改数据。
![Actions](https://sfault-image.b0.upaiyun.com/677/942/677942980-58d9ee1c67f69)

## Mutations
更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。
mutation中写有修改数据的逻辑。
另外mutation里只能执行*同步操作*。

![Mutations](https://sfault-image.b0.upaiyun.com/774/275/774275205-58d9ede387d62)

## Module
module,模块化。
因为随着后面的业务逻辑的增多，把vuex分模块的开发会使得代码更加简洁清晰明了，比如登录一个模块，产品一个模块，这样后面改动起来也简单嘛。
下图的 todo目录就是一个module，下面的 actions.js,mutations.js就和外面的一样。

![Module](https://sfault-image.b0.upaiyun.com/346/638/3466384750-58d9ee81eff60)

# 代码实践
* * *
## 引入vuex依赖
```
npm install vuex
```
## 目录结构
![vue](https://sfault-image.b0.upaiyun.com/379/310/3793109630-589550c0e95c7)

> store.js 将vuex维护的所有数据导出供外部使用。

> mutation_type.js 维护操作类型的常量字段

## main.js加载
![vue](https://sfault-image.b0.upaiyun.com/720/060/720060467-589552842c9bf)

## 页面使用
- 读取store里的值：

this.$store.state.字段名
如果有moudle的话，假设叫 login,那么取值又要变了，加上module名
this.$store.state.login.mobile

- 发起操作请求:

this.$store.dispatch('action中的方法名' , '参数');
参数你可以随便传json

- getters的用法

this.$store.getters.filterDoned
filterDoned 是在todo 里写的一个getters方法，就这么调用
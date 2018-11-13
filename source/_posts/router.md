---
title: 前端路由的前世今生
date: 2018-11-13 14:48:32
tags: [vue , jquery]
categories: 前端
---
# 什么是路由

> 原文地址:https://github.com/hwen/blogsome/issues/2

路由这个概念最先是后端出现的。在以前用模板引擎开发页面时，经常会看到这样
``` javascript
 // http://hometown.xxx.edu.cn/bbs/forum.php
```
有时还会有带.asp或.html的路径，这就是所谓的SSR(Server Side Render)，通过服务端渲染，直接返回页面。

### 其响应过程是这样的

* 1.浏览器发出请求

* 2.服务器监听到80端口（或443）有请求过来，并解析url路径

* 3.根据服务器的路由配置，返回相应信息（可以是 html 字串，也可以是 json 数据，图片等）

* 4.浏览器根据数据包的Content-Type来决定如何解析数据

 简单来说路由就是用来跟后端服务器进行交互的一种方式，通过不同的路径，来请求不同的资源，请求不同的页面是路由的其中一种功能。

# 前端路由的诞生的缘由

前端路由的出现要从 ajax 开始，为什么？且听下面分析 (ˉ▽￣～)

Ajax，全称 Asynchronous JavaScript And XML，是浏览器用来实现异步加载的一种技术方案。在 90s 年代初，大多数的网页都是通过直接返回 HTML 的，用户的每次更新操作都需要重新刷新页面。及其影响交互体验，随着网络的发展，迫切需要一种方案来改善这种情况。

1996，微软首先提出 iframe 标签，iframe 带来了异步加载和请求元素的概念，随后在 1998 年，微软的 Outloook Web App 团队提出 Ajax 的基本概念（XMLHttpRequest的前身），并在 IE5 通过 ActiveX 来实现了这项技术。在微软实现这个概念后，其他浏览器比如 Mozilia，Safari，Opera 相继以 XMLHttpRequest 来实现 Ajax。（😭 兼容问题从此出现，话说微软命名真喜欢用X，MFC源码一大堆。。）不过在 IE7 发布时，微软选择了妥协，兼容了 XMLHttpRequest 的实现。

有了 Ajax 后，用户交互就不用每次都刷新页面，体验带来了极大的提升。

但真正让这项技术发扬光大的，(｡･∀･)ﾉﾞ还是后来的 Google Map，它的出现向人们展现了 Ajax 的真正魅力，释放了众多开发人员的想象力，让其不仅仅局限于简单的数据和页面交互，为后来异步交互体验方式的繁荣发展带来了根基。

而异步交互体验的更高级版本就是 SPA（那么问个问题，异步交互最高级的体验是什么？会在文末揭晓）—— 单页应用。单页应用不仅仅是在页面交互是无刷新的，连页面跳转都是无刷新的，为了实现单页应用，所以就有了前端路由。

单页应用的概念是伴随着 MVVM 出现的。最早由微软提出，然后他们在浏览器端用 Knockoutjs 实现。但这项技术的强大之处并未当时的开发者体会到，可能是因为 Knockoutjs 实现过于复杂，导致没有大面积的扩散。

同样，这次接力的选手依然是 Google。Google 通过 Angularjs 将 MVVM 及单页应用发扬光大，让前端开发者能够开发出更加大型的应用，职能变得更大了。（不得不感慨，微软 跟 Google 都是伟大的公司）。随后都是大家都知道的故事，前端圈开始得到了爆发式的发展，陆续出现了很多优秀的框架。


# 从 vue-router 来看前端路由实现原理
前端路由的实现其实很简单。

本质上就是检测 url 的变化，截获 url 地址，然后解析来匹配路由规则。

但是这样有人就会问：url 每次变化都会刷新页面啊？页面都刷新了，JavaScript 怎么检测和截获 url？

在 2014 年之前，大家是通过 hash 来实现路由，url hash 就是类似于


> https://segmentfault.com/a/1190000011956628#articleHeader2


这种 #。后面 hash 值的变化，并不会导致浏览器向服务器发出请求，浏览器不发出请求，也就不会刷新页面。另外每次 hash 值的变化，还会触发 hashchange 这个事件，通过这个事件我们就可以知道 hash 值发生了哪些变化。

让我们来整理思路，假如我们要用 hash 的模式实现一个路由，那么流程应该是这样的。

## hash-mode

![State](https://user-images.githubusercontent.com/6712767/36025485-2ee8e22c-0dce-11e8-9af0-0b7b377a14fe.png)


hash 的实现相对来说要简单方便些，而且不用服务器来支持。

另外我们可以参考参考 vue-router 这一部分的实现（为了便于解释我简化了部分代码）

``` javascript
/**
 * 添加 url hash 变化的监听器
 */
setupListeners () {
  const router = this.router

  /**
   * 每当 hash 变化时就解析路径
   * 匹配路由
   */
  window.addEventListener('hashchange', () => {
    const current = this.current
    /**
     * transitionTo: 
     * 匹配路由
     * 并通过路由配置，把新的页面 render 到 ui-view 的节点
     */
    this.transitionTo(getHash(), route => {
      replaceHash(route.fullPath)
    })
  })
}
```

> 检测到 hash 的变化后，就可以通过替换 DOM 的方式来实现页面的更换。

14年后，因为HTML5标准发布。多了两个 API，pushState 和 replaceState，通过这两个 API 可以改变 url 地址且不会发送请求。同时还有 onpopstate 事件。通过这些就能用另一种方式来实现前端路由了，但原理都是跟 hash 实现相同的。用了 HTML5 的实现，单页路由的 url 就不会多出一个#，变得更加美观。但因为没有 # 号，所以当用户刷新页面之类的操作时，浏览器还是会给服务器发送请求。为了避免出现这种情况，所以这个实现需要服务器的支持，需要把所有路由都重定向到根页面。具体可以见：HTML5 histroy 模式

同样，我们来理清下思路，这样写起代码才更得心应手~

## html5-mode

![State](https://user-images.githubusercontent.com/6712767/36025788-4ef185a0-0dcf-11e8-9dc3-629aba9024a1.png)


这部分 vue-router 的源码，可以发现实现的思路大体也是相同的
``` javascript

export class HTML5History extends History {
  constructor (router, base) {
    super(router, base)
    /**
     * 原理还是跟 hash 实现一样
     * 通过监听 popstate 事件
     * 匹配路由，然后更新页面 DOM
     */
    window.addEventListener('popstate', e => {
      const current = this.current

      // Avoiding first `popstate` event dispatched in some browsers but first
      // history route not updated since async guard at the same time.
      const location = getLocation(this.base)
      if (this.current === START && location === initLocation) {
        return
      }

      this.transitionTo(location, route => {
        if (supportsScroll) {
          handleScroll(router, route, current, true)
        }
      })
    })
  }

  go (n) {
    window.history.go(n)
  }

  push (location, onComplete, onAbort) {
    const { current: fromRoute } = this
    this.transitionTo(location, route => {
      // 使用 pushState 更新 url，不会导致浏览器发送请求，从而不会刷新页面
      pushState(cleanPath(this.base + route.fullPath))
      onComplete && onComplete(route)
    }, onAbort)
  }

  replace (location, onComplete, onAbort) {
    const { current: fromRoute } = this
    this.transitionTo(location, route => {
      // replaceState 跟 pushState 的区别在于，不会记录到历史栈
      replaceState(cleanPath(this.base + route.fullPath))
      onComplete && onComplete(route)
    }, onAbort)
  }
}

```
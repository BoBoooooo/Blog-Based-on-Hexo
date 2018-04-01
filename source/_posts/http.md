---
title: http各类请求参数
date: 2018-03-27 14:43:39
tags: [http]
categories: 前端
---
Query String Parameters
当发起一次GET请求时，参数会以url string的形式进行传递。即?后的字符串则为其请求参数，并以&作为分隔符。

如下http请求报文头：
``` javascript
// General
Request URL: http://foo.com?x=1&y=2
Request Method: GET

// Query String Parameters
x=1&y=2
Form Data
```
当发起一次POST请求时，若未指定content-type，则默认content-type为application/x-www-form-urlencoded。即参数会以Form Data的形式进行传递，不会显式出现在请求url中。
<!--more-->
如下http请求报头：
``` javascript

// General
Request URL: http://foo.com
Request Method: POST

// Request Headers
content-type: application/x-www-form-urlencoded; charset=UTF-8

// Form Data
x=1&y=2
```
Request Payload
当发起一次POST请求时，若content-type为application/json，则参数会以Request Payload的形式进行传递（显然的，数据格式为JSON），不会显式出现在请求url中。

如下http请求报头：
``` javascript

// General
Request URL: http://foo.com
Request Method: POST

// Request Headers
content-type: application/json; charset=UTF-8

// Request Payload
x=1&y=2
```
如果希望通过Form Data的方式来传递数据，则可以通过原生方法formData()来进行数据组装，且content-type需要设置为multipart/form-data。

如下http请求报头：
``` javascript

// General
Request URL: http://foo.com
Request Method: POST

// Request Headers
content-type: multipart/form-data; charset=UTF-8

// Request Payload
------WebKitFormBoundaryAIpmgzV8Ohi99ImM
Content-Disposition: form-data; name="x"

1
------WebKitFormBoundaryAIpmgzV8Ohi99ImM
Content-Disposition: form-data; name="y"

2
------WebKitFormBoundaryAIpmgzV8Ohi99ImM--
```
其中，WebKitFormBoundaryAIpmgzV8Ohi99ImM为浏览器随机生成的boundary，作为分隔参数，作用等同于&。




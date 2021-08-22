# 跨域

本文要点：

* 什么是同源

* 什么是跨域

* CORS跨域

* JSONP跨域

## 同源

1995年，同源政策由 Netscape 公司引入浏览器。目前，所有浏览器都实行这个政策。

最初，它的含义是指，A 网页设置的 Cookie，B 网页不能打开，除非这两个网页“同源”。所谓“同源”指的是”三个相同“。

> 协议相同
> 域名相同
> 端口相同

举例来说，http://www.example.com/dir/page.html这个网址，协议是http://，域名是www.example.com，端口是80（默认端口可以省略），它的同源情况如下：
> http://www.example.com/dir2/other.html：同源
> http://example.com/dir/other.html：不同源（域名不同）
> http://v2.www.example.com/dir/other.html：不同源（域名不同）
> http://www.example.com:81/dir/other.html：不同源（端口不同）
> https://www.example.com/dir/page.html：不同源（协议不同）

* 目的是为了保护用户信息的安全，防止恶意的网站窃取数据

## 跨域

当一个请求url的协议、域名、端口三者之间任意一个与当前页面url不同即为跨域

目前，如果非同源，共有三种行为受到限制：
> 1.无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB。
> 2.无法接触非同源网页的 DOM。
> 3.无法向非同源地址发送 AJAX 请求（可以发送，但浏览器会拒绝接受响应）

## CORS

CORS 是一个 W3C 标准，全称是“跨域资源共享”（Cross-origin resource sharing）。它允许浏览器向跨域的服务器，发出XMLHttpRequest请求，从而克服了 AJAX 只能同源使用的限制。

* CORS的两种请求

CORS请求分为两类：简单请求(simple request)和非简单请求(not-so-simple request)

只要同时满足以下两大条件，就属于简单请求

1.请求方法是以下三种方法之一
> HEAD
> GET
> POST

2.HTTP的头信息不超出一下几种字段
>Accept
>Accept-Language
>Content-Language
>Last-Event-ID
>Content-Type:只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain

凡是不同时满足上面两个条件，就属于非简单请求。一句话，简单请求就是简单的 HTTP 方法与简单的 HTTP 头信息的结合。

### 简单请求

* 基本流程

对于简单请求，浏览器直接发出 CORS 请求。具体来说，就是在头信息之中，增加一个Origin字段。

下面是一个例子，浏览器发现这次跨域 AJAX 请求是简单请求，就自动在头信息之中，添加一个Origin字段。
```javascript
GET /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```
上面的头信息中，Origin字段用来说明，本次请求来自哪个域（协议 + 域名 + 端口）。服务器根据这个值，决定是否同意这次请求。

如果Origin指定的源，不在许可范围内，服务器会返回一个正常的 HTTP 回应。浏览器发现，这个回应的头信息没有包含Access-Control-Allow-Origin字段（详见下文），就知道出错了，从而抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。注意，这种错误无法通过状态码识别，因为 HTTP 回应的状态码有可能是200。

如果Origin指定的域名在许可范围内，服务器返回的响应，会多出几个头信息字段。
```javascript
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```
上面的头信息之中，有三个与 CORS 请求相关的字段，都以Access-Control-开头。

（1）Access-Control-Allow-Origin

该字段是必须的。它的值要么是请求时Origin字段的值，要么是一个*，表示接受任意域名的请求。

（2）Access-Control-Allow-Credentials

该字段可选。它的值是一个布尔值，表示是否允许发送 Cookie。默认情况下，Cookie 不包括在 CORS 请求之中。设为true，即表示服务器明确许可，浏览器可以把 Cookie 包含在请求中，一起发给服务器。这个值也只能设为true，如果服务器不要浏览器发送 Cookie，不发送该字段即可。

（3）Access-Control-Expose-Headers

该字段可选。CORS 请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个服务器返回的基本字段：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma。如果想拿到其他字段，就必须在Access-Control-Expose-Headers里面指定。上面的例子指定，getResponseHeader('FooBar')可以返回FooBar字段的值。

## JSONP

Jsonp(JSON with Padding) 是 json 的一种"使用模式"，可以让网页从别的域名（网站）那获取资料，即跨域读取数据。

适用范围：
> 1.可以是两个完全不同的域
> 2.只支持HTTP请求中的GET方式
> 3.老式浏览器全部支持
> 4.需要服务端支持

JSONP的基本思想是：先在网页上添加一个script标签，设置这个script标签的src属性用于向服务器请求JSON数据 ，需要注意的是，src属性的查询字符串一定要加一个callback参数，用来指定回调函数的名字 。而这个函数是在资源加载之前就已经在前端定义好的，这个函数接受一个参数并利用这个参数做一些事情。向服务器请求后，服务器会将JSON数据放在一个指定名字的回调函数里作为其参数传回来。这时，因为函数已经在前端定义好了，所以会直接调用。

一个例子：
```javascript
function addScriptTag(src) {
    let script = document.createElement('script');
    script.setAttribute("type","text/javascript");
    script.src = src;
    document.body.appendChild(script);
}

window.onload = function () {
    addScriptTag('http://example.com/ip?callback=foo');//请求服务器数据并规定回调函数为foo
}

function foo(data) {
    console.log('Your public IP address is: ' + data.ip);
};
```
向服务器example.com请求数据，这时服务器会先生成JSON数据，这里是{"ip": "8.8.8.8"}，然后以JS语法的方式生成一个函数，函数名就是传递上来的callback参数的值，最后将数据放在函数的参数中返回：
```javascript
foo({
    "ip": "8.8.8.8"
});
```
## CORS与JSONP比较
> 1.CORS 与 JSONP 的使用目的相同，但是比 JSONP 更强大。JSONP 只支持GET请求
> 2.CORS 支持所有类型的 HTTP 请求。JSONP 的优势在于支持老式浏览器，以及可以向不支持 CORS 的网站请求数据。
# 闭包

MDN上对于闭包的解释如下：

一个函数和对其周围状态（lexical environment，词法环境）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是闭包（closure）。也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。

通俗来说：如果一个函数用到了外部的变量，那么这个函数加这个变量就叫闭包

代码：
```javascript
function f1(){
    let a = 1
    function f2(){
        console.log(a)
    }
    f2()
}
f1()
//从代码上我们能看到f2函数调用了外部函数f1——a的变量，从而形成了闭包
```
## 闭包的用途

首先来看一个例子：
```javascript
let counter = 0
function add(){
    return counter += 1
}
add()
add()
add()//计数器现在为3
```
虽然我们的目的已经达到，可以这个代码有一个问题，代码中任何一个函数都可以随意改变counter的值，还需要优化

优化后的代码：
```javascript
let add = (function () {
    let counter = 0;
    return function () {
        return counter += 1
    }
})()
add()
add()
add()
```
优化后的代码使用了闭包，解决了上述代码的问题

### 闭包用途总结

* 可以从外部读取函数内部的变量
* 这些变量始终保存在内存中
* 封装对象的私有属性和私有方法

## 闭包的优缺点

* 优点：可以重复使用变量，避免全局变量的污染

* 缺点：比普通函数更占内存，会导致网页性能变差，在IE下容易造成内存泄漏

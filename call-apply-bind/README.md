# call、apply、bind 的用法分别是什么

call、apply、bind都是用来重新定义this的指向

先看一下代码：
```javascript
let obj = {name: 'tony'};
  
  function Child(name){
    this.name = name;
  }
  
  Child.prototype = {
    constructor: Child,
    showName: function(){
      console.log(this.name);
    }
  }
  var child = new Child('thomas');
  child.showName(); // thomas
  
  //  call,apply,bind使用
  child.showName.call(obj);//tony
  child.showName.apply(obj);//tony
  let bind = child.showName.bind(obj); // 返回一个函数
  bind(); // tony

```
通过上述代码了解到：

* call、apply改变了函数的this上下文后便执行了该函数

* bind方法创建一个新的函数，在 bind被调用时，这个新函数的 this 被指定为 bind的第一个参数

* 如果第一个参数为null、undefined，默认只想window
* 
## 三者之间的区别

 call与apply的区别在于参数，它们第一个参数都是改变上下文的对象。

* call从第二个参数开始以参数列表的形式展现

* apply则是把参数放在一个数组里作为他的第二个参数

* bind返回一个新的函数
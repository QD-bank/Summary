# Promise

Promise 对象用于表示一个异步操作的最终完成 (或失败)及其结果值。

## 如何创建一个new Promise

```javascript
return new Promise((resolve,reject)=>{})
//任务成功调用resolve(result)
//任务失败则调用reject(error)
//resolve和reject会再去调用成功和失败函数
ajax = (method, url, options)=>{
  return new Promise((resolve, reject)=>{
   const {success, fail} = options
   const request = new XMLHttpRequest()
   request.open(method, url)
   request.onreadystatechange = ()=>{
    if(request.readyState === 4){
     // resolve reject
     if(request.status < 400){
       resolve.call(null, request.response)
     }else if(request.status >= 400){
        reject.call(null, request)
        }
      }
    }
request.send()
  })
}
```
## 如何使用Promise.protoytpe.then

引用阮一峰ECMAScript(ES6)入门教程：

Promise 实例具有then方法，也就是说，then方法是定义在原型对象Promise.prototype上的。它的作用是为 Promise 实例添加状态改变时的回调函数。前面说过，then方法的第一个参数是resolved状态的回调函数，第二个参数（可选）是rejected状态的回调函数。

then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即then方法后面再调用另一个then方法。
```javascript
getJSON("/posts.json").then(function(json){
    return json.post
}).then(function(post){})
```
上面的代码使用then方法，依次指定了两个回调函数。第一个回调函数完成以后，会将返回结果作为参数，传入第二个回调函数。

采用链式的then，可以指定一组按照次序调用的回调函数。这时，前一个回调函数，有可能返回的还是一个Promise对象（即有异步操作），这时后一个回调函数，就会等待该Promise对象的状态发生变化，才会被调用
```javascript
getJSON("/post/1.json").then(function(post){
return getJSON(post.commentURL)
}).then(function(comments){
  console.log("resolved: ", comments)
},function(err){
  console.log("rejected: ", err)
})
```
上面代码中，第一个then方法指定的回调函数，返回的是另一个Promise对象。这时，第二个then方法指定的回调函数，就会等待这个新的Promise对象状态发生变化。如果变为resolved，就调用第一个回调函数，如果状态变为rejected，就调用第二个回调函数。

如果采用箭头函数，上面的代码可以写得更简洁:
```javascript
getJSON("/post/1.json").then(
  post => getJSON(post.commentURL)
).then(
  comments => console.log("resolved: ", comments),
  err => console.log("rejected: ", err)
)
```
## 如何使用Promise.all

Promise.all()方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。
```javascript
const p = promise.all([p1,p2,p3])
```
上面代码中，Pronmise.all()方法接收一个数组作为参数，p1，p2，p3都是Promise实例，如果不是，就会调用P肉米色。resolve()方法，将参数转为Promise实例。Promise.all()方法的参数可以不是数组，但必须具有lterator接口，且返回的每个成员都是Promise实例。

p的状态由p1、p2、p3决定，分成两种情况。

1.只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

2.只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

下面看一个具体的例子
```javascript
// 生成一个Promise对象的数组
const promises = [2, 3, 5, 7, 11, 13].map(function (id) {
  return getJSON('/post/' + id + ".json")
});
Promise.all(promises).then(function (posts) {

}).catch(function(reason){

})
```
上面代码中，promises是包含 6 个 Promise 实例的数组，只有这 6 个实例的状态都变成fulfilled，或者其中有一个变为rejected，才会调用Promise.all方法后面的回调函数。

下面是另一个例子
```javascript
const databasePromise = connectDatabase();
const booksPromise = databasePromise
  .then(findAllBooks)
const userPromise = databasePromise
  .then(getCurrentUser)
Promise.all([
  booksPromise,
  userPromise
])
.then(([books, user]) => pickTopRecommendations(books, user))
```
上面代码中，booksPromise和userPromise是两个异步操作，只有等到它们的结果都返回了，才会触发pickTopRecommendations这个回调函数。

注意，如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法。
```javascript
const p1 = new Promise((resolve, reject) => {
  resolve('hello')
})
.then(result => result)
.catch(e => e)
const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了')
})
.then(result => result)
.catch(e => e)
Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e))
// ["hello", Error: 报错了]
```
上面代码中，p1会resolved，p2首先会rejected，但是p2有自己的catch方法，该方法返回的是一个新的 Promise 实例，p2指向的实际上是这个实例。该实例执行完catch方法后，也会变成resolved，导致Promise.all()方法参数里面的两个实例都会resolved，因此会调用then方法指定的回调函数，而不会调用catch方法指定的回调函数。

如果p2没有自己的catch方法，就会调用Promise.all()的catch方法。
```javascript
const p1 = new Promise((resolve, reject) => {
  resolve('hello')
})
.then(result => result)
const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了')
})
.then(result => result);
Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e));
// Error: 报错了
```
## 如何使用Promise.race

```javascript
const p = promise.all([p1,p2,p3])
```
上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

Promise.race()方法的参数与Promise.all()方法一样，如果不是 Promise 实例，就会先调用下面讲到的Promise.resolve()方法，将参数转为 Promise 实例，再进一步处理。

下面是一个例子，如果指定时间内没有获得结果，就将 Promise 的状态变为reject，否则变为resolve
```javascript
const p = Promise.race([
  fetch('/resource-that-may-take-a-while'),
  new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('request timeout')), 5000)
  })
])
p
.then(console.log)
.catch(console.error)
```
上面代码中，如果 5 秒之内fetch方法无法返回结果，变量p的状态就会变为rejected，从而触发catch方法指定的回调函数。
# 数组去重

假设有数组 array = [1,5,2,3,4,2,3,1,3,4]
你要写一个函数 unique，使得
unique(array) 的值为 [1,5,2,3,4]

## set()方法
ES6 提供了新的数据结构 Set
它类似于数组，但是成员的值都是唯一的，没有重复的值

set()去重方法一(使用Array.from)：
```javascript
let arr = [1,5,2,3,4,2,3,1,3,4]
function unique(arr){
    return Array.from(new Set(arr))
}
unique(arr)//1,5,2,3,4
```
set()去重方法二(使用...扩展运算符)：
```javascript
let arr = [1,5,2,3,4,2,3,1,3,4]
let item = [...new Set(arr)]
console.log(item)//1,5,2,3,4
```
### 缺点

* 对象不可去重

* 兼容性问题

### 优点

* NaN去重

## 双重for循环去重
```javascript
let arr = [1,5,2,3,4,2,3,1,3,4]
function unique(arr){
  for(let i = 0; i < arr.length; i++){
    for(let j = i+1; j < arr.length; j++){
      if(arr[i] === arr[j]){
        arr.splice(j,1)
        j--
      }
    }
  }
  return arr
}
unique(arr)
```
### 缺点

* 对象不可以去重，因为对象不可以用来比较

* NaN不可以去重

### 优点

* 兼容性好
  
* '1'和1可以区分
  
## indexOf去重
```javascript
let arr = [1,5,2,3,4,2,3,1,3,4]
function unique(arr){
    if(!Array.isArray(arr)){
        console.log('type error')
        return
    }
    let array = []
      for (var i = 0; i < arr.length; i++) {
        if (array .indexOf(arr[i]) === -1) {
            array .push(arr[i])
        }
    }
    return array;
}
unique(arr)
```
### 缺点

* 对象不可以去重，因为对象不可以用来比较

* NaN不可以去重

## Map去重
```javascript
let arr = [1,5,2,3,4,2,3,1,3,4]
function unique(arr){
    let map = new Map
    let array = []
    for(let i = 0; i < arr.length; i++){
        if(map.has(arr[i])){
           map.set(arr[i],true)
        }else{
            map.set(arr[i],false)
            array.push(arr[i])
        }
    }
    return array
}
unique(arr)

//简化版
function unique(arr){
    let map = new Map()
    return arr.filter((a) => !map.has(a) && map.set(a,1))
}
```
### 缺点

*  对象不可以去重

### 优点

* NaN可以去重

## WeakMap去重
```javascript
// function unique(array){
//     let wm = new WeakMap()
//     for(let i =0; i < array.length; i++){
//         if(!wm.has(array[i])){
//             wm.set(array[i],i)
//         }
//     }
//     return wm
// }
// unique(arr)
```
老师WeakMap去重怎么做，网上也没有找到类似的例子。
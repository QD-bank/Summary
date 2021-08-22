# DOM事件

## 事件委托

事件委托就是利用事件冒泡，指定一个事件处理程序，就可以管理某一类型的所有事件

## 如何阻止默认事件

默认事件：浏览器的一些默认的行为。例如：点击超链接跳转，点击鼠标右键会弹出菜单，滑动滚轮控制滚动条……

W3C的方法是额。preventDefault(),IE使用e.returnValue = false

preventDefault它是事件对象(Event)的一个方法，作用是取消一个目标元素的默认行为。既然是说默认行为，当然是元素必须有默认行为才能被取消，如果元素本身就没有默认行为，调用当然就无效了。

```javascript
//阻止浏览器的默认行为 
function stopDefault( e ) { 
    //阻止默认浏览器动作(W3C) 
    if ( e && e.preventDefault ) 
        e.preventDefault(); 
    //IE中阻止函数器默认动作的方式 
    else 
        window.event.returnValue = false; 
    return false; 
}
```
## 如何阻止事件冒泡

e.stopPropagation()可中断冒泡，
```javascript
function stopBubble(e) { 
//如果提供了事件对象，则这是一个非IE浏览器 
if ( e && e.stopPropagation ) 
    //因此它支持W3C的stopPropagation()方法 
    e.stopPropagation(); 
else 
    //否则，我们需要使用IE的方式来取消事件冒泡 
    window.event.cancelBubble = true; 
}
```



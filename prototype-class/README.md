# JS继承

## 原型继承

子类型的原型为父类型的一个实例对象，子类继承父类的属性和方法是将父类的私有属性和公有方法都作为自己的公有属性和方法

```javascript
//父类型
function Person(name,age){
    this.name=name,
    this.age=age,
    this.play=[1,2,3]
    this.setName=function(){}
}
Person.prototype.setAge=function(){}
//子类型
function Student(price){
    this.price=price
    this.setScore=function(){}
}
Student.prototype=new Person()//子类型的原型为父类型的一个实例对象
let s1=new Student(15000)
let s2=new Student(14000)
console.log(s1,s2)
```

缺点：

* 无法实现多继承
* 来自原型对象的所有属性被所有实例共享
* 创建子类型实例，无法像父类构造函数传参
* 想成为子类新增属性和方法，必须要在student.prototype = new Preson()之后执行，不能放在构造器中

## 构造函数继承 在子类型构造函数中使用call()调用父类型构造函数
```javascript
function Person(name,age){
    this.name=name,
    this.age=age,
    this.setName=function(){}
}
Person.prototype.setAge=function(){}
function Student(name,age,price){
    Person.call(this,name,age)//相当于：this.Person(name,age)
    //this.name=name
    //this.age=age
    this.price=price
}
let s1=new Student('tom',20,15000)
console.log(s
```
缺点：

* 实列并不是父类的实例，只是子类的实例
* 只能继承父类的实例属性和方法，不能继承原型属性和方法
* 无法实现函数复用，每个子类都有父类实例函数的副本，影响性能

## class的继承通过extends关键字实现继承
```javascript
class Person{
//调用类的构造方法
    constructor(name,age){
        this.name=name
        this.age=age
    }
    showName(){
        console.log("调用父类的方法")
        console.log(this.name,this.age);
    }
}
let p1=new Person('kobe',39)
console.log(p1)
//定义一个子类
class Student extends Person{
    constructor(name,age,salary){
        super(name,age)//通过super调用父类的构造方法
        this.salary=salary
    }
    showName(){
        console.log("调用子类的方法")
        console.log(this.name,this.age,this.salary);
    }
}
let s1=new Student('wade',38,100000)
console.log(s1)
s1.showNmae()
```
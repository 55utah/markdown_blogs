#### js 原型分析 -- Object与Function的真面目！ __proto__分析

1.Object与Function
```
首先，Function是JavaScript一个特殊的构造函数，在JS中，每一个函数都是其对象（Object也是），如下：

Object.prototype处于原型链的顶端，js在Object.prototype基础上生成Function.prototype,Function.prototype这个对象称为空函数，与一般函数对象不同；在Function.prototype基础上，生成了两个构造函数：Function, Object；由此：
Function.prototype.__proto__ === Object.prototype
Function.__proto__ === Function.prototype
Object.__proto__ === Function.prototype
说明了Function与Object prototype的内部关系；
另外，
Function.prototype.prototype === undefined
Object.prototype作为原型链顶端，Object.prototype.__proto__ === null

```
2. __proto__ 与原型链遍历
```
function Name(){}
let n = new Name()
n.__proto__ === Name.prototype
n.constructor === Name

实例的__proto__指向原型对象，每个对象都将拥有一个__proto__属性；

n.__proto__.__proto__ === Object.prototype

n.__proto__.__proto__.__proto__ === null

需要注意：
一个构造函数A
A.prototype.constructor
A.prototype.__proto__ 
这两个属性不能忽视；

原型链遍历：
function instanceofTest(obj, Fn){
    let proto = Fn.prototype
    let p = obj.__proto__
    //遍历对象原型链的方法
    while(true){
        if(p === null) return false  //Object.prototype.__proto__ === null
        if(p === proto) return true
        p = p.__proto__  //向原型链上一层找
    }
}
```
--更新</br>
String, Number, Boolean 构造函数的原型链</br>
```
这几个构造函数与原型链，基础类型字面量有什么关系呢？
1. let a = 'abc', b = 12, c = true 字面量实际是String('abc'), Number(12), Boolean(true)
，方法返回直接量，而new String()这种返回的是对象！

2.String构造函数是继承谁的呢？
'12'.__proto__ === String.prototype
'12'.__proto__.__proto__ === Object.prototype
实际上，String, Number, Boolean构造函数都是继承的Object.


```

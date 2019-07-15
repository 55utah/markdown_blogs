1.所有的对象实例和函数都有 __proto__ 属性，所有的函数都有 prototype 属性;  prototype就是原型对象。
```
function A(){
	this.name = 'x'
}
A.prototype.getName = function(){return 'y'}
let b = new A()
b.getName // 'y'

A.__proto__ === Function.prototype  //true
// 构造函数的prototype有constructor属性  指向构造函数本身
A.prototype.constructor === A  //true

let obj = {} //let obj = new Object()的语法糖
obj.constructor === Object  //true
//实例对象根据原型链可以访问到构造函数的prototype上的属性， 所以有：
obj.constructor === Object.prototype.constructor === Object
//同样的：
A.constructor === Function  //true
//判断是不是一个Array   arr.constructor === Array
//是不是对象 obj.constructor === Object

// __proto__ 是浏览器才有的对象内部属性，ES6不推荐使用，应该用:Object.getPrototypeOf()
//__proto__ 属性可读取，可修改（ 对应Object.setPrototypeOf() ）
Object.getPrototypeOf(obj) === obj.__proto__ 
Object.getPrototypeOf(obj) === Object.prototype //true
```

1. typeof 与 instanceof

(1)typeof用来检测基础类型变量类型，对值类型number、string、boolean 、null 、 undefined、 以及引用类型的function的反应是精准的；但是，对于对象{ } 、数组[ ] 、null 都会返回object。
(2)instanceof  判断一个引用类型是否属于某构造函数
```
let a = {
  
a instanceof Object 
//等同于
Object.getPrototypeOf(a) === Object.prototype

//但是instanceof无法判断继承关系
function Person(){
  this.age = 10
}
let per = new Person
per instanceof Person //true
per instanceof Object //true

//如果要精确判断实例是哪个构造函数的实例，需要使用constructor
per.constructor === Person //true
per.constructor === Object //false
```

1. 原型污染

lodash出现原型污染漏洞，分析如下：
```
//lodash有如下方法
_.defaultsDeep({ 'a': { 'b': 2 } }, { 'a': { 'b': 1, 'c': 3 } })
//{ 'a': { 'b': 2, 'c': 3 } }
//触发原型污染
const payload = '{"constructor": {"prototype": {"toString": true}}}'
_.defaultsDeep({}, JSON.parse(payload))
//获得的对象原型已被修改!

//防止被修改原型的方法
(1) Object.freeze()
(2) lodash 修复方式， 判断属性是constructor, prototype等内部属性，退出.
```



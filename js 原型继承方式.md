#### js 原型继承方式
完整的继承代码：
```
function Parent(name){
    this.name = name
}
Parent.prototype.play = function(){
    console.log(this.name)
}

function Child(name){
    Parent.call(this, name)
    this.value = 'value'
}
Child.prototype = Object.create(Parent.prototype)
Child.prototype.constructor = Child
```
测试:
```
let c1 = new Child('hello')
let c2 = new Child('you')
c1.play()
c2.play()
//hello you
c1 instanceof Child  //true
c2 instanceof Parent  //true
//实现了继承
```
原理说明
```
想要继承父构造函数，可以在子构造函数内使用
Parent.call(this)
运行父类构造函数，使用call显式的将实例属性this指向子类；
这样子类就具有父类的实例属性。
想要继承父类的prototype原型属性，
(1)Child.prototype = Parent.prototype
问题是子类更改prototype，父类也会改变，不可行
(2)Child.prototype = new Parent()
这样子类原型指向的对象是父类的实例，子类原型改变不影响父类，子类实例之间也相互不影响；
但是两次调用父类构造函数，有性能浪费。
(3)
Child.prototype = Object.create(Parent.prototype)
Child.prototype.constructor = Child
这样就完美解决了问题，Object.create会根据原型参数实例化一个对象，作为子类的原型；
注意要修复一下子类的constructor.
```
其他
```
Object.create(Parent.prototype)
可以用如下方法代替
function content(obj){
    function F(){}
    F.prototype = obj
    return new F()
}

Object.create(proto[, propertiesObject])
proto 新创建对象的原型对象
propertiesObject 可选，添加到新创建对象的可枚举属性（即其自身定义的属性，而不是其原型链上的枚举属性）
返回值是一个新对象，带着指定的原型对象和属性。

let o
// 创建一个原型为null的空对象
o = Object.create(null);

o = {};
// 以字面量方式创建的空对象就相当于:
o = Object.create(Object.prototype);

o = Object.create(Object.prototype, {
  // foo会成为所创建对象的数据属性
  foo: { 
    writable:true,
    configurable:true,
    value: "hello" 
  },
  // bar会成为所创建对象的访问器属性
  bar: {
    configurable: false,
    get: function() { return 10 },
    set: function(value) {
      console.log("Setting `o.bar` to", value);
    }
  }
})


```
实现多继承
```
实现子类继承到多个对象，使用混入的方式。
function My(){
    SuperClass.call(this)
    OtherClass.call(this)
}

My.prototype = Object.create(SuperClass.prototype)
Object.assign(My.proptotype, OtherClass.prototype)
My.prototype.constructor = My

```
Object.assign(sourceObj, newObnj, newObj, ...)
```
let a = {s : 1}
console.log(Object.assign(a, {p: 2}))
console.log(a)
//{s: 1, p: 2}
//{s: 1, p: 2}

//会将属性拷贝到第一个参数对象sourceObj上，改变sourceObj, 并作为Object.assign的返回值！
```
#### instanceof 与 typeof 用法原理总结
typeof
```
判断一个值得类型，返回值如下：
'string', 'number', 'boolean', 'undefined', 'function', 'object', 'symbool' 共七种；
注意，typeof无法判断一个object对象的实际类型：
let s = new String('abc')
typeof s === 'object' //true
let p = 'nihao'
typeof p === 'string'  //true
typeof的原理：
js 在底层存储变量的时候，会在变量的机器码的低位1-3位存储其类型信息
000：对象,010：浮点数,100：字符串,110：布尔,1：整数, 000:null
注意：typeof会返回null值得类型为'object'，历史保留Bug.

使用:
最好只用typeof判断基本数据类型
可以用用toString来做比较准确的判断；
Object.prototype.toString.call(() => {}) // "[object Function]"
Object.prototype.toString.call(null) // "[object Null]"
```
instanceof
```
用于测试构造函数的prototype属性是否出现在对象的原型链中的任何位置
let Person = function(){}
let nico = new Person()
nico instanceof Person  //true

原理：
和用处一样，判断是不是在原型链的任何位置，查找到就是true；
function Foo(){}
Foo instanceof Object // true
Foo instanceof Function // true

简单实现：
function instanceofTest(left, right){
    let rightProto = right.prototype
    let leftValue = left.__proto__
    //遍历对象原型链的方法
    while(true){
        if(leftValue === null) return false  //Object.prototype.__proto__ === null
        if(leftValue === rightProto) return true
        leftValue = leftValue.__proto__  //向原型链上一层找
    }
}
实际实现考虑的更多；

```


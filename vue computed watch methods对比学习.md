#### vue computed watch methods对比学习
1.computed
```
模板支持简单计算式，但模板内太多计算会增加成本，难以未付；
计算属性就是解决模板内复杂计算的问题；
{{ getReason }}

...
computed: {
    getReason: function(){
        return this.message.split('').reverse()
    }
}

如果使用methods来解决：
{{ getReason() }}

methods: {
    getReason: xx
}
methods不适合解决这个问题。

computed基于响应式依赖进行缓存，依赖改变，才会重新求值，只要依赖不改变，每次访问
计算属性返回一个缓存结果；
computed: {
    a: function(){
        return Date.now() //结果不再更新
    }
}

//注意到计算属性与data选项下的属性在模板内都是以名称表示的，methods是以方法表示的；
//计算属性{{ getReason }} ,在更新或读取时，认为是一个方法来运行，{{ getReason }} 等同于 getReason()；

计算属性需要传参时

{{ fetchQ('good') }}

computed: {
    fetchQ: function(a){
        return function(){
            return a + 'boy'
        }
    }
}

计算属性需要返回一个函数，因为fetchQ('')解析要作为函数运行fetchQ('')()
如果不返回函数,fetchQ('')不是函数，就报错了。

计算属性可以有getter, setter

computed: {
    fullName: {
        get: function(){ return this.name },
        set: function(new){ this.name = new }
    }
}
```
watch</br>
当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。</br>
```
watch: {
     firstName: function (val) {
     this.fullName = val + ' ' + this.lastName
}

watch: {
    'a.b.c': function (newVal, oldVal) {
        // 监听对象子属性，需要引号
    })
}

可以用vm.$watch API来代替声明式的定义

选项
deep 监听对象内部属性变化
vm.$watch('someObject', callback, {
  deep: true
})

指定 immediate: true 将立即以表达式的当前值触发回调
vm.$watch('a', callback, {
  immediate: true
})  //定义的语句也会触发一次回调

```
computed与watch对比
```
1. computed是一个计算值，用来简化template内计算和处理，适合一个或多个值变化，页面模板计算后重新渲染的情景；watch是监听数据变化，适合数据变化时执行异步或开销较大的操作，处理部分更加复杂；
2. computed具有缓存能力， watch不具有。
```
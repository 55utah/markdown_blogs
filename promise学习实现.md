#### promise学习实现
1. promise
```
(1)一旦状态改变，就不会再变;
(2)注意，调用resolve或reject并不会终结 Promise 的参数函数的执行。
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});
// 2
// 1
上面代码中，调用resolve(1)以后，后面的console.log(2)还是会执行，并且会首先打印出来。这是因为立即 resolved 的 Promise是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。
(3)then方法会返回一个新的Promise实例。因此可以采用链式写法，即then方法后面再调用另一个then方法。
前一个then方法如果有返回就会被第二个then方法接收，不是promise对象会用Promise.resolve()转为promise对象传给第二个then方法；如果无return语句，会认为return undefined, 传给后面then方法Promise.resolve(undefined)
new Promise((resolve, reject)=>{
    resolve(1)
}).then(p => {console.log(p);return 2})
.then(k => {console.log(k)})
.then(m => console.log(m))

//1 2 undefined

(4)Promise回调函数的错误， then方法指定的回调函数中的错误，都会被catch方法捕获！对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获。

(5)跟try catch代码块不同的是，如果没有使用catch方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，即不会有任何反应。
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为x没有声明
    resolve(x + 2);
  });
};
someAsyncThing().catch(p => {console.log(1)})
//上述代码不会报错，正常执行，被catch捕获后不会抛出；
```
2.方法：
```
(1)Promise.all
Promise.all方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。
const p = Promise.all([p1, p2, p3])
<1>所有实例resolve，给then方法返回一个promise数组[re1, re2, re3]
<2>某实例reject，就给catch返回这个实例re1
let a = Promise.resolve(1)
let b = Promise.reject(3)
let x = Promise.all([a, b])
x.then(p => console.log(p)).catch(k => console.log(k))
//3

a = Promise.resolve(1)
b = Promise.resolve(3)
x = Promise.all([a, b])
x.then(p => console.log(p)).catch(k => console.log(k))
//[1, 3]

(2)Promise.race
const p = Promise.race([p1, p2, p3]);
哪个实例先改变状态就返回哪个实例，触发then或者catch方法回调；

(3)Promise.allSettled
所有给定的promise已fulfilled或全部rejected后返回描述每个promise的结果

(4)Promise.resolve()
将现有对象转为 Promise对象，如果是promise对象就原封不动返回；
如果是普通对象或值就返回一个resolved状态的promise对象；
Promise.resolve等价于下面的写法。
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))

(5)Promise.reject
类似Promise.resolve
```

3.标准和实现
```
各种实现都是基于Promises/A+标准， ES6的Promise也是基于这个标准；
实现参考 https://juejin.im/post/5b88df2a51882542e56e62c9

Promise是一个构造函数，实例具有then, catch方法；

基本结构：
function Promise(executor) {
    this.state = 'pending'; //状态  'pending', 'fulfilled', 'rejected'
    this.value = undefined; //成功结果
    this.reason = undefined; //失败原因
    function resolve(value) {
        
    }
    function reject(reason) {

    }
}
Promise.prototype.then = function(onFulfilled, onRejected){

}
实例化Promise，构造函数马上调用，这个从new Promise(()=>{}) 立即执行这个特点可以知道；

function Promise(executor){
    let _this = this
    this.state = 'pending'
    this.value = undefined
    this.reason = undefined

    executor(resolve, reject) //实例化 立即执行

    function resolve(value){
        if(_this.state === 'pending'){
            _this.value = value
            _this.state = 'fulfilled'
        }
    }
    function reject(reason){
        if(_this.value === 'pending'){
            _this.state = 'rejected'
            _this.reason = reason
        }
    }
}

Promise.prototype.then = function(onFulfilled, onRejected){
    if(this.state === 'fulfilled' && typeof onFulfilled == 'function'){
        onFulfilled(this.value)
    }
    if(this.state == 'rejected' && typeof onRejected == 'function'){
        onRejected(this.reason)
    }
}

目前Promise不支持异步，封装异步操作，then无法获取结果；
例如：
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(1);
    }，500);
});

p.then(data => console.log(data)); //没有任何结果
调用then方法的时候  状态还是pending, 解决办法是使用发布订阅模式；
目标：使用发布订阅让Promise支持异步，then方法链式调用支持；

function Promise(executor){
    let _this = this
    this.state = 'pending'
    this.value = undefined
    this.reason = undefined

    executor(resolve, reject) //实例化 立即执行

    function resolve(value){}
    function reject(reason){}

    this.onFulfilledArr = [] //成功的回调
    this.onRejectedArr = []  //失败回调
    ...
}

Promise.prototype.then = function(onFulfilled, onRejected){
    if (this.state === 'pending') {
        if (typeof onFulfilled === 'function') {
            this.onFulfilledFunc.push(onFulfilled);//保存回调
        }
        if (typeof onRejected === 'function') {
            this.onRejectedFunc.push(onRejected);//保存回调
        }
    }

    //链式调用，then方法一定会返回一个Promise对象
    //暂不实现，针对回调的不同返回封装成promise对象返回；
    ...
}

在状态改变时，执行这些回调！
function resolve(value){
    if(_this.state == 'pending'){
        _this.value = value
        _this.onFulfilledFunc.forEach(fn => fn(value))
        _this.state = 'fulfilled'
    }
}
function reject(reason){
    if(_this.state == 'pending'){
        _this.reason = reason
        _this.onRejectedFunc.forEach(fn => fn(value))
        _this.state = 'rejected'
    }
}

至此已经可以正常使用了，异步代码也可以运行！
但是then方法规范中是异步执行，所以使用setTimeout模拟
setTimeout(()=>{
    //此处的代码会异步执行
},0);


```
4. Promise.all实现
```
简单实现：
function all(ite){
    return new Promise((resolve, reject)=>{
        let arr = []
        let handler = () => {
            if(arr.length >= ite.length){
                resolve(arr)
            }
        }
        for(let i in ite){
            ite[i].then(re => {arr[i] = re; handler()}).catch(er => reject(er))
        }
    })
}
```
5. Promise.race实现
```
function all(ite){
    return new Promise((resolve, reject)=>{
        for(let i in ite){
            ite[i].then(re => resolve(re)).catch(er => reject(er))
        }
    })
}
```
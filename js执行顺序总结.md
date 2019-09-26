#### js执行顺序
```
JS的执行机制是
执行一个宏任务,过程中如果遇到微任务,就将其放到微任务事件队列;
当前宏任务执行完成后,如果微任务事件队列有内容,就将微任务依次执行完；
微任务队列先进先运行；

macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
micro-task(微任务)：Promise.then，process.nextTick

new Promise是立即执行的。
async 函数需要注意执行顺序。
```


例子：
```
(1)
new Promise(resolve => {
    console.log(1);
    resolve(3);
    Promise.resolve().then(()=> console.log(4))
}).then(num => {
    console.log(num)
});
console.log(2)

//1243
//调用resolve,reject，后面的代码正常运行，输出1，然后运行Promise.resolve()
//把打印4的then微任务放入微任务队列，然后外层的打印num的then微任务进入微任务队列；输出2，宏任务结束，执行微任务队列，依次输出4， 3；

(2)关于async函数执行顺序

const p = Promise.resolve();
(async () => {
  await p; console.log('after:await')
})()
p.then(() => console.log('tick:a'))
 .then(() => console.log('tick:b'))


//after:await
//tick:a
//tick:b

// 在 chrome 73 下也是这样的结果，新的chrome实现了正确的规范；
//老的chrome可能会有不同结果，未能正确实现规范，以最新的为准；

(3)加深对async函数运行顺序理解

a = new Promise(r => r(1))
b = new Promise(r => r(2))
c = new Promise(r => r(3))
async function dd(){
    let g = await a
    console.log(g)
    g = await b
    console.log(g)
    g = await c
    console.log(g)
}
setTimeout(() => console.log(66))
dd()
new Promise(r => r(1)).then(p => console.log(11))
.then(p => console.log(22))
.then(p => console.log(33))
setTimeout(() => console.log(88))

//1
//11
//2
//22
//3
//33
//66
//88

宏任务setTimeout会在所有的async await和promise.then执行结束后再执行
就是说async await和promise.then都是微任务，先后的await和链式的then一样，都会依次加入微任务队列，
所有先后await，链式then都加入后，全部执行，才会执行下一个宏任务，也就是setTimeout！

```
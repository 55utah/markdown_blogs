### 函数柯里化
##### 1. 介绍
柯里化就是一个接受多个参数的函数转化成为一个每次接受部分参数，返回一个新函数接受剩下的参数，得到全部参数后返回原函数结果.
##### 2. 效果
一般形式：<br>
```
fn(1,2,3,4,5) = xx
let _fn = curry(fn)
_fn(1,2,3,4,5) = _fn(1)(2)(3)(4)(5) = _fn(1,2)(3)(4,5) = ...
```
用途:<br>
降低通用性，提高适用性;<br>
```
//原本
function regFn(reg, string){
    return regExp.test(string)
}
regFn(/[\d]+/, '12121')
regFn(/[\d]+/, '123321')
regFn(/[\d]+/, '121d1')

//柯里化

let c = curry(regFn)
let s = c(/[\d]+/)
s('12121')
s('123321')
s('121d1')
```
通过柯里化，生成新函数，适用性提高，(参数复用能力).
```
let names = list.map(item => {
    return item.name
    })
//柯里化
let prop = curry((key, obj) => return obj[key])
let names = list.map(prop('name'))
```
提高复用性，降低嵌套，更精简！
##### 实现

```
function curry(fn, len = fn.length){
    return _curry.call(this, fn, len)
}

function _curry(fn, len, ...args){
    return function(...params){
        let _args = [...args, ...params]
        if(_args.length >= len){
            return fn.apply(this, _args)
        }else{
            return _curry(fn, len, ..._args)  //注意是_args
        }
    }
}

```

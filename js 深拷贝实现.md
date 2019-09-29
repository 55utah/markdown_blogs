#### js 深拷贝实现
参考https://juejin.im/post/5d6aa4f96fb9a06b112ad5b1
```
深拷贝，是原始对象属性值的精确拷贝，结果放在新的内存空间，
修改拷贝值不会改变原对象。

1.基础版本

function clone(target){
    const cloneTarget = {}
    if(typeof target === 'object'){
        for(let k in target){
            cloneTarget[k] = clone(target[k])
        }
        return cloneTarget
    }else{
        return target
    }
}

2.考虑数组

function clone(target){
    if(typeof target === 'object'){
        const cloneTarget = Array.isArray(target) ? [] : {}
        for(let k in target){
            cloneTarget[k] = clone(target[k])
        }
        return cloneTarget
    }else{
        return target
    }
}

3.考虑循环引用
如果不加处理，上面代码递归无法结束；
方法是将对象引用做一个记录，当又用到这个对象时，直接返回；
存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝。
使用WeakMap, WeakMap属性是对象的弱引用，对象被回收，WeakMap里这个属性就会消失，不增加引用。

function clone(target, map = new WeakMap()){
    if(typeof target === 'object'){
        const cloneTarget = Array.isArray(target) ? [] : {}
        if(map.get(target)){
            return map.get(target)
        }
        map.set(target, cloneTarget)
        for(let k in target){
            cloneTarget[k] = clone(target[k])
        }
        return cloneTarget
    }else{
        return target
    }
}

4.性能优化
循环方式，while效率最高，可以用while替换for of。

5.考虑其他数据类型

toString方法每个引用类型都有，不同对象有固定的返回；
Object.prototype.toString.call(Symbol())  // [object symbol]
...

对Map, Set拷贝 (set, add方法递归)

对不可继续遍历的对象，如Date, Number, Error, RegExp等需要创建新对象；

例如： 克隆symbol类型，
function cloneSymbol(target){
    return Object(Symbol.prototype.valueof.call(target))
}

6. 克隆函数

区分箭头函数与普通函数，箭头函数没有prototype属性
eval + 函数字符串可以生成新箭头函数拷贝；
普通函数通过， new Function()和正则 来创建新的拷贝；
function cloneFunc(func){
    const funcString = func.toString()
    const bodyReg = /(?<={)(.|\n)+(?=})/m;
    const paramReg = /(?<=\().+(?=\)\s+{)/;
    if(func.prototype){
        if(!param) return new Function(body[0])
        const params = paramReg.exec(funcString)
        const body = bodyReg.exec(funcString)
        return new Function(...params, body[0])
    }else{
        return eval(funcString)
    }
}

总结，这就是全部的深拷贝知识了！

```
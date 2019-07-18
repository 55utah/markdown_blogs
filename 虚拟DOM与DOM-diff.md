vue和react都使用了虚拟dom和dom diff，本文学习一下两者如何发挥作用；
### 实现虚拟DOM
虚拟dom就是通过js对象来虚拟一个DOM对象， DOM对象本身属性很多，很复杂，操作真实DOM成本高；可以在虚拟的DOM对象上进行改变， 然后再渲染到真实DOM上，<br>
虚拟DOM接受三个参数(type, props, children):<br>
type: 指定元素的标签类型，如'li', 'div'等,props: 表示指定元素身上的属性，如class, style, 自定义属性等, children: 表示指定元素是否有子节点，参数以数组的形式传入;
```
//创建虚拟对象类，描述DOM
class Element{
    constructor(type, props, children){
        this.type = type
        this.props = props
        this.children = children
    }
}

function createElement(type, props, children){
    return new Element(type, props, children)
}

export{
    Element,
    createElement
}
```


##### 渲染为真实DOM
```
//render将虚拟DOM转为真实DOM
function render(vNode){
    let el = document.createElement(vNode.type)
    for(let key in vNode.props){
        setAttr(el, key, vNode.props[key])
    }
    vNode.children.forEach(v => {
        let node
        if(v instanceof Element) node = render(v)
        else{
            node = document.createTextNode(v)
        }
        el.appendChild(node)
    })
    return el
}

function setAttr(el, key, value){
    //不同类型，处理不同
    ...
    else{
        el.setAttribute(key, value)
    }
}

```

#### DOM-diff算法
DOM-diff比较两个虚拟DOM区别， 然后用这个差异来更新DOM!<br/>
分为两步:<br/>
1.对比两个DOM标记差异，得到一个补丁；<br/>
2.对补丁分析，然后更新DOM;<br/>
```
比较规则
新的DOM节点不存在{type: 'REMOVE', index}
文本的变化{type: 'TEXT', text: 1}
当节点类型相同时，去看一下属性是否相同，产生一个属性的补丁包{type: 'ATTR', attr: {class: 'list-group'}}
节点类型不相同，直接采用替换模式{type: 'REPLACE', newNode}
```

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
    el.setAttribute(key, value)
    //不同类型，处理可能不同，最基本的是setAttribute
}

//测试渲染虚拟dom方法
//渲染测试
    const h = createElement
    const ul = h('ul', {}, [
        h('li', {class: 'item'}, ['hello1']),
        h('li', {class: 'item'}, ['hello2']),
        h('li', {class: 'item'}, [
            h('a', {}, ['baidu'])
        ])
    ])
    const root = ul.render()
    document.body.appendChild(root)
//测试成功

```

#### DOM-diff算法
dom节点或者结果发生变化，虚拟节点对象形成节点树结构，变化前后是两个树结构，diff就是找出代价最小的转换方法；<br/>
思路：<br/>
对新旧两树深度优先遍历，每个节点有唯一id，每遍历一个节点就把差异与原本树此节点进行对比，有差异就记录；<br/>
```
react diff策略

(1)Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计。
(2)拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
(3)对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。
分析：
(1)React 只会简单的考虑同层级节点的位置变换，而对于不同层级的节点，只有创建和删除操作。
所以，保持稳定的 DOM 结构会有助于性能的提升。
(2)比较同层的两个组件，同一类型，就比较子节点，不同类型，直接进行删除替换；
   A          A
 B    C    G     C
D E      D  E 
比如B节点与G节点，节点类型不同，整个节点和子树都会替换；
(3)elemnt diff
同层节点有三种diff操作，插入，移动，删除；
判断节点列表差异的时候，移动这一步，没有唯一key的情况下：
通过判断同一位置元素是否相等，不等就删除或插入，操作繁杂低效；
优化：通过给同层节点添加唯一key，通过新老列表是否有相同key，有就进行移动操作，
通过相同key的老旧位置，生成一个移动方案；
如果没有key，就无法得到最优的移动方案，增加了损耗，所以代码中子节点一定要有唯一key；
```


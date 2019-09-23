#### DOM、Node类型、Document类型、Element类型、Text类型等

```
DOM(文档对象类型)是HTML.XML的API,返回一个多层节点结构；
节点分12种类型；文档节点是根节点，每个文档只有一个，HTML页面中始终是<html>元素；
1. Node类型
js中所有节点类型都继承Node类型，具有基础dom节点操作方法；
appendChild(node) //末尾添加节点
insertBefore(new, referenceNode) //插入到某节点前
replaceChild(newnode, oldnode)  //替换节点
removeChild(node)
cloneNode(bool)  //返回克隆节点，true为深拷贝， false浅拷贝
2. document类型
表示整个页面，是window的属性；
nodeType=9
3.Element类型
指XML或者HTML元素，比如div, span这种；
nodeType=1
4.Text类型，文本类型
NodeType=3
为纯文本内容，无html元素；
不支持子节点

总结：
Node是接口，document,element,textNode都继承这个接口，具有不同的node类型，
document是9，element是1,textNode是3
```
性能相关
```
NodeList, HTMLColection等集合返回节点列表，访问会实时运行的查询得到最新节点信息，
应该尽量减少查询NOdeList次数，都会执行一次基于文档的查询。所以可以考虑将从NodeList中取得值缓存起来；
```
虚拟DOM与diff算法
```

在虚拟DOM和diff算法中，对节点类型有涉及；
1.创建虚拟节点：
function Element(tagNames, props, children){
    this.tagNames = tagNames
    this.props = props
    this.children = children
}

function createElement(type, props, children){
    return new Element(type, props, children)
}

//render成真实dom

Element.prototype.render = function(){
    const el = document.createElement(this.tagNames)
    const props = this.props
    const children = this.children

    for(let name in props){
        let v = props[name]
        el.setAttribute(name, v)
    }

    children.forEach(child => {
        let elementChild = child instanceof Element 
            ? child.render()
            : document.createTextNode(child)
        el.appendChild(elementChild)
    })

    return el
}

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

```
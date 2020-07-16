## DOMParser 将存储在字符串中的 XML 或 HTML 源代码解析为一个 DOM Document。

#### 用法
```
地址： https://developer.mozilla.org/zh-CN/docs/Web/API/DOMParser
浏览器支持： 非常好
用法：
（1）解析html，获取HTMLdocument对象，然后就可以像处理
const parser = new DOMParser()
const doc = parser.parseFromString(html, 'text/html')
// 获取document下所有的图片元素，可以这样做(querySelector, querySelectorAll是深度便利的)
doc.querySelectorAll('img')
// 需要删除某个元素可以这样做：
dom.parentNode.removeChild(dom)
// document可以转回html字符串，未做dom改变情况下，生成的html字符串与原本字符串完全相同！！
const parser = new DOMParser()
const doc = parser.parseFromString(html, 'text/html')
console.warn(doc.body.innerHTML === html)  // true

（2）解析svg图片
doc = parser.parseFromString(stringContainingXMLSource, "image/svg+xml");
// 返回一个 SVGDocument 对象，同时也是一个 Document 对象。
// 可以解析svg图片做元素处理

```

#### 总结
- 对html，xml字符串内容进行element模式的处理转换，使用文档对象的处理方式，可以摆脱正则直接操作html字符串的方式；
- 可以检索，增删改dom元素；
- 最重要的是可以转为html, xml字符串还原

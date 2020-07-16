## DOMParser 将存储在字符串中的 XML 或 HTML 源代码解析为一个 DOM Document。
```
地址： https://developer.mozilla.org/zh-CN/docs/Web/API/DOMParser
浏览器支持： 非常好
用法：
（1）解析html，获取HTMLdocument对象，然后就可以像处理
const parser = new DOMParser()
const doc = parser.parseFromString(html, 'text/html')
（2）解析svg图片
doc = parser.parseFromString(stringContainingXMLSource, "image/svg+xml");
// 返回一个 SVGDocument 对象，同时也是一个 Document 对象。

```

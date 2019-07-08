### 本地图片拖放到页面，截图paste粘贴到页面如何获取到图片
<br>
<span> #d 是一个div元素.</span>

```
function $(i){return document.querySelector(i)}
let i = $('#d')
i.ondragover = function(e){
		e.stopPropagation()
		e.preventDefault()
}
i.ondrop = function(e){
		e.stopPropagation()
		e.preventDefault()
		//本地图片拖曳到页面上  触发ondragover ondrop事件 都需要阻止默认行为
		console.log(e.dataTransfer.files[0])  // 获取到File
}
i.onpaste = function(e){
		let d = e.clipboardData
		console.log(d.items[0])
		//只有ctril alt A 截图 ctrl + v 粘贴可以获取到图片，直接复制本地文件粘贴无法获取到数据.
		console.log(d.items[0].getAsFile()) // 获取到File
}
```

#### 有用的webapi
```
1.元素查询
querySelector（元素向下查询，返回一个）
querySelectorAll（元素向下查询，返回多个）
closest（元素向上查询）
contains（判断是否包含指定元素）
2.
hidden（隐藏元素）
contenteditable（使元素可以被编辑）
spellcheck（检查拼音）
maxlength (输入最大长度)
3.
classList（类名控制器）
URLSearchParams（查询参数）
getBoundingClientRect（元素空间结构详细信息）
online state（网络状态）
battery state（电池状态）
vibration（设备震动）
page visibility（页面可见性）
deviceOrientation（陀螺仪）
customEvent（自定义事件）
notification（桌面通知）
fullScreen（全屏）
orientation（屏幕方向）
4.
document.execCommand
```
重点api分析
```
(1)元素查询，常用功能，closest, contains 对元素范围定位很有效；
(2)spellcheck默认开启，需要设置spellcheck=false来取消检查；
maxlength="x" 输入组件长度限制
(3)重点： customEvent（自定义事件）
document.addEventListener('own', ev => {console.log(ev.detail)})
window.dispatchEvent(new CustomEvent("own", {
  detail: {
    name: "hello"
  }
}));
//{name: 'hello'}
CustomEvent用来定义一个自定义事件, 参数两个，名称和数据对象；
(4)execCommand 之前有用过，可参考；
参考https://juejin.im/post/5d5df391e51d453b1e478ad0
```

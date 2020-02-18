flex的使用
```
flex: 1；
相当于flex: 1 1 0%;
当其他同级子元素未设置flex，则为默认的flex: 0 1 auto;
则此子元素会自动放大，占据父元素剩余的所有空间。
 (1) 左边宽度固定，右边自动铺满剩余空间
<div style='display: flex;width: 800px'>
  <div style='width: 100px'></div>
  <div style='flex: 1'>右</div>
</div>

```

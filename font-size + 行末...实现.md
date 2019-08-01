#### font-size意外的用处
1. 需求:
```
parent:

width: 100%;
height: 8px;
text-align: center;

child:
height: 8px;
width: 8px;
dispaly:inline-block;
```
2. 效果
```
  以为，这样就可以了，但是实际，样式是child与parent不在一个线上，child会下移几px，
  这是为什么呢，让child正确被parent包含也很简单，parent加一个样式: font-size: 0;
  原因是默认字符14px会让child下移！
```

#### 文字ellipsis效果条件
1.单行文字超出显示...的实现
```
  css样式：
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
```
但是经常发现这么配置后无法显示...  额外条件：
```
(1) 必须是行内元素或者块元素，宽度可设置的元素
(2) 宽度需要设置，不能是100%, 可以是80%等小于100%的宽度

```

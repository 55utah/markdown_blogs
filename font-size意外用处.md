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

#### flex布局与inline-felx布局非常好用
```
  块元素使用flex布局，行内元素使用inline-flex;
  以inline-flex为例，学习布局的情况；
  display:inline-flex的元素再外界看就是一个行内元素！
```
```
  <div class="wrap">
    <div class="content">
      <span v-for="item in [0,1,2,3,4]">{{item}}</span>
    </div>
  <div>
  
  需求是五个图标横行均匀排布，并且随窗口宽度改变自适应！
```
```
  .wrap{
    width: 100%;
  }
  .content{
    display: inline-flex;
    align-items: center;
   width: 80%;
  }
  .content span{
    flex: 1 0 auto;
  }
 // 注意， flex第一个值是felx-grow当空间足够时是否自动放大；
 定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大；
 如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
 这个非常重要！ 这里所有span flex-grow为1所以等分；
 flex-basis在分配多余空间之前，项目占据的主轴空间；
 设置为auto时，为本来应该占的空间；
 
 但是上面的出了问题， 当有字符宽度增大时，所有字符受到影响！
 解决办法就是：
 flex:1 0 30px;
 这样就指定span基础宽度是30px，然后5个都自动放大到均分80%宽度,
 单个字符宽度改变，只要不超过30px就不影响其他字符！
```

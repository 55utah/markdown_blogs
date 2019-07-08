### 1.背景:
<span>想要在vscode中使用react等js代码的自动补全，输入react.就会看到子属性, 进行选择</span>
### 2.解决办法:
<span>搜索相关插件,未找到，但是发现了**@types**这个东西， 原来这个才是vscode自动补全的核心！<span>
<br>
<span> @types 是npm的一个分支，用来存放 *.d.ts 文件，用于TypeScript类型检查等，大部分npm包，如react，安装时:</span>
```
npm i react --global
```
<span>其实还有@types/react可以安装:<span>
```
npm i @types/react --global
```
<span>实际上我们可能会单独安装@types/react.</span>
<span> 这样在vscode编辑器里面就可以使用ts有的.d.ts声明文件, 来进行自动补全， 非常方便！</span>
### 3.总结
<span>自动补全不需要什么插件，使用npm 安装 @types 模块，IDE就会有自动补全功能了;</span>
<br>
<span>当然常用的安装在global， 否则就安装 --save-dev</span>
<br>
<span>可以把常用的react, vue, node等添加@types自动补全</span>

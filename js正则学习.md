#### js正则表达式学习总结
1. 匹配规则
```
.   除了换行和行结束符之外的所有单个字符
\w   等价于[0-9a-zA-Z_], 数字之母下划线
\W   除了w以外的所有字符
\s   任何Unicode空白符
\S   任何非Unicode空白符的字符（范围比w大）
\d   等价于[0-9]
\D   除了d以外的所有字符
[...]   包含方括号内的任意字符
[^...]  反向匹配，不包含内部任何字符

{n,m}   匹配至少n次，但不能超过m次
{n,}    匹配至少n次，无上限
{n} 匹配n次
?   匹配0次或1次，等价于{0,1}
+   匹配1次或多次，无上限，等价于{1,}
*   匹配0次或多次，无上限，等价于{0,}

^   匹配字符串的开头，在多行检索中，匹配一行的开头
$   匹配字符串的结尾，在多行检索中，匹配一行的结尾
(?=p)   正向先行断言，要求接下来的字符都与p匹配，匹配结果不包含p
(?!p)   负向先行断言，要求接下来的字符不能和p匹配

选择、分组
|    选择匹配，左右表达式都可匹配
(...) 分组匹配，将几个项组合为一个单元，这个单元可以用"*","+","?","|","{n,m}"等符号加以修饰。而且可以记住和这个组合相匹配的字符
(?:...) 非捕获性分组  只负责匹配，不记忆匹配的字符
```

2.RegExp 
```
(1)两种定义：
let re = /pattern/flags
let re = new RegExp("pattern", "flags")
flags: g, i, m(多行匹配), u(支持unicode)
(2)实例属性：
global, ignoreCase， multiline, lastIndex(开始搜索的下一个匹配项的起始位置，默认0)
(3)实例方法：
RegExp.prototype.exec 
exec方法返回包含第一个匹配项信息的数组，或者在没有匹配项时返回null：
这里分两种情况，一种是global匹配，一种是非global匹配。
global模式执行exec()匹配成功一次以后，再次执行exec()时，会从前一次匹配的最后一位开始继续向后匹配。
当使用(...)分组匹配时，一次exec()会匹配到多个结果

let exp = /.at/g
let matches = exp.exec('cat, bat, sat, fat')
<!-- 
    返回： 
    {
        0: "bat"
        groups: undefined
        index: 5
        input: "cat, bat, sat, fat"
        length: 1
    } 
-->
matched.index => 0
matches => ['cat']
matches = exp.exec('cat, bat, sat, fat');  //二次匹配
matches.index => 5
matches => ['bat']

let exp = /(there)\s+(you)\s+(are)/;
let matches = exp.exec('hey, there you are my dear');
//matches = ["there you are", "there", "you", "are"];
分组匹配，返回值
matches[0]匹配整个表达式字符串
matches[1]匹配第一个(...)内的字符串，之后以此类推


test()
匹配返回true，否则返回false
```

3.模式匹配的string方法
```

字符串对象共有 4 个方法，可以使用正则表达式：match()、replace()、search()和split()。
ES6 将这 4 个方法，在语言内部全部调用RegExp的实例方法，从而做到所有与正则相关的方法，全都定义在RegExp对象上。

String.prototype.match 调用 RegExp.prototype[Symbol.match]
String.prototype.replace 调用 RegExp.prototype[Symbol.replace]
String.prototype.search 调用 RegExp.prototype[Symbol.search]
String.prototype.split 调用 RegExp.prototype[Symbol.split]

str.search(exp)
返回匹配的第一哥字符串起始位置，没有返回-1，会忽略global标识

str.replace(exp)
第一个参数是一个正则表达式，第二个参数是要替换成的字符串, 有全局匹配标识就全局匹配替换；

str.match(exp)
有global标识时，match返回全局匹配到的所有字符串组成的数组；当没有global标识时，match就返回第一个匹配到的字符串组成的单元素数组。

'11+2=13'.match(/\d/g) => ['1','1','2','1','3']
'11+2=13'.match(/\d/) => ['1']

str.split(exp)
当split方法传入的参数为正则表达式时，这使得split()方法异常强大。例如，可以指定分隔符，允许两边可以留有任意多的空白符：

'1   ,  ,2 , 3  ,   4  ,  5'.split(/\s*,\s*/) => ['1','2','3','4','5']
'1  34    64  90  0'.split(/\s*/) =>  ["1", "3", "4", "6", "4", "9", "0", "0"]

```
4 分组
```
捕获性分组  ()
非捕获性分组  (?:)

const RE_DATE = /(\d{4})-(\d{2})-(\d{2})/;
const matchObj = RE_DATE.exec('1999-12-31');
const year = matchObj[1]; 
const month = matchObj[2]; 
const day = matchObj[3];
console.log(year, month, day)
//1999 12 31

var dateStr = '2018/04/18';
var reg = /(\d{4})\/(\d{2})\/(\d{2})/;
dateStr = dateStr.replace(reg, '$1-$2-$3') //"2018-04-18"

非捕获型分组
有的时候只是为了分组并不需要捕获的情况下就可以使用非捕获型分组，例如
var reg = /(?:\d{4})-(\d{2})-(\d{2})/
var date = '2012-12-21'
reg.test(date)
RegExp.$1 // 12
RegExp.$2 // 21

```
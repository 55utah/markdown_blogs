#### node爬虫填坑总结
```
1. Error: unable to verify the first certificate in node.js
https网站提升连接不安全，证书不安全，node请求报错
是node默认不允许请求证书不安全的内容；
解决办法：

process.env['NODE_TLS_REJECT_UNAUTHORIZED'] = 0

设置node环境变量 允许不安全证书；
2. 网站接口request header里面没有cookie
部分网站设置了cookie http-only
这种的document.cookie无法获取到cookie
可以通过新开一个tab页，输入接口，F12看network，此时可以看到cookie；

3. 网站需要代理才能正确请求
node爬虫如何正确设置代理！
解决方法：
使用request模块

header要设定cookie, User-Agent等内容；
const headers = {
    'Cookie':'J_SESSION_ID="{559f851f1af8836c4981a9703919497e1c4e77}"',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36',
    'Host': 'cloudscope.xxx.com',
    'Content-Type': 'application/json'
}

 request({
            url: `https://xx.hxxx.com/hhh/node/over`, // 要访问的网站地址
            proxy: 'http://q00x999:xxxxx@proxy.xxxx.com:8080/', //用户密码proxy代理，这个是正确的格式；
            headers: headers,
            methods: "GET"
            }, function (error, response, body) {
                这里处理结果
            }
)


4. 完整的一个代码如下： 
(无需代理的情况，使用axios来请求，更加方便，还可以使用p-limit这样的模块来做并发控制)
const axios = require('axios')
const request = require('request')
const fs = require('fs')
const tree = require('./treeInfo.js')

const treeList = tree.result.reduce((h, k) => {
    if(k.level == 4 && k.cmdbId){
        let region = k.cmmName
     let p = tree.result.find(i => {return i.cmmUid == k.parentUid})
     let cmm = p.cmmName.toLowerCase()
        if(p && (cmm.includes('kafka') || cmm.includes('zookeeper'))){
            let service = p.cmmName
            h.push({cmdbId: k.cmdbId, region, service})
        }
    }
    return h
}, [])

console.log(treeList.length)

const base = "https://cloudscope.xxx.com"
const headers = {
    'Cookie': 'browserCheckResult=A; locale=zh-cn; J_SESSION_ID="{559f851f1af8836c4981a29111ce2f59703919497e1c4e77}"',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36',
    'Host': 'cloudscope.xxxx.com',
    'Content-Type': 'application/json'
}

process.env['NODE_TLS_REJECT_UNAUTHORIZED'] = 0

function fetch(id){
    return new Promise((resolve, reject) => {
        request({
                url: `https://cloudscope.xxx.com/CloudMonitorCenter/rest/v1/xxx/${id}/overview`,
                proxy: 'http://q00xxxx:xxxx@proxy.xxxx.com:8080/',
                headers: headers,
                methods: "GET"
            }, function (error, response, body) {
                if (!error && response.statusCode == 200) {
                    resolve(JSON.parse(body))
                }else{
                    reject('error')
                }
            })
    })
}


let result = []

let timer = session => {
    return new Promise(resolve => {
        let t = setTimeout(() => { clearTimeout(t); resolve() }, session)
    })
}

;(async () => {
    for(let i = 0; i < 500; i++){
        console.log(i)
        if(!treeList[i]) break
        try{
            const { data: { indicators: [...list] } } = await fetch(treeList[i].cmdbId).catch(e => {
                console.log(e)
                console.log(treeList[i])
            })
            let mem = list.find(p => p.name == 'mem_usage').value
            let cpu = list.find(p => p.name == 'cpu_usage').value
            let disk = list.find(p => p.name == 'disk_usage').value
            result.push({cpu, mem, disk, ...treeList[i]})
            if(i!=0 && i % 20 == 0){
                await timer(4000)
            }
        }catch(e){
            console.log(e)
            continue
        }
    }
    console.log(result)
    fs.writeFileSync('1.txt', JSON.stringify(result), null, 2)
})().catch(e => {
    console.log(e)
})



```
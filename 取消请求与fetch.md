#### 取消请求与fetch
1. fetch使用
```
fetch是浏览器全局的一个请求方法，比XMLHttpRequest更加优雅；
使用promise异步方式处理结果；

Promise<Response> fetch(input[, init]);

fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
})
.then(response => response.json()).catch(...)

参数说明:
credentials: 'include' ，会发送cookie等认证信息；
上传各种数据，比如上传文件，可以使用FormData
let formData = new FormData()
formData.append('avatar', fileField.files[0]);
fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData
})
.then(response => response.json())

fetch返回的是Response对象，
Response.ok 
Response.status
还有arrayBuffer()， blob()，json()，text()，formData()这些方法。

polyfill: https://github.com/github/fetch
```
2.取消请求实现
```
浏览器有AbortController实验中的方法提供了这个功能，兼容性差，手动实现一个；

function wrap(promise){
    const cancel = {}
    const promiseHandle = new Promise((resolve, reject) => {
        Object.defineProperty(cancel, 'signal', {
            set () {
                reject('Abort')
            }
        })
        promise.then(v => resolve(v)).catch(e => reject(e))
    })
    return Object.assign(promiseHandle, {
        cancel(){
            cancel.signal = true
        }
    })
}
let pro = new Promise((res) => {
    setTimeout(() => res('123'), 1000)
})
let obj = wrap(pro)
obj.then(d => console.log(d)).catch(e => console.log(e))
obj.cancel()
// Abort

这样我们就可以取消fetch的请求了， 测试一下：
const f = fetch('http://xxxx')
const pro = wrap(f)
pro.then(k => console.log(k)).catch(p => console.log(p))
pro.cancel()

//Abort
```

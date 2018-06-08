### overwrite
```js
(function() {
    var proxied = window.XMLHttpRequest.prototype.open;
    window.XMLHttpRequest.prototype.open = function() {
        console.log( arguments );
        return proxied.apply(this, [].slice.call(arguments));
    };
})();
(function(){
    var originWS = WebSocket
    WebSocket = function(url){
        if(flag){
            // ....
        }
        return new originWS(url)
    }
})
```


### retry promise
```js
function retry(pFunc,tryTime=1){
    return function(...ars){
    let time = 1
        return new Promise((r,j)=>{
            (function _retry(){
                return pFunc.apply(null,ars).catch(e=>{
                    if(time<tryTime){
                        time++
                        return _retry()  
                    }
                    j(e)
                }).then(e=>r(e))
            })()
        })
    }
}
// let fetchTry = retry(fetch)
```
### axios-retry
```js
//在main.js设置全局的请求次数，请求的间隙
axios.defaults.retry = 4;
axios.defaults.retryDelay = 1000;

axios.interceptors.response.use(undefined, function axiosRetryInterceptor(err) {
    var config = err.config;
    // If config does not exist or the retry option is not set, reject
    if(!config || !config.retry) return Promise.reject(err);
    
    // Set the variable for keeping track of the retry count
    config.__retryCount = config.__retryCount || 0;
    
    // Check if we've maxed out the total number of retries
    if(config.__retryCount >= config.retry) {
        // Reject with the error
        return Promise.reject(err);
    }
    
    // Increase the retry count
    config.__retryCount += 1;
    
    // Create new promise to handle exponential backoff
    var backoff = new Promise(function(resolve) {
        setTimeout(function() {
            resolve();
        }, config.retryDelay || 1);
    });
    
    // Return the promise in which recalls axios to retry the request
    return backoff.then(function() {
        return axios(config);
    });
});
```

### download doc file directly
```js
this.$ajax({
    method:"get",
    url:"http://localhost:8081/order/getDoc",
    responseType:'blob'
}).then((res)=>{
    //这里res.data是返回的blob对象
    var blob = new Blob([res.data], {type: 'application/vnd.openxmlformats-officedocument.wordprocessingml.document;charset=utf-8'}); //application/vnd.openxmlformats-officedocument.wordprocessingml.document这里表示doc类型
    var downloadElement = document.createElement('a');
    var href = window.URL.createObjectURL(blob); //创建下载的链接
    downloadElement.href = href;
    downloadElement.download = orderId+'.docx'; //下载后文件名
    document.body.appendChild(downloadElement);
    downloadElement.click(); //点击下载
    document.body.removeChild(downloadElement); //下载完成移除元素
    window.URL.revokeObjectURL(href); //释放掉blob对象
})
```
### copy to clipboard
```js
const copyToClipboard = str => {
  const el = document.createElement('textarea');
  el.value = str;
  el.setAttribute('readonly', '');
  el.style.position = 'absolute';
  el.style.left = '-9999px';
  document.body.appendChild(el);
  const selected =
    document.getSelection().rangeCount > 0 ? document.getSelection().getRangeAt(0) : false;
  el.select();
  document.execCommand('copy');
  document.body.removeChild(el);
  if (selected) {
    document.getSelection().removeAllRanges();
    document.getSelection().addRange(selected);
  }
};
//copyToClipboard('我最帅'); // '我最帅' copied to clipboard.
``` 

### escape  
```js
const escapeHTML = str =>
  str.replace(
    /[&<>'"]/g,
    tag =>
      ({
        '&': '&amp;',
        '<': '&lt;',
        '>': '&gt;',
        "'": '&#39;',
        '"': '&quot;'
      }[tag] || tag)
  );
  //escapeHTML('<a href="#">Me & you</a>'); // '&lt;a href=&quot;#&quot;&gt;Me &amp; you&lt;/a&gt;'
```
### encodeURI console magic  
```js
var arr = [];
for(var i=0;i<256;i++) {
  var char=String.fromCharCode(i);
  if(encodeURI(char)!==encodeURIComponent(char)) {
    arr.push({
      character:char,
      encodeURI:encodeURI(char),
      encodeURIComponent:encodeURIComponent(char)
    });
  }
}
console.table(arr);
```
# 原生JS API  

## window.onload DOMContentLoad document.readystatechange  

``` js  
// window.onload=(e)=>{
//     console.log('onload')
// }
window.addEventListener('load',(e)=>{
    console.log('load')
})
window.addEventListener('DOMContentLoaded',(e)=>{
    console.log('DOMContentLoaded')
})
document.addEventListener('readystatechange',(e)=>{
    console.log(document.readyState)
})
// 输出
// interactive
// DOMContentLoaded
// complete
// load
```  

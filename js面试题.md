# 面试题——JS  

1 下面代码会输出什么？如何保证i按序输出？  

``` js  
for(let i=0;i<5;i++){
    setTimeout(()=>{
        console.log('qq',i)
    }, 1000)
}
// 输出5
for(let i=0;i<5;i++){
    setTimeout(()=>{
        console.log('qq',i)
    }, 1000)
}
for(var i=0;i<5;i++){
    ((j)=>setTimeout(()=>{
        console.log('qq',j)
    }, 1000))(i)
}
```  

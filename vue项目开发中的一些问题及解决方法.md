# sd

## 1. [Vue warn]: The client-side rendered virtual DOM tree is not matching server-rendered content。错误肯定在template中  

1. 可能是那个标签没有闭合  
2. 可能是标签嵌套不合规范：如ul下使用其他标签，这是官网有说明的，但是我用ul嵌套其他标签页没报警。但是在使用table时没加thead和tbody就报警。
3. 格式化后再检查一下  

## 2. [Vue源码解析](https://ustbhuangyi.github.io/vue-analysis/)  

## 3. [Vue组件去缓存](https://www.jianshu.com/p/8cedd68d30ad)绑定key会完全重新创建组件  

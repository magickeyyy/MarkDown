# better-scroll常用方法总结[文档2.x](https://better-scroll.github.io/docs/zh-CN/)&emsp;[文档1.x](https://ustbhuangyi.github.io/better-scroll/doc/zh-hans/options-advanced.html)  

> 一般都是在mounted中请求数据，再在$nexttick总创建BScroll实例，但是不一定有效。保险点还是要先获取数据再在$nexttick中创建实例。  
>  记得在beforeDestroy中销毁scroll实例

## infinity  

> 长列表滚动优化，1.x版本在vue中使用不友好,作者在issue中不建议使用。  

必须创建墓碑元素，且class包含tombstone  
为了保证高度不会计算错误，墓碑元素高度必须固定。墓碑相当于骨架，占位，里面的图片类需要异步加载的必须先固定高度。

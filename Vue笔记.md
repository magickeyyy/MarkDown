# Vue笔记  

## $attrs、$props、props、$listeners、inheritAttrs  

$attrs  
> 可以访问写在组件上的任何自定义属性，最终会添加到组件根元素上。如果是v-bind、且组件没有porps中接收也是attrs,props中有接收就会成为props。  
props  
> 在组件上的属性，并且组件内props中定义了同名属性接收  
> 绑定不一定要加v-bind，也可直接书写（注意格式）

$props
> 组件现有的props集合，即组件内定义了要接收的全部props。但是要使用sync就要单独使用了  

$listeners  
> 组件上绑定的全部事件,但是加上native修饰符就不在了  

## 响应式原理  

Vue会在初始化阶段把data中定义的属性全部递归的劫持个遍(Object.defineProperty)，所以在修改数据后才能自动修改DOM。但是要实现自动响应，就得修改劫持的最小数据，Object.defineProperty最小可以劫持Object的属性、是对象数组元素，其他的就得返回新数据(修改堆中的地址)。那原本就是个简单属性的呢（name: 1112）,因为data返回的实际上是一个对象。  

## 组件上v-model  

组件会有个默认的props value接收，然后watch他，通过自定义事件等修改他  

## Vue JSX  

### 函数式组件  

[在vue中使用jsx语法](https://www.cnblogs.com/amylis_chen/p/11320059.html)
[在Vue中使用JSX的正确姿势](https://blog.csdn.net/tzllxya/article/details/92840142)
[官方插件](https://github.com/vuejs/jsx)

``` js  
//父组件
 ...省略无关代码
 render(){
    return (
        <Item data={this.data} class="large"/>
    )
}
// 子组件
export default {
    functional:true,
    inheritAttrs: false, // 避免props设定在最外层div
    name: "item",
    render(h,context){
        return (
        <div class="red" {...context.data}>
            {context.props.data}
        </div>
        )
    }
    }
```  

``` js  
<Modal
    ref="modal"
    value = {this.modal}
    closable={false}
    transfer={false}
    maskClosable={false}
    footerHide={true}
    on-visible-change={this.closeModel}
>
    <div class="box">
        <div class="code">
            <div>
                &yen;{this.mixin_m_formatNumber(this.payInfo.payAmount, 'x,xxx.xx')}
            </div>
            <p>请使用{tranPayWay(this.payment)}扫描二维码完成支付</p>
            <img id="qrcode" src={this.qrCode}/>
            <p>二维码有效期还剩{this.time}分钟</p>
            <div class="delete" onClick={() => {this.modal=false}}>
                <svg class="icon" aria-hidden="true">
                    <use xlinkHref="#icondanchuang_guanbi" />
                </svg>
            </div>
        </div>
    </div>
</Modal>
```

### 写法  

自定义事件不能通过@/v-bind传递，只能用on={{'on-visible-change':this.closeModel}}
v-model="attr"=>vModel={attr}  
直接传递的props如果是中间带-、：或者其他符号隔开的，全部用驼峰命名
可以有综合写法

``` js  
<Com {...{props:{},on:{}}}></Com>
```

## 响应式规律总结  

Vue会在组件beforeCreadted时递归的对组件data中的对象数组及其非基本类型属性设置getter/setter。对于对象，只要是在data中显示申明了，可随意修改属性；对于数组，如果修改的key是基本类型必须用splice等修改原数组的方法，如果key是对象等，可以直接修改，同样具有响应。对于已经在data中申明了的属性，不能直接添加响应式属性了，可以使用$set
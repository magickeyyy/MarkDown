<!--
 * @Author: magckeyyy
 * @Date: 2020-01-06 09:59:24
 * @Description: 
 * @Attention: 
 -->

# nuxt开发总结  

> &emsp;&emsp;第一次使用nuxt开发，遇到不少问题，真的很坑，很坑是因为即便是官网有许多地方也没有详细说明，代码示例更少了。有些方法的使用还得靠自己debugger、conso才能知道如何传参，尤其是context.redirect([status：304,]path[,query])，这个status传与不传真的是玄学。  
> &emsp;&emsp;由于项目很多地方都需要本地存储数据，但是要预渲染，许多地方就不能使用Window对象，改成了spa模式，后面再不断深入改回universal模式。  
> &emsp;&emsp;UI框架选的iview(spa:v3.5.1,universal:v4.0.2)，深入使用后才发现部分问题。比如universal模式，Message组件用到了document，就要用process.client判断是客户端才能使用，那就不能正常提示了，其他组价还未知。iview有些表单验证不能自动触发，还得自己手动触发校验。dropdwon组件能自动识别合适的弹出位置，即便传了placement，这在某些情况就成了问题。  
> &emsp;&emsp;一定要考虑数据持久化程度这个问题，如果页面的启动参数可以放到URL没有什么问题，如果要放到store中还要能刷新，那就恶心了，每个处理数据的逻辑都要判断是否有数据，没有就放到客户端，客户端还要更新store，没有处理过数据才能处理，很累，维护也麻烦。

## 1. 结合nuxt、vue的生命周期和nuxt的layout分析流程  

### 1.1 nuxt生命周期  

![nuxt生命周期 ](https://zh.nuxtjs.org/nuxt-schema.svg)  

1. nuxtServerlint可以在服务端获取数据操作store再传给客户端,每次执行启动项目都会自动执行，如首屏加载、刷新。注意：只能在store/index中使用，否则无效。  

```js
async nuxtServerInit({ commit}, {app: { router }, store, req, res, query, params, route}) {
    await commit('SET_TOKEN', cookie_parse(req.headers.cookie).token)
}
```  

1. middleware(context)会根据middleware/*.js中的逻辑控制路由，匹配全局layout。鉴权、重定向在这里做最好了，试过在pugins中拦截，会闪过错误页面。
2. validate({ params, query, store })可以在匹配路由前校验再匹配路由,仅限page。
3. asyncData是在页面渲染前一步获取数据并填充data，注意：会被data中的同名属性覆盖。fetch是在页面渲染前异步操作store。注意：都只在页面使用。  
4. 然后就开始预渲染，每次切换路由会进入middleware再循环。以上步骤都在服务端执行。  

5. 之后就是vue生命周期：beforeCreate等等。beforeCreate和created中仍然不能使用window对象，虽然process.client\=\=\=true&&process.server\=\=\=false。后面的钩子才是完全在客户端执行。  

### 1.2  Vue生命周期  

![Vue生命周期](https://cn.vuejs.org/images/lifecycle.png)  

### 1.3 layout  

![layout](https://zh.nuxtjs.org/nuxt-views-schema.svg)  

在layouts目录中可以配置不同布局，要自定义404、错误页面就在里面新建error.vue。从图中可以看出layout是先于page的，所以有些公共方法可以放到layout中。但是建议不要用太多layout，不在layout中增加业务逻辑，增加维护成本。  

## 2. vuex

根目录下有个store目录，nuxt会根据文件名创建store，文件名就是module名，index就是根store。很简单，用法没有什么变化。组件实例中结合辅助函数使用。  

```js  

export const state = () => ({
    locale: 'zh'
})

export const mutations = {
    SET_LANG(state, locale) {
    },
}

export const actions = {
    ASYNC_LANG({ commit }, locale) {
        commit('SET_LANG', locale)
    },
    async nuxtServerInit({ commit, dispatch }, {app: { router }, store, req, res, query, params, route}) {
        let token = parse_cookie(req.headers.cookie).token;
        await commit('login/SET_LOGIN', {
            token,
            logined: token?true:false
        })
    }
}  
````  

发现一个问题：一旦进入错误页面（404等），stroe就初始化了

## 3. route  

### 3.1  根据pages下的文件路径和名字生成route文件，具体可见官网示例。但是这样你是没法添加额外的路由配置的，比如meta、redirect等，但是官方也给出了一个扩展路由的方法：nuxt.config中配置router.extendRoutes  

``` js  
// 根据路由手写一个数组，每个元素必须有name。
const routeList = [
    {
        name: 'index',
        redirect: { name: 'hotel' }
    },
];
// 遍历手写的route，找到已有的route中同name路由把除name、children外的扩展加上。所以手写的route一定要匹配.nuxt中的route
function iterator(routes, extend) {
    for (let i = 0; i < extend.length; i++) {
        let n = routes.findIndex(v => v.name === extend[i].name);
        if (n > -1) {
            Object.keys(extend[i]).map(key => {
                if(key !== 'name' && key !== 'children') {
                    routes[n][key] = extend[i][key];
                }
                if(key === 'children') {
                    iterator(routes[n].children, extend[i].children);
                }
            });
        }
    }
}
// nuxt.congig.js中router.extendRoutes就是一个方法。arguments[0]就是当前完整路由，arguments[1]是加载组件的方法，至于是不是异步加载没去研究
module.exports = function(routes, resolve) {
    routes = iterator(routes, routeList);
};

```  

### 3.2  路由生成规则官网有许多示例，这里只讲一个比较特别的  

有个模块叫test，我们在pages下新建test文件夹，又新建test.vue且加上nuxt-child组件，又在test文件夹下新建了index.vue和us.vue，那么实际生成的路由配置是  

``` js  
 {
    path: "/test", // 无name
    component: _1e35c944,
    children: [{
      path: "",
      component: _8c89b93e,
      name: "test"
    }, {
      path: "us",
      component: _6869bc9f,
      name: "test-us"
    }]
  },
```  

结果就是/test直接跳转到index且url仍是/test，tes.vue可以写一些公共逻辑。没看过生成路由的源码。  

## 4 爬过的坑  

### 4.1 进度条始终在加载状态  

出现在middleware、plugins中使用了redirect的情况，而且是第一个参数传了staus。解决办法是只传path。  

## 5. validate  

validate是一个函数，可以拿到store、query、params等，我们可以根据业务校验他们，必须返回一个布尔值或者promise，失败去404。但是在刷新后validate获取到的params、store都是是默认值,即便我是先设置了store再跳解析路由

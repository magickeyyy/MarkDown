# react-router-dom@5.2  

## API  

### Hooks  

#### useHistory  

#### useLocation  

#### useParams  

#### useRouteMatch  

### BrowserRouter  

#### basename: string

#### getUserConfirmation: func  

#### forceRefresh: bool  

#### keyLength: number  

#### children: node  

### HashRouter  

#### basename: string  

#### getUserConfirmation: func  

#### hashType: string  

#### children: node  

### Link  

#### to: string  

#### to: object  

#### to: function  

#### replace: bool  

#### innerRef: function  

#### innerRef: RefObject  

#### component: React.Component  

#### others  

#### \<NavLink\>  

#### activeClassName: string  

#### activeStyle: object  

#### exact: bool  

#### strict: bool  

#### isActive: func  

#### location: object  

#### aria-current: string  

### \<Prompt\>  

### \<MemoryRouter\>  

#### initialEntries: array  

#### initialIndex: number  

#### getUserConfirmation: func  

#### keyLength: number  

#### children: node  

### \<Redirect\>  

#### to: string  

#### to: object  

#### push: bool  

#### from: string  

#### exact: bool  

#### strict: bool  

#### sensitive: bool  

### \<Route\>  

#### Route render methods  

#### Route props  

#### component  

#### render: func  

#### children: func  

#### path: string | string[]  

#### exact: bool  

#### strict: bool  

#### location: object  

#### sensitive: bool  

### \<Router\>  

#### history: object  

#### children: node  

### \<StaticRouter\>  

#### basename: string  

#### location: string  

#### location: object  

#### context: object  

#### children: node  

### \<Switch\>  

#### location: object  

#### children: node  

### history  

#### history is mutable  

### location  

### match  

#### null matches  

### matchPath  

#### pathname  

#### props  

#### returns  

### withRouter  

#### Component.WrappedComponent  

#### wrappedComponentRef: func  

## 个人总结  

### 注意事项  

1. 如果有子路由，父级不能使用exact，即只有在没有子路由的Route上才能使用exact。因为必须先匹配父级路由才能匹配子级路由。
2. 404。如果是嵌套路由，每个父级都要加专门的404Route

``` js  
<Route path="*" component={NotFound}></Route>

// 或者在最外层放置404路由，每个父级最后加Redirect
<Route path="/404" component={NotFound}></Route>
<Redirect to="/404"></Redirect>
```

### 路由鉴权  

1. 封装

``` js  
import React from 'react'
import { Route, Redirect } from 'react-router-dom'
import { isAuthed } from '../../utils'

/**
 * @description: 校验props.path和window.location.pathname是否匹配。动态路由尤其重要
 * @param {String} path 指props.path
 * @return {Boolean} 动态路由时：是否匹配；其他：是否完全相等
 */
function reg(path) {
    if (/\/:/g.test(path)) {
        const arr = path.split('/').filter(v => v)
        let str = ''
        arr.map(v => {
            /^:.+$/g.test(v) ? str += '/.*' : str += '/' + v
            return v
        })
        return new RegExp(str, 'g').test(window.location.pathname)
    } else {
        return path === window.location.pathname
    }
}

/**
 * @description: 需要校验的路由使用此组件。使用方法参照Route。要用到reg方法是为了不影响404路由
 */
function PrivateRoute(props) {
    const authed = isAuthed()
    const { children, ...rest } = props
    return reg(props.path) && !authed ? (
        <Redirect to="/login" />
    ) : <Route {...rest} >{children}</Route>
}
export default PrivateRoute
```  

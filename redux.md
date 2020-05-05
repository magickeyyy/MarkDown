# Redux学习笔记  

## state  

## [redux devtools](https://github.com/reduxjs/redux-devtools)  

首先安装Chrome插件Redux DevTools并开启，记得开启Chrome开发者模式。然后在项目index.js中  

``` js  
import { createStore, applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk'
//第一种，最新版  
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
const store = createStore(counter, composeEnhancers(
    applyMiddleware(thunk)
));
// 第二种  
const store = createStore(counter, compose(
    applyMiddleware(thunk),
    window.devToolsExtension?window.devToolsExtension():f=>f
));
```  

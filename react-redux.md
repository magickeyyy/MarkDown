# [react-redux](https://react-redux.js.org/)  

## [connect](https://react-redux.js.org/api/connect)  

使用方法  

``` js  
function connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?)  
```  

``` js  
export const ConnectedRoot = connect(
    (state) => state,
    (dispatch) => ({
        onFilter: (args) => dispatch({ type: 'RUN_FILTER', ...args }),
        onSetSearch: (search) => dispatch({ type: 'SET_SEARCH', search }),
        onFetchData: (day) => dispatch(fetchData(day))
    })
)(Root);  
```  

``` js  
/*
安装插件并在package.json中babel.plugins增加配置
"babel": {
    "presets": [
      "react-app"
    ],
    "plugins": [
      ["@babel/plugin-proposal-decorators", { "legacy": true }]
    ]
  }
 */
@connect(
    (state) => state,
    (dispatch) => ({
        onFilter: (args) => dispatch({ type: 'RUN_FILTER', ...args }),
        onSetSearch: (search) => dispatch({ type: 'SET_SEARCH', search }),
        onFetchData: (day) => dispatch(fetchData(day))
    })
)
class Root extends Component{}  
```  

### connect params  

> mapStateToProps?: Function  
mapDispatchToProps?: Function | Object  
mergeProps?: Function  
options?: Object  

mapStateToProps?: (state, ownProps?) => Object  
mapDispatchToProps?: Object | (dispatch, ownProps?) => Object  

# webpack@4.43.0学习笔记  

## 需要的基础知识  

### Nodejs  

#### path-路径  

##### path.join(__dirname, 'src', '/src/', '\\src', 'user.js', ...)  

把路径规范化（不同平台间隔符不同），如果某个目录不存在会报错。

##### path.resolve()

参数是若干目录，可以是字符串也可以绝对/相对路径，从右往左解析成绝对路径。  

``` js  
console.log(path.resolve('./config/webpack.base.config.js','./config/../public/art-template/template-web@4.0.min.js'))  //E:\Projects\personal_projects\webpack-jquery-bootsrap\config\webpack.base.config.js\public\art-template\template-web@4.0.min.js  

path.resolve() === path.resolve('') === __dirname // 返回当前目录
```  

#### gloab-全局变量  

##### __dirname  

##### __filename

#### process-进程  

#### fs-文件系统  

### 常用包  

#### glob-支持类似正则模糊匹配查找路径  

需要注意的是在windows上node模块解析的路径分隔符都是\\,但是glob模块返回的路径是/。  

#### dotenv读取.env文件中的值并设置process.env  

实际上完全可以通过js设置process.env。
还有个dotenv-webpack的插件，可以设置process.env，但是他是在node webpack.config.js之后，如果在webpack.config.js中就要用到，不如在导出配置前就设置好process.env。cross-env只能在npm script中设置一个值，以此值区分运行环境。

## webpack命令行(CLI)  

### context  

默认值是process.cwd()(webpack.config.js默认是在项目根目录的)，entry的相对路径就是相对于他。

### entry  

entry如果是object，output.filename不能固定文件名，这算是多入口多出口模式；entry是数组，每个元素是路径字符串，只有一个出口；是个字符串，只能有一个出口，可以指定文件名

## loader  

### babel-loader  

@babel/present-env是根据env解析语法，@babel/plugin-transform-runtime是对@babel/polyfill的按需引入

## plugins  

### mini-css-extract-plugin  

从js中提取css，并且支持按需加载。（不再使用extract-text-webpack-plugin）  
输出文件会有个publicPath需要注意，默认是webpackOptions.output.publicPath,可以在webpackOptions.module.rules中用到插件时在options中修改publicPath。  

E:\Projects\personal_projects\webpack-jquery-bootsrap\build\static\js\E:\Projects\personal_projects\webpack-jquery-bootsrap\build\static  
js输出目录是E:\Projects\personal_projects\webpack-jquery-bootsrap\build\static\js
filename总会自动加上一个路径E:\Projects\personal_projects\webpack-jquery-bootsrap\build\static\js

### webpack.DefinePlugin  

自定义一些全局常量，可以在编译中使用

### webpack.ProvidePlugin  

比如需要使用jQuery，代码中不引入jQuery而直接使用$，就可以指明他的出处,编译时webpack自动注入(在entry中加入，可以根据chunkname提取并创建script加载；这种方式是把整个包编译进chunk，所以还需要commonchunk提取。)

``` js  
module.exports = {
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery',
      template: 'art-template/lib/template-web.js',
    }),
  ],
}
```  

## stats  

看着每次编译时控制台密密麻麻的日志就烦，这个配置可以细腻的管控控制台日志输出。webpackbar这个插件可以显示编译进度，显得高端一点。  

## 某些需求  

### 多入口项目依赖jQuery+bootstrap+popper.js，不想在每个页面都再引一遍  

``` js  
module.exports = {
    // value是npm包名或路径
    entry: {
        "jquery": "jquery",
        "popper.js": "popper.js",
        "bootstrap": "bootstrap",
        "art-template": "art-template/lib/template-web.js",
        ...
    },
    plugins: [
        // 实际上每个html都需要一个HtmlWebpackPlugin
        new HtmlWebpackPlugin({
            template: cur,
            inject: true,
            filename: fileName + '.html',
            title,
            // 多入口必须指定需要的chunk，否则引入全部chunk
            // 这个顺序就是插入script的顺序
            chunks: ["jquery","popper.js","bootstrap","art-template",chunkName],
            minify: true
        }),
        ...
    ]
}
```  

不然的话就需要每个入口都全部引用一遍  

## 面试题  

### chunk、bundle区别  

chunk与entry对应（数量不限，可以使npm包）经过webpack编译后的一段代码块。bundle是最终打包出来的资源文件，可以多个chunk合并成一个bundle，一个总chunk中提取多个公共chunk打包单独的bundle。  

### loader、plugin的区别  

webpack只认识js、json格式的文件，比如css-loader，就是把css转换成string插入js。plugin是贯穿整个构建生命周期的，不限于文件数据转换了，作用很多。

### 利用缓存  

hash是项目每构建一次都会有新的hash，如果使用'[name].[hash].[ext]'这类文件会被全部改名。chunkhash只要他依赖的模块没有改动就不会改变，所以能做缓存优化（构建输出文件必须保留，manifest.json）。但是开发模式应该避免缓存。

### 构建速度优化  

1 loader设置include、exclude  
2 配置resoleve：优化解析第三方包规则modules;常用路径使用别名alias，优化路径解析速度（不能再使用ctr+路径跳转到文件功能）  
3 devtool配置sourcemap  
4 happypack多线程打包  
5 externals使用cdn：某些库可以使用cdn，配置完后，模块中引入了也不会编译  

### 为什么某些类比如jQuery已经在模板引入了，但是在模块中还需要引入

因为在模块中如果没引入就使用一个变量，他一定是undefined，因为我们的模块都是先编译（chunk）才生成bundle，在这个代码块中他是undefined。

## 遇到的问题  

### css打包问题  

1 一般都是开发模式使用style-loader，生产模式使用mini-css-extract-plugin，这是有原因的：虽然mini-css-extract-plugin文档上看上去支持hmr，但实际使用中没有卵用。  
2 在一个入口中import 'bootstrap/dist/css/bootstrap.min.css'、import './index.less'，发现打包的css只有bootstrap头部一部分，index.less一点也没有。原来是purgecss-webpack-plugin的原因，它会剔出无用的css，先不用这个插件了。  
3 使用的模板引擎是art-template，art文件中要用图片等，就加了html-loader（已经有html-webpack-plugin，会有冲突），这个loader需要ejs语法。引入jpg无压力,md引入png却报错，说要把html-webpack-plugin的minify改成false，试了一遍也不知道是哪个压缩属性影响了，改成false就好了，但压缩功能没有了。google一遍，找到思否一篇文章说url/file-loader的options加入esModule:false就解决了（百度找不到:joy:）,并且css、js中的图片也不影响，用法也直接和普通html一样了:smiley:。  

``` js  
// url/file-loader未禁用esModule
<img src={{require("../../assets/img/checkbox.png").default}} alt="加载失败">
```  

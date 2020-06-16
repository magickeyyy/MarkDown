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

## plugins  

### mini-css-extract-plugin  

从js中提取css，并且支持按需加载。（不再使用extract-text-webpack-plugin）  
输出文件会有个publicPath需要注意，默认是webpackOptions.output.publicPath,可以在webpackOptions.module.rules中用到插件时在options中修改publicPath。  

## stats  

看着每次编译时控制台快速闪烁的日志就烦，这个配置可以细腻的管控控制台日志输出。webpackbar这个插件可以显示编译进度，显得高端一点。

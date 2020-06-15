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

## webpack命令行(CLI)  

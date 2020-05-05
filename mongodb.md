# MongoDB  

## Blog  

[mongodb的几种启动方法](https://www.cnblogs.com/LLBFWH/articles/11013791.html)  

## 常用命令 mongo  

启动客户端：  

``` shell
mongod --dbpath /usr/local/var/mongodb --logpath /usr/local/var/log/mongodb/mongo.log --fork  
```  

用配置文件启动：

``` shell  
mongod --config /usr/local/etc/mongod.conf  
```  

命令行  

``` shell  
mongo [options]  
```  

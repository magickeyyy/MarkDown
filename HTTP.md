# HTTP  

盗用文章：  
[http请求头与响应头的应用](https://juejin.im/post/5b854ddef265da43635d9302)  
[面试官（9）：可能是全网最全的http面试答案](https://juejin.im/post/5d032b77e51d45777a126183#heading-6)

## 1. headers  

### 1.1 Content-Type  

Content-Type表示请求头或响应头的内容类型。所以请求体和响应体的数据类型就要和它一致。  

### 1.2 Range:bytes  

请求头通过Range:bytes可以请求资源的某一部分。利用这个字段可模拟部分、分段读取，  

### 1.3 Cache-Control与Expires之强制缓存  

### 1.4 对比缓存之Last-Modified和If-Modified-Since  

### 1.5 对比缓存之Etag和 If-None-Match  

### 1.6 Accept-Encoding  

### 1.7 referer  

# ES6标准入门学习总结  

## 1. 前言  

## 2. let和const  

const申明的常量只是在内存中的地址不变，指针不变。如果常量是个复杂类型，依然可以修改。如果把一个常量再次赋值给新变量，再覆盖新变量，常量是不会修改的。  

``` js  
const a = { age: 29 };
let b =  a;
b.age = 30;  // a.age === b.age === 30
b = 31;  // a依然是{ age: 30 }
```

## 3. 变量解构赋值  

## 4. 字符串扩展  

### 4.1 ES6前常用的方法  

4.1.1 String.fromCharCode(Unicode1,Unicode2,...,Unicoden)是String静态方法，参数是至少一个Unicode码，返回所有参数对应的字符组成的字符串。  
4.1.2 stringObject.indexOf(searchvalue[,fromindex])默认从首字符开始检索，返回某个指定的字符串值在字符串中首次出现的位置，没有返回-1。  
4.1.3 stringObject.lastIndexOf(searchvalue,fromindex)  

## 5. 字符串新增方法  

## 6. 正则扩展  

## 7. 数值的扩展  

## 8. 函数的扩展  

## 9. 数组的扩展  

## 10. 对象的扩展  

## 11. 对象新增的方法  

## 12. Symbol  

## 13. set和map  

## 14. Proxy  

## 15. Reflect  

## 16. Promise  

## 17. Iterator和for...of  

## 18. Generatore  

## 19. Generator 的异步应用  

## 20. async  

## 21. class

&emsp;&emsp;ES6之前实现一个类一般是通过工厂函数，修改原型链实现类的继承，问题是容易被篡改。ES6提供classAPI，使用extends继承。  
子类必须在constructor中首先调用super否则会报错(新建子类实例时)，因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。一级父类默认继承自Object还是function？？？？？。  
&emsp;&emsp;ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply(this)）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this  
&emsp;&emsp;super表示父类的构造函数，但是返回的是子类  
&emsp;&emsp;类中无this，只有实例才有

super指向父类（不是实例哦），constuctor默认返回的是实例，也可手动返回其他对象

## 22. class的继承  

## 23. module  

## 24. module的加载实现  

## 25. 编程风格  

## 26. 读懂规格  

## 27. 异步遍历器  

## 28. ArrayBuffer  

## 29. 最新提案  

## 30. Decorator

## 作用域  

### 全局作用域  

在函数、模块外申明的变量全局都可以访问。

``` js  
// inde.html
// 重点在type，如果是module，这段脚本就是es6module
...
<script type="text/javascript" src="./index.js"></script>
...
// index.js
var a=12; // 全局作用域
b=13; // 使用效果如上，他实际上是挂到window上，可以被删除，但是a却不能
```

### 函数/局部作用域  

在函数内申明的变量只能在函数内访问（可以访问上一级变量；module也是函数）

### 动态作用域  

this  

### 块状作用域  

{}中的代码；let、const

## 遍历  

for...in...针对object（array是object，arr.name=122,也会被遍历）,for针对array，for...of...都可以  

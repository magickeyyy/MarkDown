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

&emsp;&emsp;ES6之前实现一个类一般是通过工厂函数，修改原型链实现类的继承。ES6提供classAPI，使用extends继承。  
&emsp;&emsp;子类必须在constructor中首先调用super否则会报错(新建子类实例时)，因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。  
&emsp;&emsp;ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply(this)）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this  
&emsp;&emsp;super表示父类的构造函数，但是返回的是子类  
&emsp;&emsp;类中无this，只有实例才有

super指向父类（不是实例哦），constuctor默认返回的是实例，也可手动返回其他对象  
如果父类constructor有参数，子类必须调用super，并且传入指定参数：既子类的constructor的参数必须包括父类constructor的参数，且子类必须调用super传入这些参数  

## 22. class的继承  

&emsp;&emsp;class语法本身是es5构造函数的语法上塘，定义的类的方法(非静态方法)本身是在原型链上添加方法  

``` js  
class A {
    somemethord() {}
    othermethord() {}
}
// 等价于
Object.asign(A.prototype,{
    somemethord() {}
    othermethord() {}
})
```  

&emsp;&emsp;constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。constructor默认返回当前类的实例(this)，也可以返回指定上下文

``` js  
class A {}
// 等价于
class A {
    constructor() {}
}
```  

&emsp;&emsp;如果有父类，还必须在constructor中调用super  

``` js  
class Parent {
    constructor(x) {
        this.x = x
    }
}
class Son extends Parent {
    constructor(x,y) {
        super(x)
        this.y = y
    }
}
```  

&emsp;&emsp;在new一个类时，先调用constructor方法，如果是子类，必须在constructor内先调用super(接受全部父类需要的参数)，先创建父类上下文，再创建子类上下文  
&emsp;&emsp;class内的方法不需要加function,可以在constructor中绑定this或使用箭头函数，否则经过赋值后的方法会改变this  

``` js  
class Parent {
    say() {
        console.log(this.name)
    }
    static text() {
        console.log(this.name)
    }
}
const {say} = new Parent()
say() // Uncaught TypeError: Cannot read property 'firstname' of undefined
const {text} = Parent
text() // Uncaught TypeError: Cannot read property 'firstname' of undefined

// 修改之后
class Parent {
    constructor() {
        this.say = this.say.bind(this)
    }
    say = () => {
        console.log(this.name)
    }
    say() {
        console.log(this.name)
    }
    static username = 'nothing'
    static text1 = ()=> {
        console.log(this.username)
    }
}
const {say} = new Parent()
say() // 'Parent'
const {text} = Parent
text() // 'nothing'wqwqsdfsdf
```  

&emsp;&emsp;第二种情况，super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

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

## [帮你彻底搞懂JS中的prototype、__proto__与constructor（图解）](https://blog.csdn.net/cc18868876837/article/details/81211729)

## this  

普通函数的this，谁调用只想谁，非严格模式找不到就指向window。箭头函数是绑定的父级的作用域，如果父级也是箭头函数，那就继续晚上找。obj={fn(){}},这是obj={fn:fucntion(){}}的简写，而不是箭头函数。  

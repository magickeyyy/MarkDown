# JS学习笔记  

抄袭了不止以下文章，所以遇到链接最好是过去看一下点个赞，这里只是一些补充、整理。
> [如何写出一个惊艳面试官的深拷贝?](https://juejin.im/post/5d6aa4f96fb9a06b112ad5b1#heading-5)有几个问题：Object.getOwnPropertySymbols才能取到symbols的key；数据有保证类型和基本类型如const a = new Number(1)和const a = 1，经过typeof运算的结果前者是object后者才是number

## 1. 容易迷惑和忽略的地方  

### 1.1 判断数据类型

值类型(基本类型)：字符串（String）、数字(Number)、布尔(Boolean)、对空（Null）、未定义（Undefined）、Symbol。  
引用数据类型：对象(Object)、数组(Array)、函数(Function)。  

```js  
// instanceOf
class Parent {
    constructor() {
        this.name = 'bolb';
        this.age = 56;
    }
    get username() {
        return this.name;
    }
}
const xiaoming = new Parent();
xiaoming instanceof Parent; // true
[] instanceof Array && [] instanceof Object; // true
// typeof
typeof NaN === 'number'; // true
typeof null === 'object'; // true
// isNaN
isNaN(); // true
isNaN(NaN); // true
// Array.isArray()
Array.isArray([]); // true
// Object.prototype.toString.call(),以下是全部数据类型的返回值
const mapTag = '[object Map]';
const setTag = '[object Set]';
const arrayTag = '[object Array]';
const objectTag = '[object Object]';
const argsTag = '[object Arguments]';
const boolTag = '[object Boolean]';
const dateTag = '[object Date]';
const numberTag = '[object Number]';
const stringTag = '[object String]';
const symbolTag = '[object Symbol]';
const errorTag = '[object Error]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';

const undefinedTag = '[object Undefined]'
const numberTag_NaNTag = '[object Number]'
const nullTag = '[object Null]'
const booleanTag = '[object Boolean]'
```  

1. instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上([MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof))。就是说instanceOf可以从原型链上检查目标对象(万物皆对象)是不是某个类的实例。返回布尔值，适用于引用数据类型数据的类型判断  
2. typeof 操作符返回一个字符串，表示未经计算的操作数的类型([MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof))。返回字符串undefined、boolean、string、number、object、function、symbol。  
3. 当检测 Array 实例时, Array.isArray(value) 优于 instanceof,因为Array.isArray能检测iframes([MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/isarray))  
4. Object.prototype.toString.call(value) 可以精确判断数据类型。注意：自定义对象可能覆盖了toString方法。  

> 每一个引用类型都有toString方法，默认情况下，toString()方法被每个Object对象继承。如果此方法在自定义对象中未被覆盖，toString() 返回 "[object type]"，其中type是对象的类型。  

1. isNaN() 函数用来确定一个值是否为NaN。注：isNaN函数内包含一些非常有趣的规则；你也可以使用 ECMAScript 2015 中定义的 Number.isNaN() 来判断([MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/isNaN))。  

### 1.2 位运算和运算符优先级  

初级前端的我常常忽略这部分问题，这是个能提高开发效率但严重影响维护效率的骚操作，因为有些反而是JavaScript的糟粕，写好注释吧。  

### 1.2.1 ++n和n++

前者参与运算或者return时先自己加一再参与运算或者return运算后的结果，后者参与运算或者return时会先参与运算或者return自身再自己加一。return n+1会返回计算后的结果  

``` js  
```

### 1.3 循环遍历for、while、switch、for...in ...、for...of...、forEach、map、every、filter等  

[for](https://www.cnblogs.com/amujoe/p/8875053.html)  

### 1.4 event loop/JavaScript 执行机制

[这一次，彻底弄懂 JavaScript 执行机制](https://juejin.im/post/59e85eebf265da430d571f89)   
[实际延时比设定值更久的原因：最小延迟时间](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout#%E5%AE%9E%E9%99%85%E5%BB%B6%E6%97%B6%E6%AF%94%E8%AE%BE%E5%AE%9A%E5%80%BC%E6%9B%B4%E4%B9%85%E7%9A%84%E5%8E%9F%E5%9B%A0%EF%BC%9A%E6%9C%80%E5%B0%8F%E5%BB%B6%E8%BF%9F%E6%97%B6%E9%97%B4)  
vue的响应式原理：vue中每次操作data都是一个任务，会被放进一个执行队列，异步的执行最后一个任务，所以修改后并不能立刻获取到最新值，要使用nextTick,

javascript是单线程语言，即便加了web-worker，也只是加个单线程而已。所以执行顺序就是代码顺序。但是执行任务又分为同步任务和异步任务：不阻塞的是同步任务，会阻塞的是一部任务，主线程遇到同步任务会按顺序执行，异步任务会被放到专门的任务队列event table中，在这个队列中的任务执行完了，会被放到另一个专门的任务队列event queue,所以event queue中的顺序决定于event table中的执行结束时间。js引擎存在monitoring process进程，会持续不断的检查主线程执行栈是否为空，一旦为空，就会去Event Queue那里检查是否有等待被调用的函数。所以同步任务执行完后主线程会得到通知并执行event queue中的任务。并如此循环，即event loop。  
异步任务又分为：
macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
micro-task(微任务)：Promise，process.nextTick
不同类型的任务会进入对应的Event Queue

setTimeout(fn,0)并不是0延迟，只是表示主线程执行栈内的同步任务全部执行完成，栈为空就马上执行。
setTimeout的延时最低是4ms。[window.setTimeout](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout) 

### 1.5 this  

```js  
function f2(){
  "use strict";
  return this;
}
f2() === undefined; // true
window.f2() === window; // true

function f3() {
    return this;
}
f3() === window; // true
window.f3() === window; // true
```  

### 1.6 rem  

```js  
(function () {
    var htmlFontSize = document.documentElement.clientWidth / 375 * 100 + 'px';
    var bodyFontSize = '16px';
    var styleDom = document.createElement('style');
    styleDom.innerHTML = 'html{font-size:' + htmlFontSize + '!important;}body{font-size:' + bodyFontSize + '!important;}';
    document.getElementsByTagName('head')[0].appendChild(styleDom)
})();
```

[MDN的this释义](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)  

## 2. 资源库  

### 2.1 [W3C Plus](https://www.w3cplus.com/)  

### 2.2 [google web blog](https://developers.google.com/web/updates/2016/04/intersectionobserver)  

### 2.3 [GoogleChromeLabs](https://github.com/GoogleChromeLabs) &emsp; [ui-element-samples](https://github.com/GoogleChromeLabs/ui-element-samples)

### 2.4 [2019成都Web全栈大会PPT](https://web-conf.dev/#2019/)  

## 3. 想用但不知道的API  

### 3.1 IntersectionObserver  

元素是否在可见区域

### 3.2 document.createDocumentFragment  

每次都去操作一堆DOM并且频繁修改太耗性能，那就一次在内存中创建好要修改的DOM生成fragment一次性替换挥着插入。

### 3.3 requestAnimationFrame  

根据屏幕刷新率决定dom刷新速率  

### 3.4 getComputedStyle  

返回的style是一个实时的 CSSStyleDeclaration 对象，当元素的样式更改时，它会自动更新本身。

## 3.5 document.execCommand

## 4 类库  

### 4.1 [yargs nodejs命令行传参](https://github.com/yargs/yargs)  

### 4.2 [shelljs,用js写shell](https://www.npmjs.com/package/shelljs)  

## 5 其他  

### [script defer async](https://www.cnblogs.com/jiasm/p/7683930.html)  

defer  

> 如果script标签设置了该属性，则浏览器会异步的下载该文件并且不会影响到后续DOM的渲染；如果有多个设置了defer的script标签存在，则会按照顺序执行所有的script；defer脚本会在文档渲染完毕后，DOMContentLoaded事件调用前执行。  

async  

> async的设置，会使得script脚本异步的加载并在允许的情况下执行。async的执行，并不会按着script在页面中的顺序来执行，而是谁先加载完谁执行。可能马上就加载完就会立即执行，也可能什么都执行完了才加载完才执行。  

## [GOOGLE大神的例子集合](https://github.com/googlechrome/ui-element-samples)  

## 循环、遍历  

for...in循环有几个缺点。

数组的键名是数字，但是for...in循环是以字符串作为键名“0”、“1”、“2”等等。
for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
某些情况下，for...in循环会以任意顺序遍历键名  

## generator  

generator函数在 **调用(必须先调用)** 后不会立即执行。Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。每次调用next都从上一个yiled暂停的位置开始在下一个yiled前停下会返回一个iterator对象{value：下一个yield的值，done：false}，在遇到return是done是true，再调用next都是是{value:undefined,done:true}。yield表达式本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值。  
注意，由于next方法的参数表示上一个yield表达式的返回值，所以在第一次使用next方法时，传递参数是无效的。V8 引擎直接忽略第一次使用next方法时的参数，只有从第二次使用next方法开始，参数才是有效的。从语义上讲，第一个next方法用来启动遍历器对象，所以不用带有参数。如果想第一次就可以传参，可以先定义方法包装一下generator,在内部先执行一次next在返回generator。  

## 求值策略  

``` js  
var x = 3;
function foo(x+5) {}
```  

何时求x+5的值。两种观点：传值调用和传名调用。传值调用：函数还没编译就执行表达式。传名调用：把表达式装进函数里，届时再调用。JavaScript 语言是传值调用，它的 Thunk 函数含义有所不同。  


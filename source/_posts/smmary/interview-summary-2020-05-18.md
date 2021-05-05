---
title: 常见面试题总结
top: false
cover: false
date: 2020-05-18 19:40:29
scope1egories: 面试
tags:
- 面试
- js
- css
---

### CSS
---

#### 清除浮动
```css
.clear{
  clear:both;
}
```

#### 盒模型

`margin`+`border`+`content width and height`+`padding`
box-sizing定义如何计算一个元素的总宽度和总高度，默认content-box，width与height只包括内容的宽和高，border-box width和height包括content+padding+border。一些组件库会默认重置为border-box，比如ant-design:
```css
*, *:before, *:after {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
```

#### 权重
`!important` > `inline style` > `id` > `class` > `tag` > `*`



### JavaScript
---

#### 异步任务
```js
console.log(1);

setTimeout(() => {
  console.log(2);
}, 1000);

setTimeout(() => {
  console.log(3);
}, 0);

let last = new Date();
while(new Date() - last < 2000);

setTimeout(() => {
  console.log(4);
}, 0);

console.log(5);

// 1 5 3 2 4
```
这道题目可以拓展下promise
```js
var p1 = new Promise((resolve, reject) => {
    resolve();
})

setTimeout(() => {
  console.log(1);
},0)

p1.then(() => {
  console.log(2);
})

setTimeout(() => {
  console.log(3);
},0)

// 2 1 3
```
- MacroTask Queue（宏任务队列）,包括setTimeout, setInterval, setImmediate, requestAnimationFrame, UI rendeing, NodeJS中的`I/O等。
- MicroTask Queue（微任务队列）,包括Promise，Object.observe, MutationObserver 和NodeJs中的 process.nextTick

vue调用[异步更新队列](https://cn.vuejs.org/v2/guide/reactivity.html#%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0%E9%98%9F%E5%88%97)，尝试使用原生`Promise.then`、`MutationObserver` 和 `setImmediate`，如果执行环境不支持，则会采用 setTimeout(fn, 0) 代替。


#### 作用域
```js
var a = 1;

function foo(){
  console.log(a);
}

foo();

(function(){
  var a = 2;
  foo();
})();

(function(fn){
  var a = 3;
  fn();
})(foo);

// 1 1 1
```
JavaScript 采用的是词法作用域，函数的作用域在函数定义的时候就决定了。
作用域、this拓展：
```js
var name = 'window'

var scope1 = {
  name: 'scope1',
  getName1: function () {
    console.log(this.name)
  },
  getName2: () => console.log(this.name),
  getName3: function () {
    return function () {
      console.log(this.name)
    }
  },
  getName4: function () {
    return () => console.log(this.name)
  },
  getName5: function(){
    (function(){
      console.log(this.name)
    })()
  }
}
var scope2 = { name: 'scope2' }

scope1.getName1() // scope1
scope1.getName1.call(scope2) // scope2

scope1.getName2() // window
scope1.getName2.call(scope2) // window

scope1.getName3()() // window
scope1.getName3().call(scope2) // scope2
scope1.getName3.call(scope2)() // window

scope1.getName4()() // scope1
scope1.getName4().call(scope2) // scope1
scope1.getName4.call(scope2)() // scope2

scope1.getName5() // window
scope1.getName5.call(scope2) // window
```
参考
- [JavaScript深入之作用域链](https://github.com/mqyqingfeng/Blog/issues/6)
- [JavaScript深入之词法作用域和动态作用域](https://github.com/mqyqingfeng/Blog/issues/3)
- [JavaScript深入之从ECMAScript规范解读this](https://github.com/mqyqingfeng/Blog/issues/7)

### 简述题
---
#### 1、事件代理/事件委托
> 事件代理/事件委托是利用事件冒泡的特性，将本应该绑定在多个元素上的事件绑定在他们的祖先元素上，尤其在动态添加子元素的时候，可以非常方便的提高程序性能，减小内存空间。
当一个事件发生在具有父元素的元素上时，现代浏览器运行两个不同的阶段捕获阶段和冒泡阶段。

**在捕获阶段：**
> - 浏览器检查元素的最外层祖先<html>，是否在捕获阶段中注册了一个onclick事件处理程序，如果是，则运行它。
> - 然后，它移动到<html>中单击元素的下一个祖先元素，并执行相同的操作，然后是单击元素再下一个祖先元素，依此类推，直到到达实际点击的元素。

**在冒泡阶段，恰恰相反:**
> - 浏览器检查实际点击的元素是否在冒泡阶段中注册了一个onclick事件处理程序，如果是，则运行它
> - 然后它移动到下一个直接的祖先元素，并做同样的事情，然后是下一个，等等，直到它到达<html>元素。

**阻止冒泡/捕获**
```js
event.stopPropagation(); 
```
**使用场景**

> 一个很好的例子是一系列列表项，如果你想让每个列表点击时弹出一条信息，您可以将click单击事件监听器设置在父元素`<ul>`上，它将会冒泡到列表项上。

#### 2、深拷贝和浅拷贝
参考[Object.assign()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)和[lodash/cloneDeep](https://github.com/lodash/lodash/blob/master/cloneDeep.js)

#### 3、Promise
##### Promise有几种状态:
> pending: 初始状态，既不是成功，也不是失败状态。
> fulfilled: 意味着操作成功完成。
> rejected: 意味着操作失败。

##### Promise 解决的痛点是什么？
> 解决js callback hell
##### Promise 实现链式调用
> 返回一个promise
```js
// api getA getB getC
function getA(){
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('A');
    }, 1000)
  })
}

function getB(){
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('B');
    }, 1000)
  })
}

function getC(){
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('C');
    }, 1000)
  })
}

getA().then((result) => {
  console.log(result);
  return getB();
}).then((result) => {
  console.log(result);
  return getC();
}).then((result) => {
  console.log(result);
})

// Promise.all拓展
Promise.all([getA(), getB(), getC()]).then((results) => {
  console.log(results);
})

// async/await拓展
async function getAll(){
  const resultA = await getA();
  console.log(resultA);
  const resultB = await getB();
  console.log(resultB);
  const resultC = await getC();
  console.log(resultC);
}

getAll();
```

#### 同源策略
> [MDN同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)

#### 原型链
> JavaScript继承是基于原型链的，new一个实例时，每个实例共享prototype上的属性，当访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。几乎所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例。

参考[MDN继承与原型链](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)和[JavaScript深入之从原型到原型链](https://github.com/mqyqingfeng/Blog/issues/2)


### 其他
- [JavaScript 进阶问题列表](https://github.com/lydiahallie/javascript-questions/blob/master/zh-CN/README-zh_CN.md)

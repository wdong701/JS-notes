### 一、call
1、语法：function.call(thisArg, arg1, arg2, ...)
可以看出call有两个作用，一是改变this指向，另一个传递参数，代码如下：
```javascript
const obj = {
  value: 1,
}
  
function fn(arg) {
  console.log(this.value, arg); // 1, 2
}
  
fn.call(obj, 2);
```
2、实现思路
不使用显示绑定fn怎么拿到obj的value
```javascript
const obj = {
  value: 1,
  fn: function() {
    console.log(this.value); // 1
  }
}
obj.fn();
```
这样this就指向了obj，根据这个思路可以封装一个方法，将传入的this转换成这样的方式，那么当前this的指向就是我们想要的结果。注意fn不能写成箭头函数，this会绑到window，所以实现步骤为：

1. 将函数设置为传入对象的属性
2. 执行该函数
3. 删除该属性
```javascript
// 给obj添加属性func
obj.func = fn;
// 执行函数
obj.func();
// 删除添加的属性
delete obj.func;
```
3、模拟实现call
```javascript
Function.prototype.mycall = function (thisArg) {
  thisArg.func = this;
  thisArg.func();
  delete thisArg.func;
}
const obj = {
  value: 1,
}

function fn(arg) {
  console.log(this.value, arg); // 1, undefined
}

fn.mycall(obj, 2);
```
上面已经改变了this指向，但是传参没有拿到，因为传参不确定，可以使用剩余参数。
```javascript
Function.prototype.mycall = function (thisArg, ...args) {
  thisArg.func = this;
  thisArg.func(...args);
  delete thisArg.func;
}
const obj = {
  value: 1,
}

function fn(value1, value2) {
  console.log(this.value, ...arguments) // 1 2 [1, 2] {a: 1}
}

fn.mycall(obj, 2, [1,2], { a: 1 })
```
但是还有一种情况，函数本身还有返回值
```javascript
function fn(value1, value2) {
  console.log(this.value, ...arguments) // 1, 2, [1,2], { a: 1 }
  return {
    c: 1
  }
}

console.log(fn.mycall(obj, 2, [1,2], { a: 1 })) // undefined
```
解决方案：将执行的函数结果存下来，在return出来。所以方案如下：
```javascript
Function.prototype.mycall = function(thisArg, ...args) { 
  // 在传入的对象上设置属性为待执行函数
  thisArg.fn = this;
  // 执行函数
  const res = thisArg.fn(...args);
  // 删除属性
  delete thisArg.fn;
  // 返回执行结果
  return res;
}
```
最后，当this传null和undefined时，将是js执行环境的全局变量，浏览器中是window，node为global，以及设置一个原属性不重名的新属性，所以最终版代码如下：
```javascript
Function.prototype.mycall = function(thisArg, ...args) {   
  thisArg = thisArg || window;    
  const key = Symbol('key')
  thisArg[key] = this 
  var res = thisArg[key](...args)   
  delete thisArg[key]  
  return res
}
```
### 二、apply
1、语法func.apply(thisArg, [argsArray])
与call相比第二个参数为数组，思路和call一样
```javascript
Function.prototype.myApply = function (thisArg, args) {
  thisArg = thisArg || window; 
  const key = Symbol('key')
  thisArg[key] = this
  const res = thisArg[key](...args)
  delete thisArg[key]
  return res
}

const obj = {
  value: 1,
}
function fn() {
  console.log(this.value); // 1
  return [...arguments]
}
console.log(fn.myApply(obj, [1, 2])); // [1, 2]
```
### 三、bind
 bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。  
语法： function.bind(thisArg[, arg1[, arg2[, ...]]])
 与前两个的区别，bind会创建一个新的函数，返回一个函数，并允许传参。
```javascript
Function.prototype.myBind = function (thisArg, ...args) {
  return (...reArgs) => {
    return this.call(thisArg, ...args, ...reArgs)
  }
}
```


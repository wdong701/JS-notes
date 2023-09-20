## this相关
### 一、this的绑定
- this的绑定和函数声明位置无关，只取决于函数的调用方式。
  
1.默认绑定（独立函数调用）

```javascript
//1.普通函数独立调用
function foo() {
  console.log(this) //window
}
foo()
```
```javascript
//2.函数定义在对象中，但是独立调用
var obj = {
  bar() {
    console.log(this) //window
  }
}
var baz = obj.bar
baz()
//3.高阶函数
function foo(fn) {
  fn()
}
foo(obj.bar) //window
```
在严格模式下，全局对象无法使用默认绑定，this会绑定到undefined

2.隐式绑定

- 常见为通过某个对象进行调用（对象属性引用链中最后一层影响调用位置）
```javascript
function foo() {
  console.log(this.a) 
}

var obj2 = {
  a: 42,
  foo
}
var obj1 = {
  a: 2,
  obj2
}

obj1.obj2.foo() //42
```
**隐式丢失**：被隐式绑定的函数丢失绑定对象，会应用默认绑定。

例1：
```javascript
function foo() {
  console.log(this.a) 
}

var obj = {
  a: 2,
  foo
}
var bar = obj.foo
var a = 'global'

bar() // global
```
bar为obj.foo的引用，实际上引用foo函数本身，bar()是不带任何修饰的函数调用，此时应用默认绑定

例2：传入回调函数时
```javascript
function foo() {
  console.log(this.a) 
}

function bar(fn) {
  fn()
}

var obj = {
  a: 2,
  foo
}
var a = 'global'

bar(obj.foo) // global
```
3.显示绑定：call，apply，bind

4.new绑定

使用new调用函数执行操作

- 创建一个全新对象
- 新对象执行原型连接
- 新对象绑定到函数调用的this
- 如果函数没有返回其他对象，new表达式的函数调用会自动返回这个新对象
  
```javascript
function foo(a) {
  this.a = a
}
var bar = new foo(2)
console.log(bar.a);// 2
```

### 二、call，apply，bind
1.call和apply区别
- 传参格式不一样

  function.call(thisArg, arg1, arg2, ...)
  apply(thisArg, argsArray)

  **注意**：当这个函数处于非严格模式下，thisArg指定为null或undefined时会自动替换为全局对象，原始值会被包装。
```javascript
'use strict'
function foo() {
  console.log(this)
}
foo.call(undefined) //undefined
foo.call(null) //null
```
```javascript
function foo() {
  console.log(this)
}
foo.call(undefined) //window
foo.call(null) //window
```
2.bind

bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。
```javascript
function foo() {
  console.log(this)
}
var obj = { a: 2 }
var bar = foo.bind(obj)
bar() // { a: 2 }
```
```javascript
function foo(name, age, height, address) {
  console.log('this:', this)
  console.log('参数：', name, age, height, address);
} //'tom', 18, 1.8 beijing
var obj = { a: 2 }
var bar = foo.bind(obj, 'tom', 18, 1.8)
bar('beijing') // { a: 2 }
```
### 三、绑定优先级
- 显示绑定优先级高于隐式
```javascript
function foo() {
  console.log(this)
}
var obj = { foo }

obj.foo.apply('a') // String {'a'} 
obj.foo.call('a') // a(严格模式)
```
- new绑定优先级高于隐式

```javascript
var obj = {
  name:  '1',
  foo: function() {
    console.log(this); // foo {}
    console.log(this === obj); // false
  }
}
new obj.foo
```
- new优先级高于bind
```javascript
function foo() {
  console.log(this); // foo {}
}
var bindfn = foo.bind('a')
new bindfn
```
- bind优先级高于apply/call
```javascript
function foo() {
  console.log(this); // String {'a'}
}
var bindfn = foo.bind('a')
bindfn.call('b')
```
**总结**: new > bind > call/apply > 隐式绑定 > 默认绑定

### 四、判断this
1、函数如果在new中调用绑定，this指向新创建的对象。

2、通过call、apply、bind显示绑定，this指向绑定的对象。

3、函数是否在某个上下文对象中调用，this指向那个上下文对象。

4、都不是则使用默认绑定，严格模式下绑定到undefined，否则绑定到全局对象。

### 五、关于箭头函数
箭头函数没有自己的this、arguments。箭头函数不会创建自己的this，它只会从自己的作用域链的上层继承this。

### 六、几道this指向练习题
题一：

```javascript
var name = "window";

var person = {
  name: "person",
  sayName: function () {
    console.log(this.name);
  }
};

function sayName() {
  var sss = person.sayName;

  sss(); // 绑定: 默认绑定, window -> window

  person.sayName(); // 绑定: 隐式绑定, person -> person

  (person.sayName)(); // 绑定: 隐式绑定, person -> person

  (b = person.sayName)(); //  间接函数引用, window -> window
}

sayName();
```
题二：
```javascript
var name = 'window'

var person1 = {
  name: 'person1',
  foo1: function () {
    console.log(this.name)
  },
  foo2: () => console.log(this.name),
  foo3: function () {
    return function () {
      console.log(this.name)
    }
  },
  foo4: function () {
    return () => {
      console.log(this.name)
    }
  }
}

var person2 = { name: 'person2' }

person1.foo1(); // 隐式绑定: person1
person1.foo1.call(person2); // 显式绑定: person2

person1.foo2(); // 上层作用域: window
person1.foo2.call(person2); // 上层作用域: window

person1.foo3()(); // 默认绑定: window
person1.foo3.call(person2)(); //person1.foo3.call(person2)等同于foo3里的this指向person2 默认绑定: window
person1.foo3().call(person2); // 显式绑定: person2

person1.foo4()(); //person1.foo4()this指向person1，箭头函数上层找为person1
person1.foo4.call(person2)(); //person1.foo4.call(person2)this指向person2，箭头函数上层找为person2
person1.foo4().call(person2); // person1
```
题三：
```javascript
var name = 'window'
/*
  1.创建一个空的对象
  2.将这个空的对象赋值给this
  3.执行函数体中代码
  4.将这个新的对象默认返回
*/
function Person(name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
  },
  this.foo2 = () => console.log(this.name),
  this.foo3 = function () {
    return function () {
      console.log(this.name)
    }
  },
  this.foo4 = function () {
    return () => {
      console.log(this.name)
    }
  }
}

// person1/person都是对象(实例instance)
var person1 = new Person('person1')
var person2 = new Person('person2')

person1.foo1() // 隐式绑定: person1
person1.foo1.call(person2) // 显式绑定: person2

person1.foo2() // 上层作用域查找: person1
person1.foo2.call(person2) // 上层作用域查找: person1

person1.foo3()() // 默认绑定: window
person1.foo3.call(person2)() // 默认绑定: window
person1.foo3().call(person2) // 显式绑定: person2

person1.foo4()() // 上层作用域查找: person1(隐式绑定)
person1.foo4.call(person2)() //  上层作用域查找: person2(显式绑定)
person1.foo4().call(person2) // 上层作用域查找: person1(隐式绑定)
```
题四：
```javascript
var name = 'window'

function Person(name) {
  this.name = name
  this.obj = {
    name: 'obj',
    foo1: function () {
      return function () {
        console.log(this.name)
      }
    },
    foo2: function () {
      return () => {
        console.log(this.name)
      }
    }
  }
}

var person1 = new Person('person1')
var person2 = new Person('person2')

person1.obj.foo1()() //默认绑定：window
person1.obj.foo1.call(person2)() //默认绑定：window
person1.obj.foo1().call(person2) //显示绑定：person2

person1.obj.foo2()() //上层作用域查找：obj(隐式绑定)
person1.obj.foo2.call(person2)() //上层作用域查找：person2(显示绑定)
person1.obj.foo2().call(person2) //上层作用域查找：obj(隐式绑定)
```



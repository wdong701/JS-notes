### 一、promise
##### 1、是什么

- 是异步编程的一种解决方案
- 是一个容器，保存着某个未来才会结束的事件结果
- 是一个对象，可以获取异步操作的消息
##### 2、特点

- 对象的状态不受外界影响：它有三种状态，pending(进行中）、fulfilled(成功）、rejected(失败)，只有异步操作的结果可以决定当前是哪种状态，任何其他操作都无法改变这个状态。
- 一旦状态改变就不会再变，任何时候都可以得到这个结果：状态的改变只能从pending到fulfilled或者从pending到rejected，如果改变已经发生，再对promise对象添加回调函数，也会立即得到这个结果。
##### 3、优点

- 可以将异步操作以同步操作的流程表达出来，避免产生回调地狱
- 提供统一的接口，使得控制异步操作更加容易

##### 4、缺点

- 无法取消Permise，一旦新建就会立即执行，无法中途取消
- 如果不设置回调函数，内部抛出的错误不会反应到外部
- 当处于pending状态时，无法得知目前进展到哪一个阶段（刚开始还是要完成）

### 二、基本用法

 Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由 JavaScript 引擎提供。  

1. resolve函数的作用是将Promise对象的状态从pending变为fulfilled/resolved，在异步操作成功时调用，并将异步操作的结果作为参数传递出去
2. reject函数的作用是将Promise对象的状态从pending变为rejected，在异步操作失败时调用，并将异步操作的错误信息作为参数传递出去
   
```javascript
const promise = new Promise(function(resolve, reject) {
  console.log("1");
  resolve();
});

promise.then(function() {
  console.log("2");
});

console.log("3");

// 1
// 3
// 2
```
### 三、then和catch
then方法的作用是为Promise实例添加状态改变时的回调函数

- 第一个参数是resolve的回调函数
- 第二个参数是reject的回调函数
- then方法返回的是一个新的Promise实例，不是原来的实例

catch方法等于then方法第二个参数的别名，用于指定发生错误时的回调函数

- 如果该对象的状态变为resolve，则会调用then()方法指定的回调函数
- 如果异步操作抛出错误，状态就会变成rejected，调用catch()方法指定的回调函数
### 四、内部错误
Promise状态变成resolved，在抛出错误是无效的。
```javascript
const promise = new Promise(function(resolve, reject) {
  resolve("ok"); // 状态改为完成
  throw new Error("test"); // 抛出错误、但不会被捕获到
});
promise
  .then(function(value) {
    console.log(value); // ok
})
  .catch(function(error) {
    console.log(error); // 并没有执行，因为没有状态变为成功后不会再改变了
});
```
Promise 对象的错误具有冒泡性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个 catch 语句捕获。
```javascript
getJSON("/post/1.json")
  .then(function(post) {
    return getJSON(post.commentURL);
  })
  .then(function(comments) {
    // some code
  })
  .catch(function(error) {
    // 可以处理前面三个 Promise 产生的错误
  });
```
一般来说，不要在 then()方法里面定义 Reject 状态的回调函数（即 then 的第二个参数），总是使用 catch 方法。

**Promise 对象内部的错误不会影响代码运行**
```javascript
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为 x 没有声明
    resolve(x + 2);
  });
};

someAsyncThing().then(function() {
  console.log("everything is great");
});

setTimeout(() => {
  console.log(123);
}, 2000);
// Uncaught (in promise) ReferenceError: x is not defined
// 123
```
可以看到，promise 内部的错误不会影响外面的代码的运行。

**一般总是建议，Promise 对象后面要跟catch()方法，这样可以处理 Promise 内部发生的错误。catch()方法返回的还是一个 Promise 对象，因此后面还可以接着调用then()方法。**
```javascript
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为 x 没有声明
    resolve(x + 2);
  });
};

someAsyncThing()
  .catch(function(error) {
    console.log("oh no", error);
  })
  .then(function() {
    console.log("carry on");
  });
// oh no [ReferenceError: x is not defined]
// carry on
```
### 五、finally

用于指定不管promise对象最后的状态如何都会执行的操作。
finally方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是fulfilled还是rejected。这表明，finally方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。

### 六、Promise.all()

Promise.all()方法用于将多个 Promise 实例，包装成一个新的 Promise 实例
`const p = Promise.all([p1, p2, p3]);`
Promise.all()方法接受一个数组作为参数，p1、p2、p3都是 Promise 实例，如果不是，就会先调用下面讲到的Promise.resolve方法，将参数转为 Promise 实例，再进一步处理。
p的状态由p1、p2、p3决定，分成两种情况。
（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

```javascript
const p1 = new Promise((resolve, reject) => {
  resolve('hello');
})
.then(result => result)
.catch(e => e);

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了');
})
.then(result => result)
.catch(e => e);

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e));
// ["hello", Error: 报错了]
```
上面代码中，p1会resolved，p2首先会rejected，但是p2有自己的catch方法，该方法返回的是一个新的 Promise 实例，p2指向的实际上是这个实例。该实例执行完catch方法后，也会变成resolved，导致Promise.all()方法参数里面的两个实例都会resolved，因此会调用then方法指定的回调函数，而不会调用catch方法指定的回调函数。
如果p2没有自己的catch方法，就会调用Promise.all()的catch方法。

```javascript
const p1 = new Promise((resolve, reject) => {
  resolve('hello');
})
.then(result => result);

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了');
})
.then(result => result);

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e));
// Error: 报错了
```
### 七、Promise.race() 

Promise.race()方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。
`const p = Promise.all([p1, p2, p3]);`
上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

Promise.race()方法的参数与Promise.all()方法一样，如果不是 Promise 实例，就会先调用下面讲到的Promise.resolve()方法，将参数转为 Promise 实例，再进一步处理。

### 八、Promise.allSettled()

用来确定一组异步操作是否都结束了（不管成功或失败）。
Promise.allSettled()方法接受一个数组作为参数，数组的每个成员都是一个 Promise 对象，并返回一个新的 Promise 对象。只有等到参数数组的所有 Promise 对象都发生状态变更（不管是fulfilled还是rejected），返回的 Promise 对象才会发生状态变更。

### 九、Promise.any() 
该方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例返回。
只要参数实例有一个变成fulfilled状态，包装实例就会变成fulfilled状态；如果所有参数实例都变成rejected状态，包装实例就会变成rejected状态。
Promise.any()跟Promise.race()方法很像，只有一点不同，就是Promise.any()不会因为某个 Promise 变成rejected状态而结束，必须等到所有参数 Promise 变成rejected状态才会结束。

### 十、Promise.resolve()
有时需要将现有对象转为 Promise 对象，Promise.resolve()方法就起到这个作用。
Promise.resolve()方法的参数分成四种情况。

**（1）参数是一个 Promise 实例**

如果参数是 Promise 实例，那么Promise.resolve将不做任何修改、原封不动地返回这个实例。

**（2）参数是一个thenable对象**

thenable对象指的是具有then方法的对象，比如下面这个对象。

```javascript
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};
```
Promise.resolve()方法会将这个对象转为 Promise 对象，然后就立即执行thenable对象的then()方法。
```javascript
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function (value) {
  console.log(value);  // 42
});
```
上面代码中，thenable对象的then()方法执行后，对象p1的状态就变为resolved，从而立即执行最后那个then()方法指定的回调函数，输出42。

**（3）参数不是具有then()方法的对象，或根本就不是对象**

如果参数是一个原始值，或者是一个不具有then()方法的对象，则Promise.resolve()方法返回一个新的 Promise 对象，状态为resolved。
```javascript
const p = Promise.resolve('Hello');

p.then(function (s) {
  console.log(s)
});
// Hello
```
上面代码生成一个新的 Promise 对象的实例p。由于字符串Hello不属于异步操作（判断方法是字符串对象不具有 then 方法），返回 Promise 实例的状态从一生成就是resolved，所以回调函数会立即执行。Promise.resolve()方法的参数，会同时传给回调函数。

**（4）不带有任何参数**

Promise.resolve()方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。

所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve()方法。

```javascript
const p = Promise.resolve();

p.then(function () {
  // ...
});
```
上面代码的变量p就是一个 Promise 对象。
需要注意的是，立即resolve()的 Promise 对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮“事件循环”的开始时。
```javascript
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```
上面代码中，setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.resolve()在本轮“事件循环”结束时执行，console.log('one')则是立即执行，因此最先输出。
### 十一、Promise.reject() 
Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为rejected。
```javascript
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});
// 出错了
```
上面代码生成一个 Promise 对象的实例p，状态为rejected，回调函数会立即执行。

Promise.reject()方法的参数，会原封不动地作为reject的理由，变成后续方法的参数。

```javascript
Promise.reject('出错了')
.catch(e => {
  console.log(e === '出错了')
})
// true
```

上面代码中，Promise.reject()方法的参数是一个字符串，后面catch()方法的参数e就是这个字符串。

### 十二、resolve不同值的区别

情况一：如果resolve传入一个普通的值或者对象，那么这个值会作为then回调的参数； 

情况二：如果resolve中传入的是另外一个Promise，那么这个新Promise会决定原Promise的状态;

情况三：如果resolve中传入的是一个对象，并且这个对象有实现then方法，那么会执行该then方法，并且根据then方法的结 果来决定Promise的状态


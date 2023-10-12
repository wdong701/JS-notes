<!--
 * @Author: dlwan1
 * @Date: 2023-10-12 10:21:52
 * @LastEditTime: 2023-10-12 10:42:14
 * @LastEditors: dlwan1
 * @Description: 
 * @FilePath: \notes\es6~es13新特性.md
-->
### 一、es6
1. 变量（ let和const  ）：有块级作用域，没有作用域提升。
2. 模板字符串
3. 箭头函数：没有显示原型，不能作为构造函数使用new来创建对象
4. 解构赋值：let [a,,b] = [1,2,3]
5. symbol：表示独一无二的值
6. 函数默认参数和剩余参数
7. 对象部分：
- 对象的拓展运算符(...)用于拷贝目标对象所有可遍历的属性到当前对象。
  
-  新增了两个方法，assign和is。 assign用于**浅拷贝**源对象可枚举属性到目标对象。 is方法和（===）功能基本类似，用于判断两个值是否绝对相等。 他们仅有的两点区别是，is方法可以区分+0还是-0，还有就是它认为NaN是相等的。  
  
```javascript
let source = {a:{ b: 1},b: 2};
let target = {c: 3};
Object.assign(target, source);
console.log(target);  //{c: 3, a: {b:1}, b: 2}
source.a.b = 2;
console.log(target.a.b);  //2
```

```javascript
Object.is(1,1);//true
Object.is(1,true);//false
Object.is([],[]);//false
Object.is(+0,-0);//false
Object.is(NaN,NaN);//true
```

8. 字符串新方法：
-  startsWith()/endsWith()，判断字符串是否以参数字符串开头或结尾。  
-  repeat()方法按指定次数返回一个新的字符串。  
-  padStart()/padEnd()，用参数字符串按给定长度从前面或后面补全字符串，返回新字符串。  
9. Map和Set
- Set类似数组，但是元素不能重复，创建需要通过Set构造函数，通过forEach遍历set，另外Set支持for of遍历
  
- WeakSet中只能存放对象类型，不能存放基本数据库类型。WeakSet对元素的引用是弱引用，如果我们遍历获取其中元素就可能造成对象不能正常销毁，所以WeakSet不能遍历
  
- Map用于存储映射关系，之前我们用对象存储映射关系只能用字符串（ES6新增了Symbol）作为属性名（key），某些情况下我们可能希望通过其他类型作为key，比如对象，这个时候会自动将对象转成字符串来作为key，现在可以使用Map，Map遍历方式和set一样
  
- WeakMap只能存放对象类型，对元素的引用也是弱引用

10. Proxy、Reflect、Promise、模块化开发
    
### 二、es7

1. 幂操作
```javascript
//ES5求幂
math.pow(3,2)
//ES7求幂
let a=3**2; //结果：9
```

1. includes()判断字符串是否包含参数字符串，类似indexof，但这里不是查找下标

### 三、es8

1. 提供了 Object.values 来获取所有的value值
2. 通过 Object.entries 可以获取到一个数组，数组中会存放可枚举属性的键值对数组。（可以针对对象、数组、字符串进行操作）

![image.png](https://cdn.nlark.com/yuque/0/2023/png/29427284/1682329181541-cb4f2ff4-ca4b-4830-aade-64338804a145.png#averageHue=%23292d35&clientId=ub1aed214-1929-4&from=paste&height=437&id=ubea534b1&originHeight=546&originWidth=1293&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=98365&status=done&style=none&taskId=ud728fe97-2786-4963-b3b6-4c8cbb3dad5&title=&width=1034.4)
 3. async、await
  
### 四、es9

1. Promise新增了一个 finally方法
   
### 五、es10

1. Array的新方法flat和flatMap

- flat() 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。
- ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29427284/1682329421216-bae952c7-d129-4e68-88ef-c05012df3398.png#averageHue=%232a2f37&clientId=ub1aed214-1929-4&from=paste&height=234&id=qNaru&originHeight=292&originWidth=715&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=46409&status=done&style=none&taskId=ud28b3d2d-f5a6-4479-8521-245b536da2d&title=&width=572)
- flatMap() 方法首先使用映射函数映射每个元素，然后将结果压缩成一个新数组。
- 注意一：flatMap是先进行map操作，再做flat的操作；
- 注意二：flatMap中的flat相当于深度为1；
  
2. trimStart和trimEnd
   
### 六、es11

1. 动态import()
```javascript
//vue router中动态导入一个组件（动态方法）
component: () => import(/* webpackChunkName: "index" */ '../views/index.vue')
//ES6的import属性(静态属性)
import Vue from 'vue'
```
2. 新增BigIntBigInt（并不是新增了一个数据类型，只是让原本的整数可容纳的值更多。)
3. 空值合并操作符

  ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29427284/1682329940071-ab091aee-08e7-4886-9681-d8bbd9950a56.png#averageHue=%232c3039&clientId=ub1aed214-1929-4&from=paste&height=145&id=u015797ee&originHeight=260&originWidth=582&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=36033&status=done&style=none&taskId=uacbcbd76-ca4d-43bd-8add-0c931913792&title=&width=323.6000061035156)

4. 可选链，主要作用是让我们的代码在进行null和undefined判断时更加清晰和简洁

  ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29427284/1682329994928-49c9cf20-e12a-4ac0-bcff-3b6924ad9a2d.png#averageHue=%23292d35&clientId=ub1aed214-1929-4&from=paste&height=285&id=uaa91b59b&originHeight=578&originWidth=834&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=51648&status=done&style=none&taskId=u275f914b-8c60-416b-a80b-84f9a82f0dc&title=&width=411.20001220703125)

### 七、es12

1. String.prototype.replaceAll：全部替换
2. Promise的any方法
3. 新增逻辑赋值操作符： ??=, &&=, ||=
4. FinalizationRegistry 对象可以让你在对象被垃圾回收时请求一个回调
5. WeakRefs：如果我们默认将一个对象赋值给另外一个引用，那么这个引用是一个强引用，如果我们希望是一个弱引用的话，可以使用WeakRef

### 八、es13

1. method .at()

  ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29427284/1682337096293-e9f1049e-d82d-4fa6-8898-34db160f2dd9.png#averageHue=%232b303a&clientId=u4946a85f-800a-4&from=paste&height=175&id=u06e54be9&originHeight=360&originWidth=607&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=39480&status=done&style=none&taskId=u3eefc85d-4237-4036-839d-c9d0eef7e74&title=&width=294.6000061035156)

2. Object.hasOwn(obj, propKey)，用于判断一个对象中是否有某个自己的属性，和之前学习的Object.prototype.hasOwnProperty区别一：防止对象内部有重写hasOwnProperty，区别二：对于隐式原型指向null的对象， hasOwnProperty无法进行判断

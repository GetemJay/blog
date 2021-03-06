# 变量

* 先说一下`var`这哥们，他什么变量都可以声明，也没什么要求，所有的类型都OK，但声明之后还可以改。eg：

 ```js
 var a = 5;
 a = [1,1,1,2,3,,4] // 没错完全可以用，一点问题都没有
//  顺带一句：var定义的全局变量会自动添加全局window对象的属性
 ```

* 这样是不会报错的，有兄弟就会讲，不报错还不好吗？当然不好了，如果之后我要用之前的`a = 5`我去哪找去，这个其实还挺严重的，这是`var`让我很难受的一个点

* 下面说一个闭包的问题

```js
 var oLis = document.getElementsByTagName('li');
 for (var i = 0; i < oLis.length; i++) {
     oLis[i].addEventListener('click', funciton(){
         console.log(i) // 都知道这个地方得不到想要的结果
     })
    // 就是因为var这哥们的原因，var的作用域是函数作用域，没有块级作用域的属性，所以会用闭包给var一个自己的作用域
    funciton(j){
        oLis[j].addEventListener('click', funciton(){
         console.log(j) // 都知道这个地方得不到想要的结果
        })
    }(i)
    // 当然在ES6里面一个let完事，还要你什么闭包不闭包的，不过注意let有并发的问题，感兴趣的话可以测一下
 }
```

## 吐槽一下var，再说说js的几种变量吧

### js的数据类型

* 基础数据类型 ---> null，undefined，string，number，boolean，symbol，object

* 原始类型（Number、String、Boolean）和引用类型（Object、Array、Function）

* 我的理解：原始类型的值，即它们是最基本的数据类型，不能再细分了，是Number你只可能是数值，是String你就是字符串，是Boolean就只可能是true或者false；但是引用类型的值他们是可以继续往下分的，对象了的值可以是数组，也可以是方法，什么样的值都可合成对象，数组和函数就不用多说了，至于null和undefined，一般将它们看成两个特殊值。

* 说几种常见的检验变量类型的方法
  * typeof运算符
  * instanceof运算符
  * Object.prototype.toString方法

1. typeof运算符

```js
// 直接上代码才是我的风格
typeof 'a' // 'string'
typeof 325 // 'number'
typeof true // 'boolean'
typeof [1,2,3] // 'object'
typeof { a: 3, b: 5 } // 'object'
typeof function(){console.log(1)} // 'function'
// 可以看的出来typeof在Array和Object上面是不准的，那接下来的两种就直接比较Array和Object
```

2. instanceof运算符

```js
let arr = [1,1,3]
let obj = { a: 1 }
arr instanceof Array // true
arr instanceof Object // true
obj instanceof Object // true
obj instanceof Array // false
// instanceof 可以说也是分不出来的，当然提前已经知道是两种中的一种也是可以分出来的
```

3. Object.prototype.toString方法

```js
Object.prototype.toString.call([]) // "[object Array]"
Object.prototype.toString.call([]) // "[object Object]"
// 所以通过利用Object原型链上面的tuString方法是可以分开的，我们可以封装得到准确的获取类型的方法
function getType (type) {
    return Object.prototype.toString.call(type).match(/\[object (.*?)\]/)[1].toLowerCase()
}
```

<back-to-top />


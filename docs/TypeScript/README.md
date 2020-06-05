# ts的学习之路

### 任意值

```typescript
let myFavoriteNumber: any = 'seven'
myFavoriteNumber = 7
```

### 函数的类型

```typescript
function sum(x: number, y: number): number {
  return x + y
}
sum(1, 2)
// 输入数量不对的参数是不行的
// sum(1, 2, 3)
// sum(1)

// 函数表达式 TS中=>用来表示函数的定义 左边是输入类型 需要用括号括起来 右边是输出类型
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
  return x + y
}

// 用接口定义函数的类型
interface SearchFn {
  (source: string, subString: string): boolean
}
let mySearch: SearchFn
mySearch = function (source: string, subString: string) {
  return source.search(subString) !== -1
}
mySearch('123', '12')

// 可选参数 可选参数必须在必须参数后面
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + ' ' + lastName
  } else {
    return firstName
  }
}

// 参数默认值
function joinName(firstName: string, lastName: string = '葛') {
  return firstName + lastName
}

// 重载  为了表达清晰
function reverse(x: number): number
function reverse(x: string): string
function reverse(x: number | string): number | string {
  if (typeof x === 'number') {
    return Number(x.toString().split('').reverse().join(''))
  } else if (x === 'string') {
    return x.split('').reverse().join('')
  }
}
```

### 原始数据类型

```typescript
// 布尔值
let isDone: boolean = false
// 布尔对象
let createdByBoolean: Boolean = new Boolean(1)
// number
let num1: number = 6
let num2: number = NaN
// string
let str: string = '哈哈哈哈'
// 空值 void
let undefinedData: void = undefined
let nullData: void = null
function alertFn(): void {
  alert('1111111')
}
// null和undefined 与void的区别是 这俩是所有类型的子类型
let num3: number = undefined
let num4: number = null
```

### 声明文件

```typescript
/*
  declare var 声明全局变量
  declare function 声明全局方法
  declare class 声明全局类
  declare enum 声明全局枚举类型
  declare namespace 声明（含有子属性的）全局对象
  interface 和 type 声明全局类型
  export 导出变量
  export namespace 导出（含有子属性的）对象
  export default ES6 默认导出
  export = commonjs 导出模块
  export as namespace UMD 库声明全局变量
  declare global 扩展全局变量
  declare module 扩展模块
  /// <reference /> 三斜线指令
*/
```

### 接口

```typescript
interface Person {
  name: string
  age: number
}
let tom: Person = {
  name: '小明',
  age: 18
}

// 可选属性
interface Person1 {
  name: string
  age?: number
}
let bill: Person1 = {
  name: 'bill'
}

// 任意属性
interface Person2 {
  name: string
  age?: number
  [propName: string]: any
}
let GetemJay: Person2 = {
  name: 'GetemJay',
  age: 25,
  hobby: 'dota'
}

// 一旦定义了任意属性 确定属性和可选属性的类型都必须是它的类型的子集
// 下面这种情况不正确 任意属性的类型是string 但是age的类型是number 所以会报错
// interface Person {
//   name: string
//   age?: number
//   [propName: string]: string
// }
//
// let tom: Person = {
//   name: 'Tom',
//   age: 25,
//   gender: 'male'
// }

// 只读属性
interface Person3 {
  readonly id: number
  name: string
  age?: number
  [propName: string]: any
}
let ma: Person3 = {
  id: 123,
  name: 'ma',
  gender: 'male'
}
// 修改只读属性会报错
// ma.id = 345
```

### 数组的类型

```typescript
let arr1: number[] = [1, 2, 3]
// 出现其他类型会报错
// let arr1: number[] = [1, '2', 3]

// 数组泛型
let arr2: Array<number> = [1, 2, 3]

// 类数组 比如arguments IArguments是内置类型
function sum() {
  let args: IArguments = arguments
}

// 数组中用any
let arr3: any[] = [1, '2', '3', 4, {a: 5}]
```

### 泛型

```typescript
function createArray<T>(length: number, value: T): Array<T> {
  let result: Array<T> = []
  for (let i = 0; i < result.length; i++) {
    result[i] = value
  }
  return result
}
createArray(3, 'x')
```

### 类型断言

```typescript
// <类型>值
function getLength1(something: string | number): number {
  if ((<string>something).length) {
    return (<string>something).length
  } else {
    return something.toString().length
  }
}
// 值 as 类型
function getLength2(something: string | number): number {
  if ((something as string).length) {
    return (something as string).length
  } else {
    return something.toString().length
  }
}
getLength2(1)
getLength2('1')
```

### 联合类型

```typescript
let item: string | number
item = 'seven'
item = 7
```

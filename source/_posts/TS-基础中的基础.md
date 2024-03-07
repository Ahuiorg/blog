---
title: TS 基础中的基础
tags: TypeScript
abbrlink: 34588bfd
date: 2023-12-01 11:38:30
---

对于前端小伙伴来说，TypeScript 肯定都不陌生，但本人之前一直对 TS 了解的不多，这次决定全面学习一下 TS 并总结成博客文章

废话不多说，咱直接就开始吧 👊

### TypeScript 概览

1. **TypeScript 是什么？**

   简单理解就是 TypeScript 是增加了类型约束的 JavaScript，并且可以被编译成原生 JavaScript。

2. **为什么需要 TypeScript？**

   a. 与弱类型的 JS 结合，在编译期间增强类型检查，提前发现可能的缺陷

   b. 通过强类型约束可以放心地进行多人协作开发，保证项目的可维护性

   c. 与代码编辑器集成，提供自动补全、引用跳转等实用功能，提升开发效率

### 基本用法

下面来看看 TypeScript 的基本用法

#### 基本类型

##### 简单类型介绍

对于简单类型呢，就是 string、number、boolean、symbol、undefined 和 null，比较基础:

```
const str: string = 'hello';
const num: number = 1;
const isAfternoon: boolean = true;
let result: undefined = undefined;
let variable: null = null;
```

##### 自动推断类型

在某些场景，ts 是可以自己推断出类型的，比如:

- 初始化赋值的时候

```
let myName = 'Daniel Yang';

myName = 123; // 让我们看看将数字类型赋值给 myName 会发生什么？
```

`duang~ ts 发出了报错:`👇

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZKpQkhfmLEOgvUoicI7Ahj0ND2KEFsVEhEu0oOiapFE04kUV6sAicIbKIQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 对函数的返回值

```
function greet(name: string) {
    return `Hi, My name is ${name}.`;
}
```

ts 会自动推断出返回值类型:

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZC1eTkQOeDX2HVXHIYzte5JxTLscBG6PIhlG83Z1CTDybEt7U6jvAHw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 存在比较明显的上下文推断

```
const arr = [1, 2, 3];
```

在 map 方法中 ts 能推断出遍历元素的类型:

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZZAO1xrJMyyic883rujb0U4z1zSAS35GesfibBb5Z8WR1yxuIMazHYomw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

在这些场景下由于 ts 能推断出具体类型，所以是可以省略类型注释的，还能减少代码的长度😬

##### 特别的类型

下面介绍一些特别的类型

**1. any**

在 ts 里 有一个很特殊的 `any` 类型，对于不知道具体类型 或者就是不想写类型的情况，可以使用 `any` 来声明

不过这样会导致 ts 对该变量禁用检查，丢失掉 ts 该有的作用，所以需要避免过度使用 `any`

**2. unknown**

`unknown` 代表着任意的值，它和 `any` 非常像，但由于对 unknown 进行任意操作都是不合法的，所以它比直接使用 `any` 更安全

```
function fnWithAny(a: any) {
    a.b(); // ✅ it's OK.
}
    
function fnWithUnknown(a: unknown) {
    a.b(); // ❌ error: a is of type 'unknown'.
}
```

**3. never**

`never` 意味着永远不会发生，就像那年秋天，咳咳，扯远了。。。🤷‍♂️

对于抛出异常会提前终止执行的函数来说，适合对其返回类型声明为 never：

```
function fail(): never {
    throw new Error('oops')
}
```

看起来好像没啥用🤐

但其实 `never` 非常适合用于防止对联合类型有遗漏使用的情况，例如:

```
type Shape = 'circle' | 'square';

let shape: Shape;

switch (shape) {
    case 'circle':
        // some logic
        break;
    case 'square':
        // some logic
        break;
    default: // 按照正常逻辑是走不到 default 分支的
        const val: never = shape; // 此时 shape 为 never 类型
        break;
}
有意思的地方来了
```

如果有一天大家对 Shape 增加了新类型 `star`，但是忘记去新增 switch 的 case 分支，此时 default 分支里 ts 会报错导致代码编译不通过，将这个遗漏 case 分支的隐患暴露出来！

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZUkF9QiaVbX0oOreCb2xN1lgpibcZRDmNFaAdLzgY2y1VDu3IgXuM4NUQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZyjHN64GRZ4L1xyOwtVqNr6wuBAXejHpqmAr6m6xyLZVZ7HniaDK7Oaw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

绝![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZ9aJiaKhyO2hGUia92tdfPdR1thsiaS6XjsgJ8L4NrUoXsjLibicLe1wCcDw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**4. void**

`void` 意味着函数没有返回值或不返回任何明确的值：

```
function noop1(): void {
    console.log('noop')
}
    
function noop2(): void {
    console.log('Just nothing.');
    return;
}
```

#### 复杂类型

接下来咱们来看下如何在 ts 里给复杂对象添加类型声明

首先来认识一下 `type` 和 `interface` 关键字

##### 1. type 类型别名

在 ts 里，我们可以使用 `type` 关键词来给任意类型添加命名，这样可以方便引用和复用:

```
// 添加 Point 的类型别名
type Point = {
    x: number;
    y: number;
}

function printCoord(pt: Point) {
    console.log("coordinate's x and y is: ", pt.x, pt.y);
}
```

同时我们可以使用 `&` 符号将多个 type 进行**组合**:

```
type Animal = {
    name: string;
    eat: () => void;
}

type DogAction = {
    bark: () => void;
    walk: () => void;
}

type Dog = Animal & DogAction; // 组合

let dog: Dog;
dog.walk();
```

##### 2. interface 接口类型

`interface` 是另一种用来声明对象类型的方式:

```
interface Point {
    x: number;
    y: number;
}

function printCoord(pt: Point) {
    console.log("coordinate's x and y is: ", pt.x, pt.y);
}
```

我们可以使用 `extends` 关键字对 interface 进行**继承**:

```
interface Animal {
    name: string;
    eat: () => void;
}

interface Dog extends Animal { // 继承
    bark: () => void;
    walk: () => void;
}

let dog: Dog;
dog.walk();
```

既然有两种类型声明的方式，那么问题来了，`type` 和 `interface` 有啥区别呢？🤔

##### type 和 interface 的区别

`type` 和 `interface` 主要有以下几个区别:

1. interface 只能声明对象类型，但 type 除了对象类型以外，还可以声明简单类型和 union 联合类型

   ```
   // 对象类型
   interface Info {
       name: string;
       desc: string;
   }
   type Info = {
       name: string;
       desc: string;
   }
   
   // type 还可以声明简单类型和联合类型
   type name = string;
   type value = string | number;
   ```

2. interface 的重复声明可以合并，然而 type 不能重复声明:

   ```
   // interface 可以重复声明，声明的属性会进行合并
   interface Info {
       name: string;
   }
   
   interface Info {
       desc: string;
   }
   
   type Info = {
       name: string;
   }
   
   type Info = { // ❌ Error: type 不能重复声明
       desc: string;
   }
   ```

3. type 和 interface 实现类型扩展的方式不同

   type 通过 `&` 符号进行类型合并，而 interface 通过 `extends` 关键词实现继承

   ```
   interface A {
       a: string;
   }
   
   interface B extends A {
       b: number;
   }
   
   🤗 // interface B => { a: string; b: number; }
   
   type A = {
       a: string;
   }
   
   type B = A & {
       b: number;
   }
   
   🤗 // type B => { a: string; b: number; }
   ```

##### 3. 对象

讲完了类型声明的方式，我们来看看在 ts 里如何对对象进行类型声明，如下所示👇:

```
interface Info {
    name: string;
    desc: string;
}
```

同时我们可以用 `?` 和 `readonly` 修饰符来修饰对象属性:

- `?` 是可选修饰符，意味着该属性可以不赋值

  ```
  type Info = {
      name: string;
      phone?: string; // phone => string | undefined
  }
  ```

- `readonly` 是只读修饰符，表示该属性初始化后不能再次修改

  ```
  type Info = {
      readonly name: string;
  }
  
  let info: Info = {
      name: 'Daniel'
  }
  info.name = 'Tom'; // ❌ Error: Cannot assign to 'name' because it is a read-only property.
  ```

在使用可选属性前需要检查属性是否存在，否则 ts 会产生报错提示:

```
function printName(obj: { first: string, last?: string }) {
    console.log(obj.last.toUpperCase()); // ❌ Error: 'obj.last' is possibly 'undefined'.

    if (obj.last !== undefined) {
        console.log(obj.last.toUpperCase()); // ✅ OK.
    }

    console.log(obj.last?.toUpperCase()); // 或者使用 JS 的 ?. 语法糖 ✅
}
```

对于 `readonly` 来说虽然不会真的改变属性的性质，但会在编译期的类型检查期间禁止属性的重新写入:

```
function doSomething(obj: { readonly message: string }) {
    obj.message = 'hello'; // ❌ Error: Cannot assign to 'message' because it is a read-only property.
}
```

`readonly` 修饰符与 `const` 声明挺类似的，它并不意味着属性的值完全不能修改，而是指不能再重新更新属性的引用:

```
type PersonalInfo = {
    readonly baseInfo: { // baseInfo 是一个对象
        name: string;
        gender: string;
        age: number;
    }
}

function getPersonalInfo(person: PersonalInfo) {
    person.baseInfo.age ++; // ✅ 可以更新它的属性值

    person.baseInfo = { // ❌ 但不能更新它的引用
        name: 'Yang',
        //...
    }
}
```

##### 4. 数组

对数组来说，它的类型声明有两种方式，以字符串数组为例:

- `string[]`
- `Array<string>`

这两种写法的结果没有区别，只是第二种是泛型 `U<T>` 的写法，我们稍后再详细介绍泛型😌

与对象属性一样，我们也可以将数组声明为只读数组，同样有两种方式:

- `ReadonlyArray<string>`
- `readonly string[]`

这样使得数组内容不可更改:

```
const arr: readonly string[] = ['apple', 'banana'];

arr[2] = 'orange'; // ❌ Error: Index signature in type 'readonly string[]' only permits reading.
arr1.push('orange'); // ❌ Error: Property 'push' does not exist on type 'readonly string[]'
```

##### 5. 函数

对函数来说，需要声明类型的地方有 `函数参数` 和 `函数返回值`，例如:

```
function getMax(a: number, b: number): string {
    return `The max is ${Math.max(a, b)}`;
}
```

同样地，我们也可以声明可选参数和只读参数:

```
function fixed(n: number, digit?: number) {
    if (digit !== undefined) {
        return n.toFixed(digit);
    }
    return n.toFixed();
}

function fn(obj: { readonly a: number }) {
    console.log('obj.a is: ', obj.a);

    obj.a ++; // ❌ Error: Cannot assign to 'a' because it is a read-only property.
}
```

#### 常用类型

接下来介绍几种常用的类型

##### union 联合类型

联合类型是将两个以上的类型组合起来的形式，表示某个值可以是其中任意一个类型:

```
function printId(id: number | string) {
    console.log('Your ID is: ', id);
}

printId(101); // ✅ OK.
printId('202'); // ✅ OK.
printId({ id: 303 }); // ❌ Error: Argument of type '{ id: number; }' is not assignable to parameter of type 'string | number'.
```

除了类型联合外，咱们还可以联合具体的值，这样在代码编辑器里还能方便地增加提示:

```
function printText(s: string, alignment: 'left' | 'right' | 'center') {}
```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZwnWPnPkfs4toJexeOpbppETFS6IlufRiaVZTbZ4vWoRO7MuXlq64baQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

💣 需要注意的是，在 ts 里使用联合类型时，只有当某个属性是所有类型所共有的才可以直接用

比如某个联合类型是 `string | number`，如果直接使用只存在于 `string` 类型上的属性和方法是会喜提报错的 🙃:

```
function print(val: string | number) {
    if (typeof val.split === 'function') { // ❌ Error: Property 'split' does not exist on type 'number'.
        console.log(val.split(''));
    }
}
```

咱就是说在使用某一类型特有的属性之前，需要通过明确的类型判断让 ts 知道变量具体的类型，这样就能正常使用类型所对应的属性和方法了

偶总结了下 => 至少有以下 **几种方式** 可以用来更明确地判断变量的类型:

1. 使用 `typeof` 操作符

   ```
   function padLeft(padding: number | string, input: string) {
       if (typeof padding === 'number') { // 使用 typeof 明确变量的类型
           return ''.repeat(padding) + input;
       }
       return `${padding}${input}`;
   }
   ```

2. 使用 `in` 操作符

   ```
   type Fish = { swim: () => void };
   type Bird = { fly: () => void };
   
   function move(animal: Fish | Bird) {
       if ('swim' in animal) { // 检查 swim 是否存在于 animal 原型链上，即是否为 Fish 类型
           animal.swim();
       } else {
           animal.fly();
       }
   }
   ```

3. 使用 `instanceof` 操作符

   ```
   function logValue(x: Date | string) {
       if (x instanceof Date) { // 是否为 Date 类型实例
           console.log(x.toUTCString());
       } else {
           console.log(x);
       }
   }
   ```

4. 使用自定义类型预测方法

   除了使用 JS 本身的语言能力来做，咱也可以自定义一些类型判断方法

   比如我们需要判断一个变量究竟是 `Fish` 类型还是 `Bird` 类型，可以这样写:

   ```
   function isFish(pet: Fish | Bird): pet is Fish {
       return (pet as Fish).swim !== undefined; // 验证下 pet 变量上是否存在 swim 属性
   }
   ```

   然后放在条件判断里就好了:

   ```
   if (isFish(pet)) {
       pet.swim();
   } else {
       pet.fly();
   }
   ```

##### enum 枚举

enum 枚举是 ts 在 js 语法之外新增的特性，它允许咱们定义一组命名常量，比如:

```
enum NumericDirection {
    Up, // 默认从 0 开始，后面的变量如果没有赋值则继续加 1
    Down, // 1
    Left, // 2
    Right, // 3
}

enum StringDirection {
    UP = 'UP',
    Down = 'Down',
    Left = 'Left',
    Right = 'Right',
}
```

简单来说就是:

- 数字类型的枚举默认值为 0，后面的成员如果没有赋值则继续累加 1
- 字符类型的枚举必须要赋值

枚举成员也可以是混合类型，例如这样:

```
enum MixedType {
    A, // 0
    B: 'B'
}
```

比较有意思的是枚举其实是真实的对象，所以在代码里可以作为值直接使用:

```
enum Response {
    NO, // 0
    YES, // 1
}

// 作为类型的值
function handleResponse(type: Response, message: string) {}
handleResponse(Response.YES, 'success')

// 传递给函数参数
function fn(n: number) {
    if (n === Response.YES) {}
}

fn(Response.NO);
```

如果是这样，那问题又来了: `ts 的枚举和 js 的对象有什么区别呢?` 👻

emm... 枚举与对象主要有两点不同:

- 数字类型的枚举会生成 `反向映射`，可以通过枚举的值获取到对应的键 key:

  ```
  enum NumericEnum {
      LEFT = 1,
      RIGHT = 2,
  }
  
  NumericEnum[NumericEnum.LEFT]; // 'LEFT'
  NumericEnum[1]; // 'LEFT'
  
  // 让我们打印下 NumericEnum 的 key
  for (const key of Object.keys(NumericEnum)) console.log(key)
  // '1'
  // '2'
  // 'LEFT'
  // 'RIGHT'
  // 。。。不是很明白为什么要这样设计？🤷‍♂️
  ```

- 枚举成员是只读类型

  ```
  NumericEnum['LEFT'] = 3; // ❌ Error: Cannot assign to 'LEFT' because it is a read-only property.
  ```

##### Tuple 元组

介绍完枚举，我们来认识下 Tuple 元组

这名字听起来很高大上，但其实。。。它就是数组而已

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZ5TBlbUeeaa3H69CgdTlMrNT5lMt6Wd2Zma7ic56sZIFOJZ22icNeJAxQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

不过在元组里可以混合着不同类型，比如: `pair: [string, number]` 这样子，它就属于元组

由于元组一般是知道元素数量和对应的类型，所以 ts 可以对元组的下标访问是否越界和具体元素的操作是否合法做检查:

```
function doSomething(pair: [string, number]) {
    console.log('first value is: ', pair[0]); // ✅ It's OK.
    console.log('third value is: ', pair[2]); // ❌ Error: Tuple type '[string, number]' of length '2' has no element at index '2'.
    console.log(pair[1].split('-')); // ❌ Error: Property 'split' does not exist on type 'number'.
}
```

为什么说是一般呢，因为元组里可以有可选元素和扩展元素，它们会造成元组的实际长度不确定

- 可选元素：咱可以在元素类型后面增加 ? 表示其为可选元素，需要注意可选元素只能出现在队尾

  ```
  type TupleArray = [number, string, boolean?];
  const arr1: TupleArray = [1, '2']; // ✅ OK.
  const arr2: TupleArray = [1, '2', true]; // ✅ OK.
  ```

- 扩展元素：和 js 语法一样，咱可以用在类型前添加 ... 表示它是一个扩展元素:

  ```
  type StringNumberBooleans = [string, number, ...boolean[]]; // 表示前两个元素分别是字符和数字类型，剩下的元素都是布尔类型
  type StringBooleansNumber = [string, ...boolean[], number]; // 表示第一个和最后一个元素分别是字符和数字类型，中间的元素都是布尔类型
  type BooleansStringNumber = [...boolean[], string, number]; //  表示最后两个元素分别是字符和数字类型，前面的元素都是布尔类型
  ```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZ3DqGeMbKG3a9kT6ZjcCeUfWQXx3mp77PfsqJKkDx0aYFutGSB3Kt8A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 进阶用法

> 恭喜你，能看到这里的人都是大佬，下面让我们来学一些 ts 的进阶用法😎

#### 函数

##### 函数重载

如果某个函数能够以不同的参数数量和参数类型来调用，那在 ts 里该如何对该函数进行类型声明呢？

答案是 => 我们可以 **定义多个函数签名**

比如我们要写一个展示日期的方法，该方法可以接收一个`数字类型的时间戳参数` 或 `具体年、月、日三个参数`，那么可以这样写函数的类型声明:

```
// 函数签名
function makeDate(timestamp: number): Date;
function makeDate(year: number, month: number, day: number): Date;

// 函数实现
function makeDate(yOrTimestamp: number, month?: number, day?: number): Date {
    if (month !== undefined && day !== undefined) {
        return new Date(yOrTimestamp, month, day);
    }
    return new Date(yOrTimestamp);
}

// 函数调用
const d1 = makeDate(123456789); // ✅ OK.
const d2 = makeDate(2023, 7, 30); // ✅ OK.
const d3 = makeDate(2016, 10); // ❌ Error: No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
```

⚠️不过需要强调的是，**如果能用 union 联合类型声明的，就不要用重载来声明**，否则会把简单问题复杂化

比如我们需要写一个返回字符串或数组长度的方法，假设使用重载来进行类型声明:

```
// 函数签名
function len(s: string): number;
function len(arr: any[]): number;

// 函数实现
function len(x: any) {
    return x.length;
}
```

在普通调用下没有问题:

```
len('hello'); // ✅ OK.
len([1, 2, 3]); // ✅ OK.
```

但如果像下面这样调用，ts 就会报错:

```
len(Math.random() > 0.5 ? 'hello' : [1, 2, 3]); // ❌ Error: 
```

因为此时参数类型在编译时没法确定，不能单独匹配任意一个函数签名:

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZCQ3GITwgdOnXr0Gz0eJHic8Dwy9GtWYarcLbS28IibKUuVLR6UZ3olmA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

但冷静下来想一想 🤔，在这种参数数量和返回值类型都相同的情况下，直接使用 union 联合类型不香吗:

```
function len(x: string | any[]): number {
    return x.length;
}

len(Math.random() > 0.5 ? 'hello' : [1, 2, 3]); // ✅ OK.
完美解决 (o゜▽゜)o☆[BINGO!]
```

##### 函数泛型

下面我们来了解下 ts 里一个比较重要的概念: `泛型`

泛型是用来描述同一类型在多个值之间的关联性🌟

比如某个方法需要返回数组参数的第一个元素，虽然可以像这样写类型声明:

```
function getFirstElement(arr: any[]) {
    return arr[0];
}
```

但这样会导致方法的返回值是 `any` 类型，有点简单粗暴，表达不了返回值和入参的关系

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZTLPgMM228SINY3F7fg3P3x7ocd0aYQWYQxBFo39JQaib8B1Rum1ic3ZQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

如果返回值的类型能明确地与入参数组的元素类型关联上就好了😌

此时我们就可以使用 **泛型** 来满足这个需求，如下:

```
function getFirstElement<Type>(arr: Type[]): Type {
    return arr[0];
}
```

See? ! 通过在函数签名处添加一个类型参数 `Type` 并用在参数列表和返回值声明里，我们就在它们俩之间建立了联系

现在当我们调用函数时，返回值的类型将会与数组元素的类型一致:

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZgdpFdjsg0g6JwBl5FSa6xSG5kJpwNOPrgapgJCHPb8Af95QK11WRwg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)demo.png

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZQuglYlGJuIQfpn03wYVBGUFpzTM4Wh2tpROH3mia4G7XyMY3Z5LVdwQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

Great！🥳

同时我们还可以使用 `extends` 关键字 **对泛型增加限制**

比如我们需要实现一个 `在两元素中返回 length 属性最大的那个元素` 方法:

```
function getLonger<Type extends { length: number }>(a: Type, b: Type): Type {
    if (a.length > b.length) {
        return a;
    }
    return b;
}
```

这样就限制了该泛型必须具有 number 类型的 length 属性:

```
getLonger(10, 20); // ❌ Error: Argument of type 'number' is not assignable to parameter of type '{ length: number; }'.
getLonger([10], [20]); // ✅ OK.
```

#### 对象

##### 索引签名 index signature

在实际项目中会存在这样一种情况: 咱不知道一个类型里所有的属性值，但巧的是咱知道属性 key 和对应值的类型

此时就可以用索引签名来进行类型声明

比如可以这样声明一个下标是数字、值是字符串的对象:

```
interface StringArray {
    [index: number]: string;
}

const myArray: StringArray = getStringArray();
myArrsy[0]; // type: string;
```

But 只有 `string`、`number` 和 `symbol` 可以用作对象 key 的类型，这也符合 JS 语言中对象 key 类型的范围

如果对象的属性有不同类型，我们可以用 union 联合类型来声明值的类型:

```
interface NumberOrStringDic {
    [key: string]: number | string;
    length: number;
    name: string; // ✅ It's OK.
}
```

最后，我们也可以给索引签名增加 readonly 前缀来防止属性被重新赋值:

```
interface ReadonlyStringArray {
    readonly [index: number]: string;
}

const myArray: ReadonlyStringArray = getReadonlyStringArray();
myArray[0] = 'Daniel'; // ❌ Error: Index signature in type 'ReadonlyStringArray' only permits reading.
```

##### 对象泛型

与函数一样，对象也存在泛型声明 🤪

假设有这样一个对象 Box，它有一个包含任意类型的 content 属性，讲道理我们可以这样声明:

```
interface Box {
    content: any;
}
```

这没有问题，但使用 `any` 会导致 ts 对 `content` 属性移除了类型检查，比如:

```
const box: Box = {
    content: 'string type'
}

box.content(); // 字符串不能直接作为方法调用，但此时 ts 没有及时给出报错🙁
```

在这种情况下我们就可以对 Box 对象进行泛型声明

可以这样理解下面的声明: `Box 的 Type 就是 content 属性的类型`

```
interface Box<Type> {
    content: Type;
}
```

然后重点是 **我们在引用 Box 类型的时候需要给出 Type 的具体类型**，例如：

```
const box: Box<string> = {
    content: 'string value'
}
```

这样 ts 会明确知道 `box.content` 是 `string` 类型，从对 `box.content` 的调用做出准确的检查: 🤗

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZCHQVqwHPf8S8DRiaAL3o6SLZmzZEq7oMFfXhp2ugqJKPeaE0B3U0f1Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

另外我们还可以用 `type` 来声明泛型:

```
type Box<Type> = {
    content: Type;
}
```

同时因为 `type` 不仅可以声明对象类型，我们还能用 `type` 来声明一些泛型的辅助类型，例如:

```
type OrNull<T> = T | null;

type OneOrMany<T> = T | T[];

type OneOrManyOrNull<T> = OrNull<OneOrMany<T>>;

// 应用
type OneOrManyOrNullStrings = OneOrManyOrNull<string>;
```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/udZl15qqib0MZ0KCRLHicjHZaVWibY31dzZOmpw190y0vGABRdpESicnUC7V1DMWKmibY6vFh7OLqxvNqhzicUJO607Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 实用工具类型

文章的最后咱们来认识一些实用的工具类型吧 🔨

##### 1. Partial<Type>

返回一个与 Type 属性相同但全被设为可选的新类型:

```
interface Todo {
    title: string;
    desc: string;
}

let optionalTodo: Partial<Todo>;
/**
   {
       title?: string;
       desc?: string;
   }
*/
```

##### 2. Required<Type>

与 Partial 相反，返回一个与 Type 属性相同但全被设为必填的新类型:

```
interface Info {
    name?: string;
    age?: number;
}

let requiredInfo: Required<Info>;
/**
    {
       name: string;
       age: number;
    }
*/
```

##### 3. Pick<Type, Keys>

从 Type 里挑出指定的 Keys 来构造一个新类型:

```
interface Todo {
    title: string;
    desc: string;
    completed: boolean;
}

type TodoPreview = Pick<Todo, 'title' | 'completed'>;
/**
    {
       title: string;
       completed: boolean;
    }
*/
```

##### 4. Omit<Type, Keys>

与 Pick 相反，从 Type 里移除掉指定的 Keys 来构造一个新类型:

```
interface Todo {
    title: string;
    desc: string;
    completed: boolean;
}

type TodoPreview = Omit<Todo, 'desc' | 'completed'>;
/**
    {
       title: string;
    }
*/
```

##### 5. Extract<UnionType, ExtractedMembers>

取 UnionType 和 ExtractedMembers 的交集来构造一个新类型:

```
type T0 = Extract<'a' | 'b' | 'c', 'a' | 'f'>; // T0: 'a'

type T1 = Extract<string | number | (() => void), Function>; // T1: () => void
```

##### 6. Exclude<UnionType, ExcludedMembers>

从 UnionType 里移除掉 ExtractedMembers 存在的类型来构造一个新类型:

```
type T0 = Exclude<'a' | 'b' | 'c', 'a'>; // T0: 'b' | 'c'

type T1 = Exclude<string | number | (() => void), Function>; // T1: string | number
```

##### 7. NonNullable<Type>

从 Type 里移除掉 `undefined` 和 `null` 来构造一个新类型:

```
type T0 = NonNullable<string | number | undefined | null>; // T0: string | number
```

------

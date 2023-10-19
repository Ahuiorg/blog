---
title: 'ts objecy[key] 报错'
tags: ts
abbrlink: d55bdcba
date: 2023-09-21 10:48:25
---

原文： https://fettblog.eu/typescript-better-object-keys/

译文： 

注意：要非常小心使用这个技术。最好查看我的新方法。

TypeScript在lib.d.ts中的预定义类型通常是非常良好类型化的，并提供了大量关于如何使用内置功能以及提供额外类型安全性的信息。直到它们不是。考虑以下带有对象类型Person的示例：

```typescript
type Person = {
  name: string, age: number, id: number,
}
declare const me: Person;

Object.keys(me).forEach(key => {
  // 💥 下一行在我们这里抛出了红色波浪线
  console.log(me[key])
})
```

我们有一个类型为Person的对象，我们想使用Object.keys获取所有键作为字符串，然后使用这些键在映射或forEach循环中访问每个属性，以在严格模式下对其进行某些操作，但是我们得到了红色波浪线。这是错误消息：

"Element implicitly has an 'any' type because expression of type 'string' can't be used to index type 'Person'. No index signature with a parameter of type 'string' was found on type 'Person'"

那么发生了什么？Object.keys的类型声明如下：

```typescript
interface ObjectConstructor {
  //... 
  keys(o: object): string[]
  keys(o: {}): string[]
}
```

这两个重载都接受任何对象作为输入，并返回一个字符串数组作为输出。这是正确的和预期的行为。只是对于我们已经知道更多信息的情况，以及TypeScript应该了解更多信息的情况下，它太泛化了。

string是我们可以从Person访问的实际键的超集。具体的子集将是name | age | id。这也是TypeScript允许我们从Person索引的值的集合。对于每个其他字符串，TypeScript说它可能是，但索引值可以是任何东西。在严格模式下，任何都是不允许的，除非明确声明。

重要提示：这很可能有原因。或者更具体的类型在已建立的库中会引起问题。或者行为太复杂，无法在类型中总结。或者，只是有更重要的事情。这并不意味着更好的类型定义不会在某个时候出现。

但仍然，我们能做什么呢？

选项1. 类型转换#
最糟糕的解决方案是关闭noImplicitAny。这是一个引发错误和错误类型的开放之门。最明显的解决方案是类型转换。我们可以将对象转换为any以允许发生一切。

```typescript
Object.keys(me).forEach((key) => {
  console.log((me as any)[key])
})
```

不够好。或者我们可以将key参数强制转换为keyof Person，以确保TypeScript了解我们的目标。

```typescript
Object.keys(me).forEach((key) => {
  console.log(me[key as keyof Person])
})
```

更好一些。但仍然不够好。这是TypeScript应该自己做的事情！所以如果TypeScript还不知道，我们可以开始教会TypeScript如何做。

选项2. 扩展Object构造函数#
由于接口的声明合并功能，我们可以使用我们自己的类型定义来扩展ObjectConstructor接口。我们可以直接在需要它的地方进行此操作，或者创建自己的环境声明文件。

我们打开接口，并为keys编写另一个重载。这次，我们希望非常具体地描述我们得到的对象值，并根据其形状决定返回什么。

这是行为：

- 如果我们传递一个数字，我们得到一个空数组。
- 如果我们传递一个字符串或数组，我们得到一个字符串数组作为返回值。此字符串数组包含数字索引的字符串表示，以索引数组或字符串的位置。这意味着字符串数组的长度与其输入相同。
- 对于任何实际对象，我们返回它的键。
我们为此构建了一个辅助类型。这是一个条件类型，描述了上面的行为。

```typescript
type ObjectKeys<T> = 
  T extends object ? (keyof T)[] :
  T extends number ? [] :
  T extends Array<any> | string ? string[] :
  never;
```

在我的条件类型中，我通常以never结束。这给了我信号，要么我在声明中忘记了东西，要么在代码中做了完全错误的事情。无论哪种情况，这都是看到有问题的指示器。

现在，我们打开ObjectConstructor接口并为keys添加另一个重载。我们定义一个泛型类型变量，返回值基于条件类型ObjectKeys。

```typescript
interface ObjectConstructor {
  keys<T>(o: T): ObjectKeys<T>
}
```

同样，由于这是一个接口，我们可以在需要它的地方进行修补定义。一旦我们将具体对象传递给Object.keys，我们就将泛型类型变量T绑定到此对象。这意味着我们的条件可以提供关于返回值的确切信息。由于我们的定义是所有三个keys声明中最具体的，TypeScript默认使用这个。

我们的小示例不再向我们抛出红色波浪线。

```typescript
Object.keys(me).forEach((key) => {
  // typeof key = 'id' | 'name' | 'age'
  console.log(me[key])
})
```

key的类型现在是'id' | 'name' | 'age'，正如我们希

望的那样。而且对于所有其他情况，我们都获得了适当的返回值。

重要提示：传递数组或字符串的行为并没有显着变化。但这是一个很好的指示器，你的代码可能有问题。与空数组一样。仍然，我们保留了内置功能的行为。

扩展现有接口是一种很好的方式，可以选择地加入到类型中，在某种情况下，我们无法获取所需信息。

感谢与我合作的Mirjam的提示👏

进一步阅读#
Dan Vanderkam给我指出了为什么Object.keys不返回keyof T的Anders的问题。阅读GitHub问题评论以获取更多详细信息。TLDR：尽管keyof T在类型级别世界中是有效的，在运行时对象可以有更多的键。Lenz也有一个很好的例子。

问题在于您对类型合同的期望以及您如何处理Object.keys在一般情况下。因此，请确保小心处理此修补程序！ 

Dan还指出了他的一篇文章，详细介绍了如何遍历对象的策略。一定要查看！

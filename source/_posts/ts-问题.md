---
title: ts 问题
tags: ts
abbrlink: 9d2bd8d6
date: 2023-09-21 10:36:21
---

原文链接：[How not to learn TypeScript](https://link.zhihu.com/?target=https%3A//fettblog.eu/how-not-to-learn-typescript/%3Futm_campaign%3Dsebastien_lorber_newsletter%26utm_medium%3Demail%26utm_source%3DRevue%20newsletter)

全文 7747 字，大约 15 分钟。

译者注：**这篇文章对于初学 TypeScript 的同学应该是有帮助的，但是要注意，本文更多关注于初学，其中的建议在实际工作中不一定有用，甚至是不对的。但是，在初学 TypeScript 时，这些内容，还是有一定帮助的。同时我也相信，只要读者坚持搞下去，不用多长时间，应该也能明白这些问题。**

“我和 TypeScript 永远不是好朋友！“哎，我经常能听到这样的抱怨。学习 TypeScript，即便是在 2022 年，看起来也不是件容易的事儿。因为很多原因，尤其是有 Java 和 C# 经验的人会发现，在 TypeScript 中，和他们的预期是不一样的。经常写 JavaScript 的人又会被 TypeScript 的[编译器](https://www.zhihu.com/search?q=编译器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})搞的崩溃。这里是，我发现的一些新手开始学  TypeScript ，经常会犯的问题。我希望他们能帮助到你。

这篇文章深受 Deny 的[如果学习错误的 Rust](https://link.zhihu.com/?target=https%3A//dystroy.org/blog/how-not-to-learn-rust/)的影响，我也推荐你们看看这篇文章。

## 错误1: 忽视 JavaScript

从一开始， TypeScript 有一句著名的广告语：“TypeScript 是 JavaScript 的超集”。这句话是指，JavaScript 本身就是 TypeScript 的一部分！所有的 JavaScript！选择 TypeScript 并没有给你不去学 JavaScript 的权利，实际上，JavaScript 的怪异的行为依然存在。但是 TypeScript 帮助你更容易理解它们。

你可以看看[我的博客](https://www.zhihu.com/search?q=我的博客&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})关于[错误处理](https://link.zhihu.com/?target=https%3A//fettblog.eu/typescript-typing-catch-clauses/)的文章。在任何其他语言里，捕获一个错误都是非常合理的事情。

```ts
try {
  // 举个例子，比如你正在使用 Axios
} catch(e: AxiosError) {
//         ^^^^^^^^^^ Error 1196  
}
```

但是，这在 JavaScript 是无法做到的。原因是 JavaScript 的错误机制（详细的原因在上面那个错误处理的文章里）。但是这样的代码在 TypeScript 里，就可以实现。

另一个例子，使用 Object.keys 然后获取它的属性，直接在 TypeScript 里这么做也会造成问题：

```text
type Person = {
  name: string, age: number, id: number,
}
declare const me: Person;

Object.keys(me).forEach(key => {
  //   下一行在 TypeScript 要报错
  console.log(me[key])
})
```

一个绕过的方法可以看这篇[文章](https://link.zhihu.com/?target=https%3A//fettblog.eu/typescript-better-object-keys/)，但是这个方法并不适用所有的场景。TypeScript 编译器无法根据你的代码确定你希望的类型和你期望的行为是一致的。这样的代码在 JavaScript 里运行是完全没有问题的，但是，因为很多原因，在类型系统里是很难直接实现的。

如果你学习 TypeScript，而没有 JavaScript 的背景，应该去学习 JavaScript 和 [类型系统](https://www.zhihu.com/search?q=类型系统&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})的区别。也要去学习如何能搜索的正确的写法。TypeScript 的语法，比如把[函数](https://www.zhihu.com/search?q=函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})的参数起好名字。用 objects 当作参数。其实就是一个比较好的 JavaScript 的模式。[可选链](https://www.zhihu.com/search?q=可选链&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})？实际是 TypeScript 编译器先实现的，但是后来也变成了 JavaScript 的特性。类和继承的语法？也是 JavaScript 的语法。私有类成员属性？你知道，你可以通过 # 来实现一个没有外部可以访问的[内部属性](https://www.zhihu.com/search?q=内部属性&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})，这也是 JavaScript 的语法。

用 TypeScript 写代码，其实绝大部分事情，仍然是发生在 JavaScript 的世界里。如果你使用类型去表达你的意图和协议，你又到了类型系统的领地。

目前，TypeScript 官网有了 TypeScript 更清晰的说法：*TypeScript 是有类型语法的 JavaScript*。是的，TypeScript 就是 JavaScript。理解 JavaScript 是理解 TypeScript 的核心钥匙。

## 错误2:到处标注类型

类型标注是一个给出类型是什么的方式。你知道，在其他语言里，这个事情是非常重要的事儿，例如 StringBuilder sringBuilder = new StringBuilder() 这个语法，确保，你确实在操作 StringBuilder。右边的就是一个类型引用，TypeScript 会尝试帮你找到类型。 let a_number = 2, TypeScript 也会帮你找到类型 number。

类型标注是 TypeScript 和 JavaScript 非常明显的语法不一样的地方。

当你开始学习 TypeScript，你可能会去到处标注你写的东西的类型。这个好像是选择 TypeScript 自然而然的选择。但是，我非常建议你，不要这么做。你要学会让 TypeScript 的编译器帮助你去找你想要的类型。为什么呢？我来给你解释，[类型标注](https://www.zhihu.com/search?q=类型标注&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})实际在做什么。

**类型标注是你表达，什么地方的协议，需要被检查。**如果你在一个式子的左边标注类型，本质是你告诉编译器，在代码评估阶段，去检查，类型匹配是否符合你的预期。

```text
type Person = {
  name: string,
  age: number
}

const me: Person = createPerson()
```

如果 createPerson 函数返回的东西和 Person 不匹配，TypeScript 就会报错。这么做的目的是，你希望确保这里建立连接的地方是正确的类型。

同时，me 这个变量，就是 Person 类型的变量，TypeScript 就会把 me 当作 Person 类型来看待。如果 me 里有更多的属性，比如 profession 字段，TypeScript 不会允许你访问他，因为这个属性，你没有定义在 Person 类型中。

如果你在一个函数的返回类型里标注类型，这是你告诉编译器要在返回的值和你预期[返回值](https://www.zhihu.com/search?q=返回值&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})的类型做检查：

```text
function createPerson(): Person {
  return { name: "Stefan", age: 39 }
}
```

如果你返回不匹配 Person 类型的东西，TypeScript 就会报错。这么做，确保了你返回的一定是你期望的类型。这个在你做一个函数，返回一个复杂对象的时候及其有帮助。

如果你在函数的参数进行类型标注，这个是你告诉编译器，在使用这个函数的时候，这个参数必须符合你的类型约束。

```text
function printPerson(person: Person) {
  console.log(person.name, person.age)
}

printPerson(me)
```

这个类型标注，是最重要，且不可避免的标注。但是其他的类型，是可以推导的。

```text
type Person = {
  name: string,
  age: number
}

// 推导
// 返回类型是 { name: string, age: number }
function createPerson() { 
  return { name: "Stefan", age: 39}
}

// 推导!
// me 的类型是 { name: string, age: number}
const me = createPerson() 

// 标注! 你必须去检查，类型是否兼容
function printPerson(person: Person) {
  console.log(person.name, person.age)
}

// 一切顺利
printPerson(me) 
```

函数的参数要使用类型标注。这里是检查你协议的地方。这不仅很方便，你还会得到很多好处。比如，你顺带获得了[多态](https://www.zhihu.com/search?q=多态&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})。

```text
type Person = {
  name: string,
  age: number
}

type Studying = {
  semester: number
}

type Student = {
  id: string,
  age: number,
  semester: number
}

function createPerson() { 
  return { name: "Stefan", age: 39, semester: 25, id: "XPA"}
}

function printPerson(person: Person) {
  console.log(person.name, person.age)
}

function studyForAnotherSemester(student: Studying) {
  student.semester++
}

function isLongTimeStudent(student: Student) {
  return student.age - student.semester / 2 > 30 && student.semester > 20
}

const me = createPerson() 

// 一切顺利
printPerson(me)
studyForAnotherSemester(me)
isLongTimeStudent(me)
```

**译者注：在不同的 tsconfig 编译器设置中，上述的行为不一定是这样的。这里可能会牵扯逆变和协变的问题，但是不是本文关注的重点。**

Student，Person 和 Studying 有一些重叠，但是互相没关系。createPerson 可以返回所有符合这些类型的对象。如果我们标注太多，我们需要创建跟多的类型和更多的检查，但是可能这些工作是没必要的。（**译者注：初学 TypeScript 这么想是没问题的，但是实际上，写的东西复杂了，这个作者推荐的这种方式可能是有问题的。**）

在学习 TypeScript 时，不太[依赖类型](https://www.zhihu.com/search?q=依赖类型&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})系统，可以从结构类型系统里获得一些好处。（**译者注：这里谈到的是[鸭子类型](https://www.zhihu.com/search?q=鸭子类型&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})**）

## 错误3:错误的类型

TypeScript 是 JavaScript 的超集，这意味着，TypeScript 实际上在一个已经定义好和存在的语言中增加了一些额外的东西。随着你学习的深入，你应该要知道，哪些是 JavaScript，哪些是 TypeScript。

把 TypeScript 当作正常 JavaScript 上的一个增加层是一种有帮助的看法（**译者注：这甚至是一种流派，因为你用纯粹 TypeScript 的新语法，要编译到 JavaScript，这个行为，在一些场景，可能给你带来相当的烦恼。**）。在这个层面，TypeScript 就像一层薄薄的[元数据信息层](https://www.zhihu.com/search?q=元数据信息层&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})，JavaScript 只要去掉这一层，就能很好的运行在实际的运行环境中。当用 tsc 进行编译时，一些人甚至说，TypeScript 代码“擦除”到了 JavaScript。

TypeScript 作为 JavaScript 的这一层，代表着，在不同层用不同的语法。例如，function 或者 const 关键字是 JavaScript 的部分，type 和 interface 关键字是 TypeScript 的部分。

```text
// TypeScript 的世界! --> type
type Collection<T> = {
  entries: T[]
}

// printCollection 是在 JavaScript 的世界! --> value
function printCollection(coll: Collection<unknown>) {
  console.log(...coll.entries)
}
```

我们也能看到，任何声明语句，要么是一个类型声明，要么是一个值声明。因为类型层要在值的上一层，也可以在类型层消费值层的内容，但是无法倒过来。我们也有语法做到这一点：

```text
// 一个值
const person = {
  name: Stefan
}

// 一个类型，这个类型消费了值世界的类型
type Person = typeof person;
```

typeof 关键字通过值层的东西给类型层创建了类型。

也有一些声明，即创建了值，也创建了类型，这很烦人（**译者注：库作者必须要明白这里的行为，[monorepo](https://www.zhihu.com/search?q=monorepo&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"}) 里，这个问题也是一个特别麻烦的问题**）。比如 Class 的实例，既可以在 TypeScript 层作为一个类型，也可以在 JavaScript 层作为一个值。

```text
// 声明
class Person {
  name: string

  constructor(n: string) {
    this.name = n
  }
}

// 值
const person = new Person("Stefan")

// 类型
type PersonCollection = Collection<Person>

function printPersons(coll: PersonCollection) {
  //...
}
```

但是名字的惯性会给你造成一些迷惑。一般来说，我们定义 class、type、interface、enum 都会大写开头。即便他们提供值，他们也会提供类型。直到你也会给你的 React app 写大写开头的函数名。

如果你习惯用名字来表示类型和值，你有可能碰到一个老 tsc 报错 *TS2749: ‘YourType’ refers to a value, but is being used as a type*error.

```text
type PersonProps = {
  name: string
}

function Person({ name }: PersonProps) {
  return <p>{name}</p>
}

type Collection<T> = {
  entries: T
}

type PrintComponentProps = {
  collection: Collection<Person> // ERROR! 
  // 'Person' refers to a value, but is being used as a type
}
```

这是 TypeScript 会造成一些真的很困扰人的地方。什么是类型，什么是值，为什么我们要区分他们，为什么这个行为和其他[编程语言](https://www.zhihu.com/search?q=编程语言&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})不一样？随着深入，你开始面对，typeof  甚至系统内置的 InstanceType 帮助类型来处理一些东西，因为你发现类实际有[两个类型](https://link.zhihu.com/?target=https%3A//fettblog.eu/typescript-interface-constructor-pattern/)（震惊）

所以，知道什么贡献了类型，什么贡献了值是有价值的。边界是什么，出了问题该如何处理，这些对于你的程序意味着什么？下面这个表，和 TypeScript 文档一样，指明了这些事。

| Declaration type | Type | Value |
| ---------------- | ---- | ----- |
| Class            | X    | X     |
| Enum             | X    | X     |
| Interface        | X    |       |
| Type Alias       | X    |       |
| Function         |      | X     |
| Variable         |      | X     |

当学习 TypeScript 时，一个比较好的开始是，先关注在函数和变量，以及简单的类型别名（或者接口，如果你需要）。这会让你开始了解类型层会发生什么，值层会发生什么。

## 错误4:一开始什么都学

我们说了很多，如果你从另一个语言到 TypeScript 可能碰到的问题。说句实话，这些都是我的工作。（言外之意，作者一直在致力于帮助其他背景的人转到 TypeScript 上）但是，还有另一个轨道：如果你已经写了一大堆 JavaScript，突然要面对 TypeScript 世界的这些烦人的工具怎么办？

这会导致非常沮丧的经验。你很熟悉你的代码，你的代码仓库中的一切，你都了如指掌。但是你发现，TypeScript 不明白，它报了那么多错。你的代码跑了很多年，你知道他们是没问题的。

你甚至都疑惑，为啥这么多人会喜欢 tsc 这种看起来就是一堆 bug 的东西。TypeScript 应该提升你的效率，但是实际上，它只给你带来一堆红色的报错。

但是，我们已经来到这里了，不是么？

我能理解这一点。如果你直接把一个 JavaScript 项目切换到 TypeScript，那肯定是一堆报错。TypeScript 希望能了解你整个程序的细节，这需要大量的类型标注工作。

如果你从 JavaScript 来，我建议你利用 TypeScript 的渐进修改的特性。TypeScript 实际上提供了一些方便的特性，让你慢慢把你的项目变成一个 TypeScript 的项目。

1. 一步步把你的程序改为 TypeScript，而不是一下都改了。TypeScript 有一个编译器选项  allowJS。
2. TypeScript 有一个选项，可以静默编译器的报错，这个选项是 noEmitOnError，这个选项可以让你即便程序有 TypeScript 的报错，也可以运行。
3. 使用 TypeScript 编写[类型声明文件](https://www.zhihu.com/search?q=类型声明文件&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})，然后通过 [JSDoc](https://link.zhihu.com/?target=https%3A//fettblog.eu/typescript-jsdoc-superpowers/) 来引入它们。这对于 TypeScript 了解更多你的程序是有帮助的。
4. 在任何地方使用 any，是有问题的。但是和主流观点不同，我认为[适当严格的使用 any](https://link.zhihu.com/?target=https%3A//fettblog.eu/typescript-any-is-ok/)是 OK 的。

学习 tsconfig 的[文档](https://link.zhihu.com/?target=https%3A//www.typescriptlang.org/tsconfig)去学习哪些参数是 OK 的。TypeScript 被设计为可以渐进更改。你可以使用尽可能多的类型，你可以仍然使用大量的 JavaScript 在你的程序里，这个可以让你快速开始你的 TypeScript 之旅。

作为一个 JavaScript 的开发者，开始学习 TypeScript 时，不要怀疑自己。尝试把类型当成更好的内连文档，让你的代码有更好的含义，然后开始从中扩展和收益。

## 错误5:学习错误的 TypeScript

重申，受到[错误学习 Rust 文章](https://link.zhihu.com/?target=https%3A//dystroy.org/blog/how-not-to-learn-rust/)的影响。如果你的代码需要用下列的关键字，那你可能正在学习不合时宜的 TypeScript 的部分，这会给你带来很多问题（这些内容，都是 TypeScript 在演进中的折衷和兼容方案，现在都不应该在 2022 年使用）

- namespace
- declare
- module
- <reference>
- abstract （**译者注：为什么不让用 abstract 呢？**）
- [unique](https://www.zhihu.com/search?q=unique&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2307521220"})

不用这些关键字，不是说这些关键字不重要，在一些场景，是有用的。但是，在开始的时候，你可以先不要学这些内容。

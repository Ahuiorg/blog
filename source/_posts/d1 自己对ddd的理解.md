---
title: d1 自己对领域设置的理解
abbrlink: 1720814833
date: 2022-07-11 14:16:56
---

### 背景

1. 前端项目日益复杂，功能多
2. 业务逻辑，数据流，渲染，交互等等功能都是放在一块， 没有经过成熟的设计，杂乱无章，功能多了之后， 改一个小东西要把逻辑从头到尾都动一遍



### 理解

1. 是一个抽象概念一种思想，只在项目设计阶段存在，就是去划分项目每个模块的职责



### 核心点

1. 建模，建模是整个ddd最重要的部分

   > 需要去区分一个业务中的各个概念，为不同的概念建立不同类型的模型，并且找到它们之间的关系，通过建模，建立起我们编程的基础工程，后续所有的开发，都是在这些模型的基础上完成的



### 前端建模

#### 	核心思想：分层思想（Layered Architecture）

​	比如一些常见的想法：

1. 定义业务对象
2. 控制数据流
3. 界面渲染
4. 用户交互��染
4. 用户交互







### 相关概念

领域驱动设计（domain-driven-design）是软件代码的结构及语言需要符合**业务领域**中的习惯用法

领域驱动设计可以将实现对应到持续进化的模型

领域驱动设计的前提是：

- 把项目的主要重点放在核心领域（core domain）和领域逻辑
- 以领域中的模型为基础，进行复杂的设计
- 让技术人员以及领域专家合作，以迭代方式来完善特定领域问题的概念模型

##### **上下文（Context）**

情境，脉络，上下文。比如：电子商务系统。

##### **领域（Domain）**

知识、影响、活动。客户使用软件要处理的问题种类即为软件的领域。

##### **模型（Model）**

一类描述域的不同方面并可用于解决相关问题的系统化的抽象

##### **通用语言（Ubiquitous Language）**

一种领域专家使用，为了描述[域模型](https://zh.wikipedia.org/wiki/領域模型)而构造的语言，以减少沟通成本。

> 理想情况下，只有一个统一的模型。 但是通常情况下都无法实现，因此在实践中通常分成多个模型。

##### **限界上下文**

任何大型项目都有多个模型。 然而，当基于不同模型的代码相结合，软件变得越来越多，不可靠，并且难以理解。 团队成员之间的交流变得越来越难。 模型的使用情境变得越来越不清晰。

因此：需要明确定义模型适用的上下文，并且根据团队组织，应用程序特定部分的使用情况以及代码库和数据库模式等物理表现明确设置边界。 保持模型在这些范围内严格一致，并且不被外部的问题影响。

##### **持续集成**

当愈多人在相同的有限背景下工作时，模型就愈应该分裂。 团队越大，问题就越大，即使只有三四个人也会遇到严重的问题。 然而，将系统分解为更小的环境最终会失去一个有价值的集成和一致性。

因此：创建一个经常合并所有代码和其他实现工件的过程，用自动化测试快速标记碎片。通过持续地运用统一术语去夯实随着概念在不同人的头脑中的演变而逐渐形成对模型的共同观点。

##### **上下文关系**

在缺乏全局认识的情况下，个别有界上下文会留下一些问题。 其他模型的背景可能仍然是模糊不清的。 其他团队的人不会意识到上下文的界限，并且会不知不觉地做出模糊边缘或使连接复杂化的变化。 当连接必须在不同的上下文之间进行时，它们往往会相互渗透。

因此：确定项目中正在使用的每个模型并定义其限界上下文。 这包括非面向对象子系统的隐式模型。 命名每个限界上下文，并将其命名为通用语言的一部分。 描述模型之间的关联点，确保任何用于共享交流的词语都有清晰明确的含义。 映射现有的情形。



### DDD领域驱动设计

DDD（Domain-Driven Design）是帮助工程师，应对复杂业务系统设计和开发的思想武器和方法论。它指导了我们如何去和领域专家（熟悉业务的负责人）进行沟通，如何和他们找到一门共通语言，并基于该语言构建一套关于领域知识的图谱，并且是按照我们做系统设计的思路构建这套图谱。

建模是DDD的核心方法论，你需要去区分一个业务中的各个概念，为不同的概念建立不同类型的模型，并且找到它们之间的关系，通过建模，建立起我们编程的基础工程，后续所有的开发，都是在这些模型的基础上完成的。

> DDD是什么呢，DDD就是一个抽象的概念，DDD只在软件的架构设计阶段出现，它就是软件模块职责的划分



### 前端建模

如果你使用vue组件，你会有一种为视图撰写模型的感觉，即ViewModel，它一定指向视图层（界面与用户交互）。但如我在多个场合提到的一样，vue组件是纯视图层的要件，如果你把有关业务的代码，写在vue组件中，你的代码将会是业务逻辑和视图逻辑混杂在一起的代码，你将无法在后来的维护中区分和把握到底要改业务逻辑还是改交互逻辑。实际上，这种操作是很多初级前端的惯用手法，因为大部分初级前端的编程习惯，都是随着意识流，按线性的思维写代码。而真正有经验的工程师，***一定会在开始写代码之前先思考将要写作的代码，哪些是用于定义业务对象的，哪些是用于控制数据流的，哪些是为了完成界面渲染的，哪些是为了完成用户交互的等等。而这些思考，用一种思想来概括就是“分层思想”或者叫“Layered Architecture”。***有了分层思想之后，开发者才不会认为抽象出业务模型是一件麻烦的事。**分层开发，势在必行。**

**前端业务模型分为两类：一类是用于展示的模型，一类是用于数据提交（表单）的模型**。后者在复杂度上会比前者高出一个等级。

>你可能会有疑问，不都是业务模型么，怎么还区分用于展示的和提交的？这可能是前端的特殊之处。后端应用，提交数据到数据库时，具有特定的约束，但是在输出到前端时却没有约束，因此，后端把大部分工作都投入在对数据库有写入动作的业务逻辑上，而丢给前端的数据，基本上不需要按照视图层的逻辑建模，只要一股脑把数据丢给前端即可。但是前端则不同，视图层具有复杂的交互逻辑，而这些交互逻辑依赖业务对象的特征，比如当这个业务对象处于什么样的一个状态时，才能点击某个按钮触发一个流转业务。因此，在展示/交互这个层面，前端也需要建模。而提交数据就更不用说了，前端业务表单本身就是极为复杂的一种场景，不建立模型，根本无法对一个表单所要表达的业务对象完成清晰的创建或更新处理。



### 如何建模？

讲了那么多，那么到底应该如何实施前端建模呢？作为工程师，我们必须掌握一定的方法论，在理论上对我们的设计有一定的自信，才能确保我们的建模方式是对的。DDD为我们提供了建模的方法论，它提供了多个方案（Scheme），比如Entity, Value Object, Service, Modules, Aggregate, Factory, Repository等等，这些都是用来构造模型的方案。

这么多方案，实际上本质要解决两个问题：

- 核心
- 边界

我们要对业务进行建模，首先要抓住该业务的核心是什么。例如银行转账这个业务，它的核心是什么？是转账的金额，还是账号？它的边界又是什么？例如在转账这个业务中，我是否需要去把两边账号的消费记录拉出来看看？这些，都是我们要在建模的时候解决的问题。DDD告诉我们的方法论，是不要自己闭门造车，不要从开发人员的角度去设计一个系统，而是要找领域专家（对该业务的实操了如指掌的人）进行了解，建立自己对该业务的知识体系，并且和领域专家一起敲定有关这个业务各个细节的模型体系。

回到我们前端。我们实际上要找到这个业务中，存在那些对象，收集到所有对象之后，去一个一个的观察它们，如果它是业务逻辑中的关键对象，就要使用Entity的方案，对它进行细致的深入的描述，如果它是一个次要的或者说固定不变的或者一次性用完的对象，那么就可以使用Value Object的方案（也就是一个普通的对象）。有了这些对象之后，还需要有一个东西把它们聚合在一起，这时我们可以使用Aggreate的方案。在这些对象之间，还可能出现一些动作（动词，非静态的），此时我们可以使用Service和Factory的方案。我们还需要和服务端交互，拉取数据，填充到模型中，形成更丰富的细节，此时我们可以采用Repository的方案。

不要被这么多的名词吓到，本质上，放到前端的语境下，你就是需要去创建一些类，并处理好这些类在真实被使用时，它们之间的约束逻辑等等。



### 分层架构

通过分层架构（Layered Architecture），我们的代码被以不同层的不同理念进行组织。有关模型的东西，全都且只在模型层处理，我们不需要考虑外部将会如何使用它，理论上可以表述为“内存实体不需要考虑外层环境”的Clean Architecture，因此，我们只需要考虑，我们的建模是否符合真实业务的需要。这样的代码组织，将颠覆我们传统前端开发的一些经验，然而，这种颠覆显得没有什么惊喜，它看上去复杂度增加了，我们反问一句自己，我们是为了分层而分层，还是为了这样分层写出的代码，将有助于我们区分代码块功能，以利于我们的项目在两年三年的持续迭代维护中，有比较清晰的代码组织，从而让我们的维护更加有效？



### 提炼关系表

关系表是指在某个业务逻辑中，涉及多个业务对象，它们之间的存在不同情况下的不同联系，所最终组成的一个Object对象。






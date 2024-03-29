---
title: Typescript中Interface与Type的异同点
date: 2023-04-11 22:21:39
tags:
  - TypeScript
---

## interface 与 type 异同点

这可能是最经典的一道 TS 面试题了，因此这里我们放在第一个知识点来讲解。

### 及格线

不论如何，以下这些概念是你需要基本了解的，否则很容易被怀疑是否真的深入使用过 TypeScript 。

- 在对象扩展情况下，interface 使用 extends 关键字，而 type 使用交叉类型（`&`）。
- 同名的 interface 会自动合并，并且在合并时会要求兼容原接口的结构。
- interface 与 type 都可以描述对象类型、函数类型、Class 类型，但 interface 无法像 type 那样表达元组、一组联合类型等等。
- interface 无法使用映射类型等类型工具，也就意味着在类型编程场景中我们还是应该使用 type 。

### 优秀回答

只是回答这些概念定义显得过于枯燥，而且很容易被认为像是在背书，因此你可以穿插自己在工程中的实践， 比如小册中提过的，使用 interface 来定义对象类型，使用类型别名来处理函数签名、联合类型、工具类型等等。这同样也代表了你对这两个工具的理解：**interface 就是描述对象对外暴露的接口，其不应该具有过于复杂的类型逻辑，最多局限于泛型约束与索引类型这个层面。而 type alias 就是用于将一组类型的重命名，或是对类型进行复杂编程。**

另外，你也可以提到在官方的 Wiki 中，特别说明了在对象扩展的情况下，使用接口继承要比交叉类型的性能更好。

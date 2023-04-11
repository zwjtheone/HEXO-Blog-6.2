---
title: Typescript中infer的理解
date: 2023-04-11 22:20:02
tags:
  - TypeScript
---

条件类型在泛型的基础上支持了基于类型信息的动态条件判断，但无法直接消费填充类型信息，而 infer 关键字则为它补上了这一部分的能力，让我们可以进行更多奇妙的类型操作。

```TypeScript
type PromiseValue<T> = T extends Promise<infer V> ? PromiseValue<V> : T;
```

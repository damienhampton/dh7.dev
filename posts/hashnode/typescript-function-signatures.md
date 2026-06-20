---
title: "TypeScript function signatures"
slug: typescript-function-signatures
publishedAt: 2021-10-25
brief: "Here are four different ways to define a typed function in TypeScript. //A const strLen: (str: string) => number = str => {   return str.length; }  //B type StrLenType = (str: string) => number  const"
tags: ["typescript", "javascript", "beginners"]
---

Here are four different ways to define a typed function in TypeScript.

```
//A
const strLen: (str: string) => number = str => {
  return str.length;
}

//B
type StrLenType = (str: string) => number

const strLen: StrLenType = str => {
  return str.length;
}

//C
const strLen = (str: string): number => {
  return str.length;
}

//D
function strLen(str: string): number {
  return str.length;
}

```

All of these are syntactically valid, but which would you choose?

Personally, I dislike options `A` and `B`. I find `A` very hard to read and whilst I find `B` easier to read than `A`, it feels unnecessary.

I like `C` and `D` because the description of the parameters and the return type is where I _expect_ them to be. I guess it is familiarity with other languages.

I have tended to shy away from classes in JavaScript, in part out of a desire to be more functional. Having spent some time using Scala, I think I've come around to blending functional ideals with classes and I think in TypeScript it works well. This tends to mean I moved to preferring `D` as a general convention.

I will sometimes still use `C`, when writing smaller utility functions. I've deliberated ignored the implicitly return option for the example above, but if I was writing a function in the style of option `C`, I would write:

```
const strLen = (str: string): number => str.length;
```
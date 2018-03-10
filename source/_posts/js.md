---
title: JavaScript
date: 2018-03-04 12:56:06
tags: [JavaScript]
categories: [Learn]
---
### Array

#### push and concat

`push` 是在array后面添加一个元素，相当于java里面的`add`, 但这里返回的是 新的 length
`concat` 是直接把一整个array 添加到另一个array后面，而且这里返回的是新array的instance

### String

#### charCodeAt and fromCharCode

`charCodeAt` 是把一个char转换成 ascii码 比如 'abc'.charCodeAt(0) // 97
`fromCharCode`  一把一个ascii码转回字母，比如，String.fromCharCode(97) //'a' 
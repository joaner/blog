---
title: framework7 入门笔记
id: 200
categories:
  - 前端
date: 2015-11-21 10:32:21
tags:
---

framework7是一个前端UI框架，它能保持和原生应用一样的UI风格，操作体验。
> 官网 [http://www.idangero.us/framework7](http://www.idangero.us/framework7)
今天遇到了第一个问题:

初始化框架时报错:
```
result of expression this[0]."classList" is not an object  (framework7.min.js:22)
```
这是由于浏览器版本太低，比如 Safari7 不支持 `classList` 特性，才会有这个错误。
> 支持的浏览器版本：[http://caniuse.com/#feat=classlist](http://caniuse.com/#feat=classlist)
可以使用这个补丁解决： [https://github.com/eligrey/classList.js](https://github.com/eligrey/classList.js)

需要在解析framework7.min.js之前，先加载这个补丁。它会给不支持的浏览器DOM补上`classList`的功能

&nbsp;

由于我只有一个android手机，没有测试出这个兼容性问题，浪费了其他同事的测试时间。后来在 [https://crossbrowsertesting.com/](https://crossbrowsertesting.com/) 上用真机IPAD免费测试，才算定位到问题。

&nbsp;
---
title: 用setTimeout实现setInterval
date: 2018-03-06 21:50:53
tags:
---

看到一个面试题：用 `setTimeout()` 实现 `setInterval()` 的功能，尝试做了下。

## 简单版本

本身没有难度，用递归实现即可

```
function setInterval(fn, delay) {
  function interval() {
    setTimeout(function() {
      interval()
      fn()
    }, delay)
  }

  interval()
}
```

## 支持clearInterval

需要在全局保存`intervalID`，用来指向正在执行的`timeoutID`，调用`clearTimeout()`来终止执行。

```
function setInterval(fn, delay) {
  if (!window.intervalIDs) {
    window.intervalIDs = new Array(1)
  }

  const intervalID = window.intervalIDs.length
  function interval() {
    window.intervalIDs[intervalID] = setTimeout(function() {
      interval()
      fn()
    }, delay)
  }

  interval()

  return intervalID
}

function clearInterval(intervalID) {
  clearTimeout(window.intervalIDs[intervalID])
}
```

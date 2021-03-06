---
title: css垂直水平居中复习
id: 338
categories:
  - 前端
date: 2018-02-16 21:45:42
tags:
---

对于父容器高宽未知的居中方案，我了解的有四种

在codepen上重新手写了一遍复习：[https://codepen.io/joaner/pen/ddZQQw](https://codepen.io/joaner/pen/ddZQQw)

## 1. table-cell + vertical-align

```
<div>
 <div style="display: table; width: 100%; height: 100%;">
 <span style="display: table-cell; vertical-align: middle; text-align: center;">table-cell</span>
 </div>
</div>
```

## 2. flex + align-items + justify-content
```
<div>
 <div style="display: flex; align-items: center; justify-content: center; height: 100%;">
  <span>flexbox</span>
 </div>
</div>
```

## 3. absolute + margin: auto

```
<div>
 <div style="position: relative; height: 100%">
  <span style="position: absolute; top: 0; left: 0; right: 0; bottom: 0; margin: auto; width: 70px; height: 20px;">position</span>
 </div>
</div>
```

## 4. transform: translate(-50%, -50%)

```
<div>
 <div style="position: relative; height: 100%;">
  <span style="position: absolute; left: 50%; top: 50%; transform: translate(-50%, -50%)">transform</span>
 </div>
</div>
```

----

以上，我习惯用 `table-cell` 和 `position: absolute + margin: auto` 方案，因为以前在IE下兼容性好些。

但现在主流浏览器都已支持`flexbox`，所以更加推荐这个方案，适用性更强。

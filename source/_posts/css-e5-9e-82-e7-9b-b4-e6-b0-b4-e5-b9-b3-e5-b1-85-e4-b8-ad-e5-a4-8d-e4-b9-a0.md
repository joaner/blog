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
<pre>&lt;div class="examples"&gt;
 &lt;!-- table-cell +vertical-align: middle --&gt;
 &lt;div&gt;
 &lt;div style="display: table; width: 100%; height: 100%;"&gt;
 &lt;span style="display: table-cell; vertical-align: middle; text-align: center;"&gt;table-cell&lt;/span&gt;
 &lt;/div&gt;
 &lt;/div&gt;

 &lt;!-- flex + align-items +justify-content --&gt;
 &lt;div&gt;
 &lt;div style="display: flex; align-items: center; justify-content: center; height: 100%;"&gt;
 &lt;span&gt;flexbox&lt;/span&gt;
 &lt;/div&gt;
 &lt;/div&gt;

 &lt;!-- absolute + margin: auto --&gt;
 &lt;div&gt;
 &lt;div style="position: relative; height: 100%"&gt;
 &lt;span style="position: absolute; top: 0; left: 0; right: 0; bottom: 0; margin: auto; width: 70px; height: 20px;"&gt;position&lt;/span&gt;
 &lt;/div&gt;
 &lt;/div&gt;

 &lt;!-- transform: translate(-50%, -50%) --&gt;
 &lt;div&gt;
 &lt;div style="position: relative; height: 100%;"&gt;
 &lt;span style="position: absolute; left: 50%; top: 50%; transform: translate(-50%, -50%)"&gt;transform&lt;/span&gt;
 &lt;/div&gt;
 &lt;/div&gt;
&lt;/div&gt;</pre>
以上，我习惯用的 `table-cell` 和 `position: absolute + margin: auto` 方案，因为以前在IE下兼容性好些。

但现在大多数浏览器都支持`flexbox`，更加推荐这个方案，适用性更强。
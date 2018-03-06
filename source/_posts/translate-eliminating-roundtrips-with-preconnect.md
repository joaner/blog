---
title: "[译]使用preconnect减小延迟"
categories:
  - html5
date: 2018-03-06 02:28:26
---

> 本文翻译自 [Eliminating Roundtrips with Preconnect](https://www.igvita.com/2015/08/17/eliminating-roundtrips-with-preconnect/) ，作者 Ilya Grigorik 著有[《High Performance Browser Networking - O'Reilly》](https://hpbn.co/?utm_source=igvita&utm_medium=referral&utm_campaign=igvita-footer)

在实际的请求内容发送到服务器之前，建立HTTP连接的“简单”动作可能会产生很多往返数据包：浏览器需要先解析DNS名称，执行TCP握手，如果是HTTPS还需要建立TLS加密隧道。这些动作都将会阻塞获取数据，在网速不佳或者移动网络上问题会更突出。

![xsocket-setup](https://www.igvita.com/posts/15/xsocket-setup.png.pagespeed.ic.SvciYHBRv0.webp)

现代浏览器会[尽力预测](https://www.igvita.com/posa/high-performance-networking-in-google-chrome/#tcp-pre-connect)要预先建立的连接。通过提前启动预连接，浏览器可以在实际请求创建前就完成DNS/TCP/TLS往返。但浏览器还做不到很聪明，无法准确预测每个网站的预连接目标。

**好消息是现在可以了：由开发者告知浏览器需要预连接的目标，Firefox 39和Chrome 46都已支持这项特性！详细兼容性：[caniuse.com](https://caniuse.com/#feat=link-rel-preconnect)**

## 预先连接动态请求的URL

由于是否加载的逻辑，UserAgent适配或其它原因，应用程序可能无法确定要加载的资源URL。但是，如果资源的归属的服务器是确定好的，则预连接正好适用，比如谷歌字体服务，总是需要连接 *fonts.gstatic.com*，以下是未使用/使用预连接的示例：

![xfont-preconnect](https://www.igvita.com/posts/15/xfont-preconnect.png.pagespeed.ic.pi46037zWI.webp)

上面第一个加载过程中，浏览器执行HTML后先请求CSS资源 *fonts.googleapis.com* ，然后确定要使用的字体，再连接 *fonts.gstatic.com* - 连接过程是依次进行的，比较慢。

```
<link href='https://fonts.gstatic.com' rel='preconnect' crossorigin>
<link href='https://fonts.googleapis.com/css?family=Roboto+Slab:700|Open+Sans' rel='stylesheet'>
```

而在第二个加载过程中，我们在`<link>`中加入了`preconnect`预连接提示，指定应用将从`fonts.gstatic.com`中获取资源。因此，浏览器将与CSS请求并行，提前建立连接，从而可以立即加载字体！在这种特定情况下，预连接可以减少加载字体的耗时，尽早呈现内容。

> 按照 [字体加载规范](https://www.w3.org/TR/css-fonts-3/#font-fetching-requirements)，我们必须加上`crossorigin`的属性，


## 通过HTTP响应头启动预连接

除了通过HTML的`<link>`声明外，我们还可以通过响应header的`Link`报头来指定，不用修改HTML。**`Link`报头机制允许多个响应，表示应该提前连接的额外来源**。例如服务器端渲染的组件可以通过添加header来优化依赖资源的加载性能。

![xpreconnect-header](https://www.igvita.com/posts/15/xpreconnect-header.png.pagespeed.ic.C9-EQcMnlu.webp)

## 用javascript预先连接

我们不需要预先声明所有preconnect源。借助JavaScript，应用程序可以响应用户输入，预期活动或其他用户信号调用预连接。例如，考虑应用程序预期可能的导航目标并发出提前预连接的情况：

```
function preconnectTo(url) {
    var hint = document.createElement("link");
    hint.rel = "preconnect";
    hint.href = url;
    document.head.appendChild(hint);
}
```

![xreactive-preconnect](https://www.igvita.com/posts/15/xreactive-preconnect.png.pagespeed.ic.6-2JQXfomz.webp)

如图，用户进入jsbin.com; 在第3秒时，页面javascript判断用户可能要点击导航（mouseover等手段），就此启动预连接; 在第5秒时，用户真的点击了，此时连接已建立，请求不受阻塞地发送了 - 几乎为跳转节省了一秒钟！

## 适当的使用预连接

Preconnect是优化工具箱中的重要工具。如上面的例子所示，它可以避免获取资源时的阻塞 - 在某些情况下，请求延迟可以减少数百甚至数千毫秒。同时要明智的使用它：每个打开的TCP连接都会在客户端和服务器上产生成本，避免打开不会使用到的连接。

## 如何调试

在chrome的network面板上，不会显示preconnect优化的请求，因为它和主线程没关系。可以在 [webpagetest.org](https://www.webpagetest.org/) 上在线调试，查看完整的请求timeline。

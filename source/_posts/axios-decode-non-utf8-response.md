---
title: axios decode non-utf8 response
date: 2018-05-23 00:08:24
tags:
---

Node.js支持的编码不多，如果文本不是utf-8编码，就需要手动转码。

axios网络请求库对编码的文档很少，所以记录下解码GBK响应内容的用法：

```javascript
const axios = require('axios')
const iconv = require('iconv-lite')

// 示例页面，gb2312编码
const url = 'http://2018.ip138.com/ic.asp'
axios.get(url, {
    responseType: 'arraybuffer',
}).then(response => {
    const html = iconv.decode(response.data, 'gb2312')
    console.log(html)
})
```

由于 *icon-lite* 的 `decode(Buffer, sourceCharset)` 方法为了效率不直接接受`String`，所以关键配置是：**responseType: 'arraybuffer'** ，让axios直接返回`Buffer`而不是utf8字符串。

相关的处理源码：[lib/adapters/http.js#L199](https://github.com/axios/axios/blob/cb630218303095c0075182b542ccb2f72d20dd9d/lib/adapters/http.js#L199)


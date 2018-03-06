---
title: webpack4性能小测试
date: 2018-03-07 00:16:29
tags:
---

之前写了个用webpack打包静态页面的项目：[webpack-static-pages-template](https://github.com/joaner/webpack-static-pages-template)

最近webpack4已经正式发布了，对2/3/4版本做了性能测试，每个版本都有不错的速度提升。

## 测试配置

很棒的是webpack即便大版本升级，loaders也都能前后兼容。只需要增加 **mode** 等少量webpack的配置，即可使用最新版 *4.1.0*

完整的配置文件：[webpack.config.js](https://github.com/joaner/webpack-static-pages-template/blob/master/webpack.config.js)

```
module.exports = {
  // only for webpack 4
  mode: 'production',

  // also includes css/js
  entry: {
    home: './pages/index.html',
    about: './pages/about.html',
    contact: './pages/contact.pug',
  },

  module: {
    rules: [
      // ... loaders
    ],
  },
}
```

## 测试结果

### 2.6.1 : 2080ms

```
Version: webpack 2.6.1
Time: 2080ms
                   Asset       Size  Chunks                    Chunk Names
assets/global.89c970.css   69 bytes          [emitted]
   assets/time.208f6e.js  161 bytes          [emitted]
              about.html  116 bytes          [emitted]
              index.html  529 bytes          [emitted]
            contact.html  386 bytes          [emitted]
 imgs/webpack.f78661.png     368 kB          [emitted]  [big]
              contact.js    2.71 kB       0  [emitted]         contact
                 home.js    2.71 kB       1  [emitted]         home
                about.js    2.71 kB       2  [emitted]         about
```

### 3.11.0 : 1445ms

```
Version: webpack 3.11.0
Time: 1445ms
                   Asset       Size  Chunks                    Chunk Names
              index.html  529 bytes          [emitted]
   assets/time.208f6e.js  161 bytes          [emitted]
assets/global.89c970.css   69 bytes          [emitted]
              about.html  116 bytes          [emitted]
            contact.html  386 bytes          [emitted]
 imgs/webpack.f78661.png     368 kB          [emitted]  [big]
              contact.js    2.55 kB       0  [emitted]         contact
                 home.js    2.55 kB       1  [emitted]         home
                about.js    2.55 kB       2  [emitted]         about
```

### 4.1.0 : 1087ms

```
Version: webpack 4.1.0
Time: 1087ms
Built at: 2018-3-7 00:14:55
                   Asset       Size  Chunks                    Chunk Names
            contact.html  386 bytes          [emitted]
              about.html  116 bytes          [emitted]
   assets/time.208f6e.js  161 bytes          [emitted]
assets/global.89c970.css   69 bytes          [emitted]
              index.html  529 bytes          [emitted]
 imgs/webpack.f78661.png    360 KiB          [emitted]  [big]
              contact.js  575 bytes       0  [emitted]         contact
                about.js  574 bytes       1  [emitted]         about
                 home.js  575 bytes       2  [emitted]         home
```

## 总结

除了打包的速度提升30%，生成的入口js文件，也缩减了2KB。

这次打包的内容算比较少，相比vue/react等重量上有很大差异，所以结果不具有代表性。

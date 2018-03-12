---
title: Vue项目升级到webpack4
date: 2018-03-10 13:49:16
tags:
---

webpack正式发布有几周了，但是一些Vue依赖的webpack插件还没有正式支持webpack4，有的目前是beta版本。

如果你不担心项目稳定性，现在就可以在vue2项目中使用webpack4打包了，只需要升级依赖，移除一些废弃的配置即可。

## 升级步骤

我创建了一个 [vue2+webpack4的示例仓库](https://github.com/joaner/vue-webpack4-example)，以下步骤最终修改的代码都在这个 [commit](https://github.com/joaner/vue-webpack4-example/commit/645566606c4fc5c72d592462f92c283f107654b1) 里。

### 升级依赖

首先需要安装`npm-check-updates`，它可以不受升级规则`^`的限制，安装最新的大版本，比如`webpack`从`^3.11.0`到`^4.1.1`

```bash
npm install -g npm-check-updates
ncu -u
npm install extract-text-webpack-plugin@4.0.0-beta.0
npm install --force
```
以上命令即可完成升级，需要说明：
1. `ncu` 命令只会修改 *package.json*
2. `--force` 会强制重新安装，不使用缓存或本地已有的包
2. *extract-text-webpack-plugin* 还处于beta状态，所以ncu安装的还是上一个大版本`3.x.x`，需要手动指定安装`4.0.0-beta.0`

### 修改配置

vue项目生产和开发两个配置分开的：*build/webpack.dev.conf.js*, *build/webpack.prod.conf.js*
1. 增加 **mode** 配置，分别设置为 *development* 和 *production*
2. 移除CommonsChunkPlugin的使用

-----

## 详细的修改

***依赖包的升级很快，以下描述有的已经过时***

### package.json

大多数loader/plugin都已支持webpack4，只需要安装最新版：

```
-    "html-webpack-plugin": "^2.30.1",
+    "html-webpack-plugin": "git+https://github.com/webpack-contrib/html-webpack-plugin.git",
-    "babel-loader": "^6.2.10",
+    "babel-loader": "^7.1.4",
-    "eslint-loader": "^1.9.0",
+    "eslint-loader": "^2.0.0",
-    "extract-text-webpack-plugin": "^2.1.2",
+    "extract-text-webpack-plugin": "^4.0.0-beta.0",
-    "optimize-css-assets-webpack-plugin": "^1.3.0",
+    "optimize-css-assets-webpack-plugin": "^4.0.0",
-    "vue-loader": "^11.3.4",
+    "vue-loader": "^14.2.1",
-    "webpack": "^2.7.0",
+    "webpack": "^4.1.1",
```

以上配置，需要说明的几点：

1. 版本号中的 **^** 表示大版本不能被升级

  也就是说`npm update webpack`只会更新到最新的`2.x.x`，所以需要你手动修改版本号，或者`npm install webpack@latest --save-dev`

2. *html-webpack-plugin* 还没有发布

  webpack项目组fork了一份来支持webpack4，需要手动从git仓库安装，暂时这样：

  ```
  npm install https://github.com/webpack-contrib/html-webpack-plugin.git --save-dev
  ```

3. 有些插件的大版本号没变，但是也需要更新

  比如 *copy-webpack-plugin* 从 *4.4.0* 开始支持webpack4

  简单粗暴的升级方法：
  ```
  rm -rf node_modules
  npm cache clean
  npm install
  ```

### webpack.config.js

在vue2项目的 *build/webpack.prod.conf.js*，需要移除一些废弃的配置。

```
 {
+  mode: 'production',

   plugins: [
     // 废弃了UglifyJsPlugin，改为直接配置 `optimization.minimize`
-    new webpack.optimize.UglifyJsPlugin({
-      compress: {
-        warnings: false
-      },
-      sourceMap: true
-    }),

     // OptimizeCSSPlugin似乎还有兼容问题，所以不启用
-    new OptimizeCSSPlugin({
-      cssProcessorOptions: {
-        safe: true
-      }
-   }),

-    // 废弃了CommonsChunkPlugin ，改为配置 `optimization.splitChunks`
-    new webpack.optimize.CommonsChunkPlugin({
-      name: 'vendor',
-      minChunks: function (module, count) {
-        // any required modules inside node_modules are extracted to vendor
-        return (
-          module.resource &&
-          /\.js$/.test(module.resource) &&
-          module.resource.indexOf(
-            path.join(__dirname, '../node_modules')
-          ) === 0
-        )
-      }
-    }),
-    new webpack.optimize.CommonsChunkPlugin({
-      name: 'manifest',
-      chunks: ['vendor']
-    }),
   ]
+  
+  optimization: {
+   
+  }
 }
```

以上变化包含：
1. 增加 **mode** 配置，设为 *production* 自动设置压缩等配置
2. 移除了 *webpack.optimize.UglifyJsPlugin* *webpack.optimize.CommonsChunkPlugin* 等常用插件，改为在`optimization`中直接配置和使用
3. *OptimizeCSSPlugin* 至少在我的项目中还有问题，所以不启用

*build/webpack.dev.config.js* 没有要改的，就是增加一项 `mode: 'development'`

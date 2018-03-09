---
title: '[译]webpack 4: mode and optimization'
date: 2018-03-09 16:04:58
tags:
---

> 本文翻译自 [webpack 4: mode and optimization - medium](https://medium.com/webpack/webpack-4-mode-and-optimization-5423a6bc597a) ，详细解释了新添加的 **mode** 选项用法和意义。

webpack4添加了 **mode** 这个必须的选项（实际上不算必填，但是会有警告信息），在webpack内部快捷实现了常用的配置，但开发者仍然可以自定义。

**mode** 现在有两个选项值：`development` 开发环境和 `production` 生产环境。

## 选项值

### development

适合开发过程，默认包含以下的配置：
- 浏览器调试工具
- 修改代码后快速增量编译
- 完善的运行时错误信息

这些是最常用的开发配置，实际取决于你的项目情况。

### production

提供了适合部署生产环境的配置，重点包含：
- 最小的输出内容
- 运行速度更快
- 剔除只适用开发模式的代码（比如调试代码`console`）
- 不包含源代码或文件路径等开发信息
- 易于适用的静态资源

最后一点非常重要，*production* 提供了优化后的静态资源。还有其它更多的优化手段，但配置上更加复杂，所以我们没有引入，因为我们更重视入门的学习成本，而不是完美的优化。况且，大多数额外的优化只适合大型应用程序。

## 影响配置

使用新的 **mode** 选项，简单的选项值就能覆盖常见的案例，用最少的配置来实现可用的构建。

但根据我们的经验，也知道这不可能适合每个项目。每个团队都有不同的需求：历史遗留问题，个人偏好，项目的特殊性，或是不相信常用的最佳配置。很多人都想改变默认配置以适应自己的项目。因此，设置 **mode** 并不意味着我们删除了原有的配置，实际上大多数内部优化步骤都可以配置。

### 涉及到的配置列表

以下是 **mode** 选项影响到的配置，你也可以自定义它们。

比如在 **mode** 设为 *production* 时， `optimization.minimize`会自动开启，而设为 *development* 时不开启，因为会影响编译性能。

> 选项列表暂未翻译完成，详细介绍请[阅读英文原文](https://medium.com/webpack/webpack-4-mode-and-optimization-5423a6bc597a)

- devtool
- cache
- module.unsafeCache
- output.pathinfo
- performance
- optimization.removeAvailableModules
- optimization.removeEmptyChunks
- optimization.mergeDuplicateChunks
- optimization.flagIncludedChunks
- optimization.occurrenceOrder
- optimization.providedExports
- optimization.usedExports
- optimization.sideEffects
- optimization.concatenateModules
- optimization.splitChunks
- optimization.runtimeChunk
- optimization.noEmitOnErrors
- optimization.namedModules
- optimization.namedChunks
- optimization.nodeEnv
- optimization.minimize
- optimization.portableRecords

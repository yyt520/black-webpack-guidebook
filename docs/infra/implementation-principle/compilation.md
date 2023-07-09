---
nav:
  title: 架构原理
  order: 2
group:
  title: 底层原理
  order: 2
title: Compilation
order: 6
---

# Compilation

Compilation 模块会被 Compiler 用来创建新的编译（或新的构建）。Compilation 实例能够访问所有的模块和它们的依赖（大部分是循环依赖）。它会对应用程序的依赖图中所有模块进行字面上的 **编译（iteral compilation）**。

在编译阶段，模块会被 **加载（loaded）**、**封存（sealed）**、**优化（optimized）**、**分块（chunked）**、**哈希（hashed）** 和 **重新创建（restored）**。

Compilation 类扩展（extend）自 `Tapable`，并提供了以下生命周期钩子。可以按照 `compiler` 钩子的相同方式，调用 `tap`。

代码示例：

```js
compilation.hooks.someHook.tap(...)
```

## 插件开发

Compilation 对象代表了一次资源版本构建。当运行 Webpack 开发环境中间件时，每当检测到一个文件变化，就会创建新的 `compilation`，从而生成一组新的编译资源。一个 `Compilation` 对象表现了当前的模块资源、编译生成资源、变化的文件、以及被跟踪依赖的状态信息，简单来讲就是把本次打包编译的内容存到内存里。Compilation 对象也提供了插件需要自定义功能的回调，以供插件做自定义处理时选择使用拓展。

简单来说，Compilation 的职责就是构建模块和 Chunk，并利用插件优化构建过程。和 Compiler 用法相同，钩子类型不同，也可以在某些钩子上访问 `tapAsync` 和 `tapPromise`。

代码示例：

```jsx | inline
import React from 'react';
import img from '../../assets/principle-analysis/console-compilation.png';

export default () => <img alt="Compilation" src={img} width={720} />;
```

## 生命周期钩子

和 `compiler` 用法相同，取决于不同的钩子类型，也可以在某些钩子上访问 `tapAsync` 和 `tapPromise`。

| 事件名称                       | 内容说明                                                                                                                                                                                 | 参数                                               | 类型          |
| :----------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------- | :------------ |
| `buildModule`                  | 在模块构建开始之前触发                                                                                                                                                                   | `module`                                           | Sync          |
| `rebuildModule`                | 在重新构建模块之前触发                                                                                                                                                                   | `module`                                           | Sync          |
| `failModule`                   | 模块构建失败时执行                                                                                                                                                                       | `module` `error`                                   | Sync          |
| `succeedModule`                | 模块构建成功时执行                                                                                                                                                                       | `module`                                           | Sync          |
| `finishModules`                | 所有模块都完成构建                                                                                                                                                                       | `modules`                                          | Sync          |
| `finishRebuildingModule`       | 模块完成重新构建                                                                                                                                                                         | `module`                                           | Sync          |
| `seal`                         | compilation 停止接收新模块时触发                                                                                                                                                         | -                                                  | Sync          |
| `unseal`                       | compilation 开始接收新模块时触发                                                                                                                                                         | -                                                  | Sync          |
| `optimizeDependenciesBasic`    | -                                                                                                                                                                                        | `modules`                                          | SyncBail      |
| `optimizeDependencies`         | 依赖优化开始时触发                                                                                                                                                                       | `modules`                                          | SyncBail      |
| `optimizeDependenciesAdvanced` | -                                                                                                                                                                                        | `modules`                                          | SyncBail      |
| `afterOptimizeDependencies`    | -                                                                                                                                                                                        | `modules`                                          | Sync          |
| `optimize`                     | 优化阶段开始触发                                                                                                                                                                         | -                                                  | Sync          |
| `optimizeModulesBasic`         | -                                                                                                                                                                                        | `modules`                                          | SyncBail      |
| `optimizeModules`              | -                                                                                                                                                                                        | `modules`                                          | SyncBail      |
| `optimizeModulesAdvanced`      | -                                                                                                                                                                                        | `modules`                                          | SyncBail      |
| `afterOptimizeModules`         | -                                                                                                                                                                                        | `modules`                                          | Sync          |
| `optimizeChunksBasic`          | -                                                                                                                                                                                        | `chunks`                                           | SyncBail      |
| `optimizeChunks`               | -                                                                                                                                                                                        | `chunks`                                           | SyncBail      |
| `optimizeChunksAdavanced`      | -                                                                                                                                                                                        | `chunks`                                           | SyncBail      |
| `afterOptimizeChunks`          | chunk 优化完成之后触发                                                                                                                                                                   | `chunks` `modules`                                 | Sync          |
| `optimizeTree`                 | 异步优化依赖树                                                                                                                                                                           | `chunks` `modules`                                 | AsyncSeries   |
| `afterOptimizeTree`            | -                                                                                                                                                                                        | `chunks` `modules`                                 | Sync          |
| `optimizeChunkModulesBasic`    | -                                                                                                                                                                                        | `chunks` `modules`                                 | SyncBail      |
| `optimizeChunkModules`         | -                                                                                                                                                                                        | `chunks` `modules`                                 | Sync          |
| `optimizeChunkModulesAdvanced` | -                                                                                                                                                                                        | `chunks` `modules`                                 | SyncBail      |
| `afterOptimizeChunkModules`    | -                                                                                                                                                                                        | `chunks` `modules`                                 | Sync          |
| `shouldRecord`                 | -                                                                                                                                                                                        | -                                                  | SyncBail      |
| `reviveModules`                | 从 records 中恢复模块信息                                                                                                                                                                | `modules` `record`                                 | Sync          |
| `optimizeModuleOrder`          | 将模块从最重要的最不重要的进行排序                                                                                                                                                       | `modules`                                          | Sync          |
| `advancedOptimizeModuleOrder`  | -                                                                                                                                                                                        | `modules`                                          | Sync          |
| `beforeModuleIds`              | -                                                                                                                                                                                        | `modules`                                          | Sync          |
| `moduleIds`                    | -                                                                                                                                                                                        | `modules`                                          | Sync          |
| `optimizeModuleIds`            | -                                                                                                                                                                                        | `chunks`                                           | Sync          |
| `afterOptimizeModuleIds`       | -                                                                                                                                                                                        | `chunks`                                           | Sync          |
| `reviveChunks`                 | 从 records 中恢复 chunk 信息                                                                                                                                                             | `modules` 和 `records`                             | Sync          |
| `optimizeChunkOrder`           | 将 chunk 从最重要的到最不重要的进行排序                                                                                                                                                  | `chunks`                                           | Sync          |
| `beforeOptimizeChunkIds`       | chunk id 优化之前触发                                                                                                                                                                    | `chunks`                                           | Sync          |
| `optimizeChunkIds`             | 优化每个 chunk 的 id                                                                                                                                                                     | `chunks`                                           | Sync          |
| `afterOptimizeChunkIds`        | chunk id 优化完成之后触发                                                                                                                                                                | `chunks`                                           | Sync          |
| `recordModules`                | 将模块信息存储到 records                                                                                                                                                                 | `modules` 和 `records`                             | Sync          |
| `recordChunks`                 | 将 chunk 信息存储到 records                                                                                                                                                              | `chunks` 和 `records`                              | Sync          |
| `beforeHash`                   | 在编译被哈希之前                                                                                                                                                                         | -                                                  | Sync          |
| `afterHash`                    | 在编译被哈希之后                                                                                                                                                                         | -                                                  | Sync          |
| `recordHash`                   | -                                                                                                                                                                                        | `records`                                          | Sync          |
| `record`                       | 将 compliaztion 相关信息存储到 records 中                                                                                                                                                | `compilation` 和 `records`                         | Sync          |
| `beforeModuleAssets`           | -                                                                                                                                                                                        | -                                                  | Sync          |
| `shouldGenerateChunkAssets`    | -                                                                                                                                                                                        | -                                                  | SyncBail      |
| `beforeChunkAssets`            | 在创建 chunk 资源（assets）之前                                                                                                                                                          | -                                                  | Sync          |
| `additionalChunkAssets`        | 为 chunk 创建附加资源（asset）                                                                                                                                                           | `chunks`                                           | Sync          |
| `recordss`                     | -                                                                                                                                                                                        | `compilation` 和 `records`                         | Sync          |
| `additionalAssets`             | 为 compilation 创建附加资源（asset）这个钩子可以用来下载图像。                                                                                                                           | -                                                  | AsyncSeries   |
| `optimizeChunkAssets`          | 优化所有 chunk 资源（asset）。资源会被存储在 `compilation.assets`。每个 Chunk 都有一个 files 属性，指向这个 chunk 所创建的所有文件。附加资源被存储在 `compilation.additionalChunkAssets` | `chunks`                                           | AsyncSeries   |
| `afterOptimizeChunkAssets`     | chunk 资源（Asset）已经被优化                                                                                                                                                            | `chunks`                                           | Sync          |
| `optimizeAssets`               | 优化存储在 `compilation.assets` 中的所有资源（Asset）                                                                                                                                    | `assets`                                           | AsyncSeries   |
| `afterOptimizeAssets`          | 资源优化已经结束                                                                                                                                                                         | `assets`                                           | Sync          |
| `needAdditionalSeal`           | -                                                                                                                                                                                        | -                                                  | SyncBail      |
| `afterSeal`                    | -                                                                                                                                                                                        | -                                                  | AsyncSeries   |
| `chunkHash`                    | -                                                                                                                                                                                        | `chunk` 和 `chunkHash`                             | Sync          |
| `moduleAsset`                  | 一个模块中的一个资源被添加到编译中                                                                                                                                                       | `module` 和 `filename`                             | Sync          |
| `chunkAsset`                   | 一个 chunk 中的一个资源被添加到编译中                                                                                                                                                    | `chunk` 和 `filename`                              | Sync          |
| `assetPath`                    | -                                                                                                                                                                                        | `filename` 和 `data`                               | SyncWaterfall |
| `needAdditionalPass`           | -                                                                                                                                                                                        | -                                                  | SyncBail      |
| `childCompiler`                | -                                                                                                                                                                                        | `childCompiler`、`compilerName` 和 `compilerIndex` | SyncHook      |
| `normalModuleLoader`           | 普通模块 loader，真正（一个接一个地）加载模块图（Graph）中所有模块的函数                                                                                                                 | `loaderContext` 和 `module`                        | Sync          |

## 参考资料

- [📖 Compilation](https://webpack.docschina.org/api/compilation/)

---
nav:
  title: 基本综述
  order: 1
group:
  title: 基本概念
  order: 1
title: 模块化
order: 1
---

# 模块化

模块化就是 `把复杂的系统分解到多个模块以方便编码`。

过去代码组织方式，会出现的问题：

- 命名空间冲突
- 无法合理地管理项目依赖和版本
- 无法方便控制依赖的加载顺序
- 项目体积变大后难以维护

## 模块化方案

在**模块化编程**中，开发者将程序分解成离散功能块（discrete chunks of functionality），并称之为 <strong style="color:red">模块</strong>。

每个模块具有比完整程序更小的接触面，使得校验、调试、测试轻而易举。 精心编写的模块提供了可靠的<u>抽象</u>和<u>封装界限</u>，使得应用程序中每个模块都具有条理清楚的设计和明确的目的。

- CommonJS
  - **同步加载** 依赖的模块
  - 可复用于 Node.js 环境 ，例如做同构应用
  - 成熟的第三方模块社区
  - 无法直接运行于浏览器环境，必须通过工具转换为标准的 ES5
- AMD
  - **异步加载** 依赖的模块
  - **并行** 加载多个模块
  - 可在不转换代码的情况下直接在 **浏览器** 运行
  - 可运行在浏览器和 Node.js 环境
  - 代表性实践 [requirejs](http://requirejs.org)
- ES6 Module
  - 语言层面上实现模块化
  - 需转换成 ES5 实现
- Module in CSS
  - `@import` 语句

![JavaScript 模块化方案](https://pic1.zhimg.com/80/v2-ae9253e557d902369b1beaed998061cb_hd.jpg)

[模块化方案](https://juejin.im/post/5cb004da5188251b130c773e)

- CommonJS：同步，有缓存，一般用于服务器端
- AMD：同步不适合浏览器端，非同步加载模块，允许指定回调函数 `require.js`
- CMD：异步加载，`sea.js`
- ES6：动态引入，按需加载，没有缓存

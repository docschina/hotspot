# webpack 文档更新日志（8.15-8.28）

> 新的更新日志又来了哦，从日志中可以看出插件和 loader 更新会比较频繁，大家可以多关注。

## 英文篇

### 内容更新

#### API

* `compilation-object` 中新增了 `deleteAsset` 字段，用于移除资源

#### loader

* 新增了 `remark-loader`，用于处理 markdown
* `css-loader` 中新增了 `compileType` 的使用说明
  * 当配置为 `icss` 时，只使用 `interoperable css` 的部分特性；当配置为 `modules` 时，会使用所有的 `css module` 特性。（webpack v4 以前，icss 是默认应用于所有文件的）
* `postcss-loader` 完善了文档内容及示例
* `less-loader`
  * 选项中的 `prependData` 和 `appendData` 合并为 `additionalData`，用于在入口文件的开始和结尾处新增 `Less` 代码
  * 选项中的 `implementation` 被移除
  * 选项中新增了 `webpackImporter`，用于启用/禁用 webpack 默认的 importer。

#### 插件

* `DefinePlugin` 新增了 `runtimeValue` 的属性，当 `runtimeValue` 监听的文件变化时，webpack 将重新执行构建
* 使用 `split-chunks-plugin` 插件时，`optimization.splitChunks` 选项的 `chunks` 默认值变更为 `async`
* `mini-css-extract-plugin` 插件中新增了关于 CSS Module 的配置项，`modules` 和 `namedExport`

#### 指南

* `asset-management` 新增关于 loader 链式调用的描述
  * 模块 loader 可以链式调用。链中的每个 loader 都将对资源进行转换。链会逆序执行。第一个 loader 将其结果（被转换后的资源）传递给下一个 loader，依此类推。最后，webpack 期望链中的最后的 loader 返回 JavaScript。

#### 配置

* `externals` 章节新增了 `externalsType` 的说明
  * 当 `externalsType` 设置为 `script` 时，可使用外部 CDN 链接

```js
module.exports = {
  // ...
  externalsType: 'script',
  externals: {
    lodash: ['https://cdn.jsdelivr.net/npm/lodash@4.17.19/lodash.min.js', '_'],
  }
};
```

## 中文篇

### 内容更新

#### loader

* 完成了 `html-loader` 的翻译工作
* 完成了 `css-loader` 新增部分的翻译工作
* 完成了 `loader` 首页的翻译

#### 指南

* 优化了 `concepts` 首页中的描述

#### 插件

* 完成了 `terser-webpack-plugin` 的翻译工作
* 完成了 `mini-css-extract-plugin` 中新增部分的翻译工作。

### 站点更新

* 添加所有新增文档部分的锚点后缀

## 总结

从更新日志的更新频次来看，最近社区针对 CSS Module 的更新较多，大家可以从这方面进行尝试。

## 关注我们

我们将为你带来最前沿的前端资讯。

# webpack 文档更新日志（7.11-7.27）

> Hi，大家好，今天又到了 webpack 文档更新日志同步的环节。

老样子，更新日志会分为英文篇和中文篇：

1. 英文篇会主要介绍 webpack 文档的更新部分；
2. 中文篇则会介绍中文站的最新进展。

话不多说，开始正题。

## 英文篇

### 内容更新

#### API 部分

* 新增了在 loader/plugin 中使用 Logger API 的示例
* Compilation Object 去除了 modifyHash 选项
* 模块方法中新增了引入 data uri 的示例

```js
import 'data:text/javascript;charset=utf-8;base64,Y29uc29sZS5sb2coJ2lubGluZSAxJyk7';
import { number, fn } from 'data:text/javascript;charset=utf-8;base64,ZXhwb3J0IGNvbnN0IG51bWJlciA9IDQyOwpleHBvcnQgY29uc3QgZm4gPSAoKSA9PiAiSGVsbG8gd29ybGQiOw==';
```

#### 配置（Configuration）

* module 中新增了 `Rule.mimetype` 选项，以支持处理 data uri
* Optimization 中的 `minimizer` 选项新增了 `...` 参数用于访问默认值。
* watch 部分不再支持对 `watchOptions` 直接赋值数字后，等同于选项 `aggregateTimeout` 的操作

#### 插件 （Plugin）

* 移除了 HotModuleReplacementPlugin 插件的 `multiStep` 和 `fullBuildTimeout` 的选项。
* `splitChunks` 新增了 `enforceSizeThreshold` 选项

#### loader

* `css-loader` 增加了 `namedExport` 选项
* `css-loader` 删除了 `exportGlobals` 选项

#### 概念

* 模块联合部分中新增了动态远程容器 —— 此部分还未翻译，有兴趣的可以联系我

#### 指南

* 当使用插件生成配置中的 entry 时，`webpack.config` 中的 entry 支持传递空对象
* 构建性能章节，优化了对 `ts-loader` 的描述，增加了优化选项
  * 可以使用 `transpileOnly` 来缩短 ts-loader 的构建时间
  * 但同时会关闭类型检查，可以通过 `ForkTsCheckerWebpackPlugin` 开启
* `output` 章节移除了 `jsonpScriptType`，统一使用 `scriptType`

## 中文篇

### 内容部分

#### API

* 完成了 cli 部分的翻译

#### loader

* 完成了 sass-loader 部分的翻译

### 站点更新

* 优化了锚点跳转，修复了中文造成页面展示异常的问题

> 注：站点更新后，大家在翻译标题时，需保留 `{#}` 中的部分。

## 其他

关于站点优化，有一篇总结近期会呈现给大家，敬请期待。

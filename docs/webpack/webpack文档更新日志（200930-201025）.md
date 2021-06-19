# webpack 文档更新日志（9.30-10.25）

> 10 月初，webpack v5 正式发布。我们也及时进行了维护与跟进，但由于这段时间工作较忙，所以更新日志没有及时更新，见谅。
>
> 在升级 5 之前，请先阅读文档中的迁移指南。
>
> 中文：https://webpack.docschina.org/migrate/5/
>
> 英文：https://webpack.js.org/migrate/5/

## 英文篇

### 内容更新

#### API

- CLI 文档进行了重写，新增了核心 Flag（core flag）的概念
  - 从 CLI v4 和 webpack v5 开始，CLI 将采用从 webpack 的 core 中导入整个配置的模式，允许 CLI 调整几乎所有配置项
  - 支持的选项链接（https://github.com/webpack/webpack-cli/tree/next/packages/webpack-cli#webpack-5）
- Compilation hook 文档中新增了 `chunkIds` 的描述
- Module Variables
  - 移除了关于 `process` 的说明

#### Blog

- 新增了 `2020-10-10-webpack-5-release` 的博文

#### Concepts（概念）

- Modules 章节中对 webpack 所支持的模块类型进行了描述：
  - ECMAScript 模块
  - CommonJS 模块
  - AMD 模块
  - Assets 资源
  - WebAssembly 模块

#### Configuration（配置）

- 配置章节中对原有配置进行了升级，现已改为 webpack 5 相关的配置
- Optimization 章节
  - 移除了 `optimization.namedChunks` 选项
  - 移除了 `optimization.noEmitOnErrors` 选项
  - 新增了 `optimization.emitOnErrors` 选项
  - `optimization.mangleExports` 选项可以赋值为 `deterministic`，默认在生成环境下开启。
- stats 章节
  - 新增了 `stats.assetsSpace`
  - 新增了 `stats.modulesSpace`
  - 新增了 `stats.chunkModulesSpace`
  - 新增了 `stats.nestedModulesSpace`
  - 新增了 `stats.cachedModules`
  - 新增了 `stats.runtimeModules`
  - 新增了 `stats.dependentModules`
  - 新增了 `stats.groupAssetsByChunk`
  - 新增了 `stats.groupAssetsByEmitStatus`
  - 新增了 `stats.groupAssetsByInfo`
  - 新增了 `stats.groupModulesByAttributes`
  - 移除了 `stats.chunkRootModules`
  - 移除了 `stats.maxModules`
  - 新增了 `stats.chunkGroupAuxiliary`
  - 新增了 `stats.chunkGroupChildren`
  - 新增了 `stats.chunkGroupMaxAssets`
  - 新增了 `stats.ignoreWarnings`
  - 使用 `stats.ignoreWarnings` 替换 `stats.warningsFilter`
- target 章节
  - 默认值变更为 `browerlist`，如果未找到 `browserslist`，则为 `web`
  - 支持的类型从 `string` `function (complier)` 改为了 `string` `string[]` `false`
  - 可指定 `node/electron` 的版本，如 `target: node12.18`
  - 当传递 `string[]` 时，会采用数组中 target 的共有特性

#### Guides（指南）

- 新增了 ECMAScript Modules 章节，用于介绍 ECMA
- 新增了 Package exports 章节

#### Loader

- `babel-loader` 章节中中新增了关于容错的说明
  - 忽略不应参与转码的库，例如 `core-js` 和 `webpack/buildin` 如果被 Babel 转码会发生错误
- 移除了 `eslint-loader`，使用 `eslint-webpack-plugin` 代替
- 重写了 `postcss-loader` 章节
- 新增了 `stylus-loader` 章节

#### Migrate

- 更新了 4 迁移到 5 的迁移文档，升级前，建议仔细阅读文档

#### Plugin

- 新增了 `eslint-webpack-plugin`
- 新增了 `html-minimizer-webpack-plugin`
- 新增了 `image-minimizer-webpack-plugin`
- 新增了 `json-minimizer-webpack-plugin`
- 细化了 `mini-css-extract-plugin`
  - 对每个选项进行了详细说明
- `terser-webpack-plugin`
  - 移除了 `cache` 选项
  - 移除了 `cacheKeys` 选项

### 站点更新

1. webpack 官网新增了黑暗主题
2. webpack 官网的构建升级至 webpack 5
3. 更新了获取 plugin 和 loader 的方式
4. 新增了处理 markdown 的单测

## 中文篇

### 内容更新

翻译了 webpack 5 大部分的文档内容：

- 博客部分
- 各个章节新增的部分

### 站点更新

- 自动同步的方式进行优化，保证始终与官网的更新获取方式同步
- 修复了官网更新后带来的 bug，并反馈给官方
- 并为官方提供了单测

## 关注我们

我们将为你带来最前沿的前端资讯。

# webpack 文档更新日志（8.29-9.11）

> 本期 webpack 文档更新日志又来了哦，最近 webpack 文档以删为主，本期主要删除了 5 个相关插件。

## 英文篇

### 内容更新

#### Plugins

* 移除了 `i18n-webpack-plugin`
* 移除了 `extract-text-webpack-plugin`
* 移除了 `babel-minify-webpack-plugin`
* 移除了 `uglifyjs-webpack-plugin`
* 移除了 `zopfli-webpack-plugin`
* `common-chunk-plugin` 中移除了关于 `extract-text-webpack-plugin` 的配置
* `copy-webpack-plugin` 新增了 `filter` 选项，用于对拷贝文件进行过滤
* `css-minimizer-webpack-plugin` 中修改了使用示例，在 `minify` 选项中修改了入参并添加了 `source-map` 的用法

#### Loaders

* `source-map-loader` 新增了选项 `filterSourceMappingUrl`，用于过滤 `SourceMappingURL`
* 更新了 `postcss-loader` 文档

#### 配置

* `output` 选项中新增了 `charset`，用于为 `<script>` 新增 `charset`（兼容非现代浏览器）
* `output` 选项中新增了 `scriptType`，用于为异步 `chunk` 添加 `<script>` 类型
  * 如果 `output.module` 设置为 `true`，即使设置了 `scriptType: module`，也会被替换为 `false`

### 站点更新

* 移除了未被使用的 `markdown-loader` 以及 `marked`
* 升级了站点的 `eslint` 版本

## 中文篇

### 内容更新

#### Plugins

* 同步移除了相关插件
* 对插件中新增的选项进行了翻译

#### API

* 对 `node` 中的 `MultiCompiler` 相关翻译进行了优化

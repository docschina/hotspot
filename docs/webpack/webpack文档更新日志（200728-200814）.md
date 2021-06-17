# webpack 文档更新日志（7.28-8.14）

> webpack 更新日志又来了哦，近期对之前的 changelog-v5 的中文版进行了更新，新增了许多 beta 版相关的日志。
>
> 对 v5 变化感兴趣的，可以持续关注我们的 webpack 文档更新日志。
>
> 也可阅读原文查看 changelog-v5 的最新中文版。

## 英文篇

### 内容更新

#### 配置

* `experiments` 实验特性部分，去除了 `importAsync` 和 `importAwait`

#### 指南

* `code-splitting` 新增了 `runtimeChunk` 选项，用于在单个 HTML 上使用多个入口时，需将其设置为 `single`
* `asset-modules` webpack 5 的资源模块针对 `asset/resource` 导出时，新增了 query 字段
  * 例如：`asset/resource` 模块以 `[hash][ext][query]`

#### loader

* css-loader
  * 新增 `icss` 选项，此会启用对 css module 的支持
  * 去除了 `icss` 选项，新增 `compileType` 选项，其值可设置为 module 或 icss
* worker-loader 文档对其参数进行了详细介绍，同时新增了示例
* html-loader 修改了 `minimize` 为 true 时，启用的压缩内容
* postcss-loader 的 config 新增 boolean 和 string 类型
  * boolean 启用或关闭自动加载配置
  * string 配置文件的路径

#### plugin

* `terser-webpack-plugin`
  * 对 `filename` 选项新增了友好的提示
  * 去除了 `warningsFilter` 选项
  * 完善了相关配置，新增了使用示例。
* 新增 `css-minimizer-webpack-plugin`，用于优化 css 压缩，目前版本 1.1.1
* 移除了 `eval-source-map-dev-tool-plugin` 中的 `filename` 选项

## 中文篇

### 内容更新

#### 迁移

* 对 `从 v4 升级到 v5` 进行了重译

#### loader

* 完成了 `style-loader` 的翻译工作。
* 完成了 `css-loader` 的翻译工作
* 完成了 `thead-loader` 的翻译工作

#### plugin

* 完成了 `mini-css-extract-plugin` 的翻译工作

## 关注我们

我们将持续为你带来最前沿的前端资讯。

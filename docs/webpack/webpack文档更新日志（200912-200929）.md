# webpack 文档更新日志（9.12-9.29）

> 久等了，本应上周五发的更新日志，现在才来。话不多说，开始正题。

## 英文篇

### 内容更新

#### API

* `loader` 中移除了对 `this.exec` 的说明
* `module-methods` 中新增了 webpack 对模块一致性检查的说明，可在 `package.json` 中设置值为 `module` 或 `commonjs` 的 type 字段，用于对编译时的语法进行一致性检查（针对 es6 与 commonjs 混用的情况）
* `module-variables` 中新增了两项：
  * 新增了 `import.meta.url` 用于获取模块的绝对路径（以 `file:` 开头）
  * 新增了 `import.meta.webpack` 用于获取当前 `webpack` 的版本

#### 配置

* 实验特性 - 移除了 `webpack.config.js` 中 `experiments` 的 `mjs` 字段和 `asset` 字段
* module - 新增了 `resolve.fullySpecified` 的选项，用于模块一致性检查。当在代码中引入模块时，是否需要编写后缀名。
* stats - 新增了 `errorsCount` 和 `warningsCount` 的字段，用于判断是否返回 error 或 warning 的计数。
* output
  * `output.futureEmitAssets` 选项用于让 webpack 使用未来版本的 `emit` 逻辑，**在 `emit` 完成后释放资源文件内存**。（此选项将在 v5 中移除，并默认支持）
  * `output.wasmLoading` 选项用于配置 webpack 加载 wasm 模块的方式，并且会根据 `target` 的不同而变化
  * `output.enabledWasmLoadingTypes` 选项用于配置 webpack 入口所支持的 wasm 加载方式的列表。
* externals - 新增了 externals 相关的使用示例以及配置。
  * 新增了 `externalsPresets` 选项

> ps: 近期官方对 output 改动较多，而且很频繁。output 中新增了很多选项。建议 v5 上了之后重新过一遍 output 的文档。

#### 插件

* `terser-webpack-plugin` 中新增了 `targetOption` 的选项，可以声明自定义的配置项，在 `minify` 函数中使用。

#### 指南

* `asset-management`（资源管理）对 `type` 为 json 的资源可启用自定义 parser 进行了说明

#### loader

* 优化了 `thread-loader` 的说明，此 loader 仅在有耗时操作时使用

### 站点更新

* 新增了 `webpack-contrib/stylus-loader` 的文档
* 优化了切换 tab 回到顶部的动画效果

## 中文站

### 内容更新

* 完成了上述内容的翻译工作

## 总结

`webpack` 最近更新频繁，v5 发布在即。今晨发布了 5.0.0-rc.1 版本，大家敬请期待。

中文文档目前与官方文档同步更新中，时差不会大于一周。请大家认准 webpack 中文官网 `https://webpack.docschina.org`。

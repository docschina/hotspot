# webpack 文档更新日志（10.26-11.17）

> HI，各位小伙伴，不定期更新的 webpack 文档更新日志来了哦。这次更新的内容不多，但是很实用。

在说更新日志前，说**两个**文档更新较为重要的点：

1. **webpack 5 中 `webpack-dev-server` 的启动方式发生了变化**；
2. webpack 5 处理资源文件时，**可不使用 `raw-loader`、`file-loader` 以及 `url-loader` 等 loader 处理**，可直接使用全新的 [资源模块（Asset Modules）](https://webpack.docschina.org/guides/asset-modules/)。

## devServer 的变化

启动 devServer 的方式由 `webpack-dev-server` 变更为 `webpack serve`

```bash
# 旧的启动方式
webpack-dev-server --content-base ./dist
# 现在的启动方式
webpack serve --content-base ./dist
```

如需升级 webpack 5 的小伙伴，请周知。

## 资源模块（Asset Modules）

举个简单的例子，大家就明白用法了：

```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ['style-loader', 'css-loader'],
      },
    {
      test: /\.(png|svg|jpg|jpeg|gif)$/i,
      type: 'asset/resource', // 新方式
    },
    ],
  },
};
```

最后，附上两个文档链接：

* [指南->管理资源](https://webpack.docschina.org/guides/asset-management/#loading-images)
* [资源模块](https://webpack.docschina.org/guides/asset-modules/)

## 英文篇

### 内容更新

#### 配置

* DevServer 章节
  * 移除了 `devServer.color` 的说明
  * 移除了 `devServer.socket` 的说明
* 其他选项
  * 新增 `ignoreWarnings`，用于忽略警告信息
* stats 章节
  * 弃用了 `stats.warningsFilter`，使用 `ignoreWarnings` 替代
* mode 章节
  * 新增可选择 `'none'`，当 mode 为 `'none'` 时不使用任何优化项

#### API

* CLI 章节
  * `--progress` 查看编译进度的参数，默认为 boolean，新增字符串的支持。如需查看详细的 `profile`，可以使用 `--progress=profile`

#### 指南

* hot module replacement 章节
  * 将原有 `webpack-dev-server` 的命令更新为 `webpack serve`
* tree shaking 章节
  * tree shaking 内部使用的匹配库由 `micromatch` 变更为 `glob-to-regexp`
* asset modules 章节
  * 新增关于 webpack 5 中将 `file-loader`、`raw-loader` 以及 `url-loader` 与资源模块一同使用时，出现模块重复问题的说明，**解决方案为将 asset type 设置为 `'javascript/auto'`**

#### 插件

* terser-webpack-plugin
  * 此插件 webpack 5 已默认支持，如使用 5 及以上版本无需再安装

## 中文篇

1. 对以上内容进行了同步跟进
2. 优化了部分文档的翻译
3. 完成了 `configuration-types` 章节的翻译工作

## 关注我们

我们将为你带来最前沿的前端资讯。

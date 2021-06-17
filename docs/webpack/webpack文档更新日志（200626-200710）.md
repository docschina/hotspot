# webpack 本周更新日志（6.16-7.10）

> 伴随着 webpack 5 beta 版本特性的更新，webpack 的文档也在升级中。这期间文档内容、loader 和 plugin 都在发生着变化。为了让国内的前端开发能够及时看到文档中的变化，我们（印记中文团队）决定每半个月向大家同步一篇 webpack 文档的更新日志（包括中文站）。

更新日志分为英文篇和中文篇：

1. 英文篇会主要介绍 webpack 文档的更新部分；
2. 中文篇则会介绍翻译工作的最新进展。

## 英文篇

### 站点更新

* 采用了 [cypress](https://www.cypress.io/) 对 [webpack.js.org](https://webpack.js.org/) 进行 E2E 测试。

> ps: cypress 是基于 Mocha API 的基础上开发的一套开箱即用的 E2E 测试框架，并不依赖前端框架，也无需其他测试工具库，配置简单，并且提供了强大的 GUI 图形工具，可以自动截图录屏，实现时空旅行并在测试流程中 Debug 等等。

### 内容更新

#### loader

* 删除了 bundle-loader 文档
* 删除了 transform-loader 文档

#### plugin

* 新增了 NoEmitOnErrorsPlugin 文档

#### 概念（concepts）

* 更新了 Module Federation 部分的文档

#### 配置（Configuration）

* 更新了 output 文档
  * 新增 `ouput.scriptType` 选项
* 更新了 resolve 文档
  * 新增 `resolve.restrictions` 选项

#### 参考资料

* [用 Cypress 拯救业务项目的前端自动化测试](https://juejin.im/post/5db1cf6251882502905024c7)

## 中文篇

> 中文站点有大概长达一年多的时间没有进行维护，最近我们重启了中文站点的翻译工作，对翻译工作感兴趣的戳这里。

### 同步

为了实现同步，我编写了两个小工具：

1. 自动与英文站进行同步
2. 自动下载最新的 loader

以上两个操作都会结合自动合并到中文站，如果产生冲突会自动发起 Pull Request。

再结合 Github Action 就可以实现自动同步。

### 翻译进展

#### 指南及配置

由 [lcxfs1991](https://github.com/lcxfs1991)、[dear-lizhihua](https://github.com/dear-lizhihua) 以及 [QC-L](https://github.com/QC-L) 共同完成。

这两部分内容，已基本完成。

#### API

| 文章 | 译者 | 校对者 | 状态 |
| :----: | :----: | :----: | :----: |
| Plugin API | [chenzesam](https://github.com/chenzesam) | [QC-L](https://github.com/QC-L) | 已完成 |
| Logger 接口 | [wangjq4214](https://github.com/wangjq4214) | [QC-L](https://github.com/QC-L) | 已完成 |
| Compilation Object | [jacob-lcs](https://github.com/jacob-lcs) | [QC-L](https://github.com/QC-L) | 已完成 |
| API 首页 | [Xeonice](https://github.com/Xeonice) | [QC-L](https://github.com/QC-L) | 已完成 |
| Module 变量 | [carlz812](https://github.com/carlz812) | [QC-L](https://github.com/QC-L)/[lcxfs1991](https://github.com/lcxfs1991) | 已完成 |
| Module 方法 | [kingzez](https://github.com/kingzez) | [QC-L](https://github.com/QC-L) | 已完成 |
| HMR | [Geekhyt](https://github.com/Geekhyt) | [QC-L](https://github.com/QC-L) | 已完成 |
| 解析器 | [pampang](https://github.com/pampang) | [QC-L](https://github.com/QC-L) | 已完成 |
| Node | [ssszp](https://github.com/ssszp) | [QC-L](https://github.com/QC-L) | 已完成 |
| CLI | [Fonkie](https://github.com/Fonkie) | [QC-L](https://github.com/QC-L) | 校对修改中 |
| Loader 接口 | [Aastasia](https://github.com/Aastasia) | [QC-L](https://github.com/QC-L) | 校对修改中 |
| Stats 数据 | - | - | 未认领 |

### 配置

| 文章 | 译者 | 校对者 | 状态 |
| :----: | :----: | :----: | :----: |
| 配置首页 | [dear-lizhihua](https://github.com/dear-lizhihua) | - | 翻译中 |
| Output | [navigatorOpera](https://github.com/navigatorOpera) | [QC-L](https://github.com/QC-L) | 已完成 |
| Mode | [aa875982361](https://github.com/aa875982361) | [QC-L](https://github.com/QC-L) | 已完成 |
| 入口及上下文 | [jungor](https://github.com/jungor) | [QC-L](https://github.com/QC-L) | 已完成 |
| 配置类型 | [doflamin](https://github.com/doflamin) | [QC-L](https://github.com/QC-L) | 校对修改中 |
| 配置语言 | [NealST](https://github.com/NealST) | [QC-L](https://github.com/QC-L) | 已完成 |
| DevServer | [jerexyz](https://github.com/jerexyz) | [QC-L](https://github.com/QC-L) | 已完成 |
| DevTool | [weiyuan0609](https://github.com/weiyuan0609) | [QC-L](https://github.com/QC-L) | 已完成 |

### 迁移

| 文章 | 译者 | 校对者 | 状态 |
| :----: | :----: | :----: | :----: |
| 从 v3 升级到 v4 | [Catherine001li](https://github.com/Catherine001li) | [QC-L](https://github.com/QC-L)/[lcxfs1991](https://github.com/lcxfs1991) | 已完成 |
| 从 v1 升级到 v2 或 v3 | [miazhuce](https://github.com/miazhuce) | [QC-L](https://github.com/QC-L) | 已完成 |

### Loader 及 Plugin

由于 loader 和 plugin 部分内容较多，这里只展示已完成的内容。

### Loader

| 文章 | 译者 | 校对者 |
| :----: | :----: | :----: |
| work-loader | [jacob-lcs](https://github.com/jacob-lcs) | [QC-L](https://github.com/QC-L) |
| babel-loader | [flytam](https://github.com/flytam) | [QC-L](https://github.com/QC-L) |
| bundle-loader | [mercurywang](https://github.com/mercurywang) | [QC-L](https://github.com/QC-L)/[lcxfs1991](https://github.com/lcxfs1991) |
| cache-loader | [mercurywang](https://github.com/mercurywang) | [QC-L](https://github.com/QC-L) |
| coffee-loader | [mercurywang](https://github.com/mercurywang) | [QC-L](https://github.com/QC-L) |
| eslint-loader | [Xeonice](https://github.com/Xeonice) | [QC-L](https://github.com/QC-L) |
| less-loader | [phobal](https://github.com/phobal) | [QC-L](https://github.com/QC-L) |
| source-map-loader | [mercurywang](https://github.com/mercurywang) | [QC-L](https://github.com/QC-L) |

> 由于 bundle-loader 已弃用，此文档将于近期移除。为了表示对译者尊重，所以这里做了展示。

### Plugin

| 文章 | 译者 | 校对者 |
| :----: | :----: | :----: |
| dll-plugin | [weiyuan0609](https://github.com/weiyuan0609) | [QC-L](https://github.com/QC-L) |
| hot-module-replacement-plugin | [navigatorOpera](https://github.com/navigatorOpera) | [QC-L](https://github.com/QC-L) |
| i18n-webpack-plugin | [NealST](https://github.com/NealST) | [QC-L](https://github.com/QC-L) |
| uglifyjs-webpack-plugin | [jefferyE](https://github.com/jefferyE) | [QC-L](https://github.com/QC-L) |

### 其它

| 文章 | 译者 | 校对者 | 状态 |
| :----: | :----: | :----: | :----: |
| 品牌 | [helianthuswhite](https://github.com/helianthuswhite) | [QC-L](https://github.com/QC-L)/[lcxfs1991](https://github.com/lcxfs1991) | 已完成 |
| 对比 | [mercurywang](https://github.com/mercurywang) | [QC-L](https://github.com/QC-L) | 已完成 |
| 词汇表 | [daxiaoxiaodejia](https://github.com/daxiaoxiaodejia) | [QC-L](https://github.com/QC-L)/[lcxfs1991](https://github.com/lcxfs1991) | 已完成 |
| 许可证 | [GSZS](https://github.com/GSZS) | [QC-L](https://github.com/QC-L) | 已完成 |

以上为半月以来 webpack 中文文档的更新内容。

感谢所有译者的参与与支持，同时希望更多的开发者参与到社区的维护中来。

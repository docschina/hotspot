# Vue 3.1.0 的 beta 版发布

> 以往都是翻译给大家，这次换个形式为大家介绍。

本次 beta 版本带来了一些有趣的新特性和错误修复。

## 新特性

* `onServerPrefetch`：composition-api 版本的 serverPrefetch
* 组件级别的 `compilerOptions`
* `@vue/compiler-core` 支持了空白字符处理策略
* 支持通过 `app.config.compilerOptions` 配置运行时的编译器
* devtools 改进了对 KeepAlive 的支持
* 支持通过 `is="vue:xxx"` 将普通元素转换为组件

### `onServerPrefetch`

具体请参见 [PR 3070](https://github.com/vuejs/vue-next/pull/3070) 和 [PR 2902](https://github.com/vuejs/vue-next/pull/2902)

此特性主要解决在 `composition-api` 情况下没有提供 serverPrefetch 生命周期钩子函数的问题。

这个钩子函数名为 `onServerPrefetch`。

如果你也这方面的需求，可以尝试升级至 `3.1.0-beta` 版

相关讨论:

* [vue-apollo](https://github.com/vuejs/vue-apollo/issues/1102)
* [app-extension-apollo](https://github.com/quasarframework/app-extension-apollo/issues/51#issuecomment-791977057)

### `@vue/complier-core` 支持了空白字符处理策略

具体内容请参阅 [PR 1600](https://github.com/vuejs/vue-next/pull/1600) 和 [v2 原有效果](https://github.com/vuejs/vue/blob/dev/flow/compiler.js#L10)。

#### 应用

我们来测试下此策略：

先装个 beta 版本的 `@vue/compiler-core`

```bash
yarn add @vue/compiler-core@beta
```

新建 `index.js` 文件

```js
const core = require('@vue/compiler-core')

const { baseCompile: complie } = core

const { ast } = complie(`      foo \n bar baz      `, {
  whitespace: 'preserve' // condense
})

console.log(ast.children[0])
console.log(ast.children[0].content)
```

大概效果如示例所示：

``` html
<!-- 源代码 -->
      foo \n bar baz     

<!-- whitespace: 'preserve' -->
      foo \n bar baz     

<!-- whitespace: 'condense' -->
 foo bar baz 
```

#### 源码

原本只在 `compiler-core` 的 `parser` 文件中的 `defaultParserOptions` 提供了默认的 `condense` 情况

```ts
whitespace: 'condense'
```

在 `compiler-core` 的 options 文件中新增了 `whitespace`：

```ts
whitespace?: 'preserve' | 'condense'
```

相关链接：

* [PR 1600](https://github.com/vuejs/vue-next/pull/1600)
* [stackoverflow](https://stackoverflow.com/questions/64432182/vue-3-removes-white-space-between-inline-block-elements)
* [vue 2.0/compiler](https://github.com/vuejs/vue/blob/dev/flow/compiler.js#L10)
* [vue 2.0 的 `whitespace`](https://github.com/vuejs/vue/issues/9208#issuecomment-450012518)
* [vue 2.0 的 PR](https://github.com/vuejs/vue/commit/e1abedb9e66b21da8a7e93e175b9dabe334dfebd)

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

原本只在 `compiler-core` 的 `parse` 文件中的 `defaultParserOptions` 提供了默认的 `condense` 情况

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

### 通过 `is="vue:xxx"` 支持普通元素的转换

这条特性的更新，从源码上看，兼容了两种类型。

1. 弃用的 `v-is` 指令
2. `is="vue:xxx"` 的属性

#### 源码

```js
  let { tag } = node

  // 1. 动态组件
  const isExplicitDynamic = isComponentTag(tag)
  const isProp =
    findProp(node, 'is') || (!isExplicitDynamic && findDir(node, 'is'))
  if (isProp) {
    if (!isExplicitDynamic && isProp.type === NodeTypes.ATTRIBUTE) {
      // <button is="vue:xxx">
      // 如果不是 <component>，仅仅是 "vue:" 开头
      // 在解析阶段会被视为组件，并在此处进行
      // tag 被重新赋值为 "vue:" 以后的内容
      tag = isProp.value!.content.slice(4)
    } else {
      const exp =
        isProp.type === NodeTypes.ATTRIBUTE
          ? isProp.value && createSimpleExpression(isProp.value.content, true)
          : isProp.exp
      if (exp) {
        return createCallExpression(context.helper(RESOLVE_DYNAMIC_COMPONENT), [
          exp
        ])
      }
    }
  }
```

```js
// 当 tag 为 <component>，或者 is="vue:xxx"，跳过后续处理
if (
  name === 'is' &&
  (isComponentTag(tag) || (value && value.content.startsWith('vue:')))
) {
  continue
}
// ...
```

上述代码中有几个点：

1. 首先 `isComponentTag`，用以判断是否为动态组件：

```js
// 此方法用于判断是否为动态组件
function isComponentTag(tag: string) {
  return tag[0].toLowerCase() + tag.slice(1) === 'component'
}
```

2. 查找是否含有 `is` 属性

```js
// 先查属性
findProp(node, 'is')
// 否则判断是不是动态组件，如果不是，判断是不是指令
!isExplicitDynamic && findDir(node, 'is')
```

其主要原因是，两者的 AST 结构不同。

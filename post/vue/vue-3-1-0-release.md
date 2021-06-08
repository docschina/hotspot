# Vue v3.1.0 Pluto 发布

随着 Vue 3 生态的逐步稳定，Vue 3 的迁移版本也提上了日程。

就在今天早些时候，Vue 官方团队正式发布了 v3.1.0 版本。

后续版本都会致力于让你更轻松地从 Vue 2 迁移至 Vue 3。

为此，官方团队做了很多努力。

## 文档更新

官方团队针对迁移文档进行了进一步的完善 [PR 1033](https://github.com/vuejs/docs-next/pull/1033/files)

其中尤大在 [Vue Conf China 2021](https://vue.w3ctech.com/) 上提到的 [Vue Compat](https://github.com/vuejs/vue-next/tree/master/packages/vue-compat) 仓库的文档，也一并合并到了官方文档中，其连接为 [迁移构建](https://v3.vuejs.org/guide/migration/migration-build.html)。

## 破坏性更改

本次更新中存在两个小的破坏性更改，

### 1. props 中声明的 key，将一直存在。不管父组件是否传递该 key。

源码中的核心代码如下：

```js
// ensure all declared prop keys are present
for (const key in instance.propsOptions[0]) {
  if (!(key in props)) {
    props[key] = undefined // 如果 key 不存在 props 中，将默认在 props 中进行声明
  }
}
```

这会导致一些行为的变化，比如(issues 中的案例)：

因为字段一直存在，所以在使用 `hasOwnProperty` 时，就会出现异常情况。

Old:

```js
const propsToAdd = {
  'value': props.hasOwnProperty('modelValue') ? props.modelValue : props.value
}
```

New:

```js
const propsToAdd = {
  'value': props.hasOwnProperty('modelValue') && props.modelValue !== undefined ? props.modelValue : props.value
}
```

官方文档给出了相应解释：

> Similar to `this.$props` when using Options API, the `props` object will only contain explicitly declared props. Also, all declared prop keys will be present on the `props` object, regardless of whether it was passed by the parent component or not. Absent optional props will have a value of `undefined`.
>
> If you need to check the absence of an optional prop, you can give it a Symbol as its default value:

如果你遇到了这方面的问题，可以按照如下方式修改：

```js
const isAbsent = Symbol()

export default {
  props: {
    foo: { default: isAbsent }
  },
  setup(props) {
    if (props.foo === isAbsent) {
      // foo is absent
    }
  }
}
```

相关链接：

- [issue 3288](https://github.com/vuejs/vue-next/issues/3288)
- [issue 3889](https://github.com/vuejs/vue-next/issues/3889)
- [commit](https://github.com/vuejs/vue-next/commit/4fe4de0a49ffc2461b0394e74674af38ff5e2a20)
- [文档更新](https://github.com/vuejs/docs-next/commit/e41f44167f637bbff1f7c3ee041e8e7b37d56e22)

### 2. `optionsMergeStrategies` 不再接收组件实例作为第三个参数

这个对大家影响不大，主要用于生成警告。

## 弃用

- `app.config.isCustomElement` 已被废弃，应使用 `app.config.compilerOptions` 下的 `isCustomElement` 选项。[文档](https://v3.vuejs.org/api/application-config.html#compileroptions)
- `delimiters` 组件选项已被废弃，请使用 `compilerOptions` 下的 `delimiters` 选项。[文档](https://v3.vuejs.org/api/options-misc.html#compileroptions)
- `v-is` 已弃用，请使用 `is="vue:xxx"` 代替。[文档](https://v3.vuejs.org/api/special-attributes.html#is)

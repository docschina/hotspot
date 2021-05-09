# [3.1.0-beta.2](https://github.com/vuejs/vue-next/compare/v3.1.0-beta.1...v3.1.0-beta.2) (2021-05-08)

### Bug 修复

* **兼容：** 处理并警报 config.optionMergeStrategies ([94e69fd](https://github.com/vuejs/vue-next/commit/94e69fd3896214da6ff8b9fb09ad942c598053c7))

# [3.1.0-beta.1](https://github.com/vuejs/vue-next/compare/v3.0.11...v3.1.0-beta.1) (2021-05-08)

### Bug 修复

* **compiler-core:** 当注释选项被打开，在生产环境下保留注释 ([e486254](https://github.com/vuejs/vue-next/commit/e4862544310a4187dfc8b3a49944700888bb60e3))
* **hmr:** 不再从组件种类中移除 __file key ([9db3cbb](https://github.com/vuejs/vue-next/commit/9db3cbbfc1a072675a8d0e53edf3869af115dc60))
* **hydration:** 修复 asnyc 组件水合前更新 ([#3563](https://github.com/vuejs/vue-next/issues/3563)) ([c8d9683](https://github.com/vuejs/vue-next/commit/c8d96837b871d7ad34cd73b4669338be5fdd59fd)), closes [#3560](https://github.com/vuejs/vue-next/issues/3560)
* **reactivity:** 修复 readonly + reactive Map 的追溯 ([#3604](https://github.com/vuejs/vue-next/issues/3604)) ([5036c51](https://github.com/vuejs/vue-next/commit/5036c51cb78435c145ffea5e82cd620d0d056ff7)), closes [#3602](https://github.com/vuejs/vue-next/issues/3602)
* **runtime-core:** 确保声明 props 的 key 永远存在 ([4fe4de0](https://github.com/vuejs/vue-next/commit/4fe4de0a49ffc2461b0394e74674af38ff5e2a20)), closes [#3288](https://github.com/vuejs/vue-next/issues/3288)
* **runtime-core:** 监听多源: computed ([#3066](https://github.com/vuejs/vue-next/issues/3066)) ([e7300eb](https://github.com/vuejs/vue-next/commit/e7300eb47960a153311d568d7976ac5256eb6297)), closes [#3068](https://github.com/vuejs/vue-next/issues/3068)
* **Teleport:** 避免改变对 vnode.dynamicChildren 的引用 ([#3642](https://github.com/vuejs/vue-next/issues/3642)) ([43f7815](https://github.com/vuejs/vue-next/commit/43f78151bfdff2103a9be25e66e3f3be68d03a08)), closes [#3641](https://github.com/vuejs/vue-next/issues/3641)
* **watch:** 避免遍历 non-plain 对象 ([62b8f4a](https://github.com/vuejs/vue-next/commit/62b8f4a39ca56b48a8c8fdf7e200cb80735e16ae))
* **watch:** this.$watch 应该支持监听键路径 ([870f2a7](https://github.com/vuejs/vue-next/commit/870f2a7ba35245fd8c008d2ff666ea130a7e4704))

### 功能

* onServerPrefetch ([#3070](https://github.com/vuejs/vue-next/issues/3070)) ([349eb0f](https://github.com/vuejs/vue-next/commit/349eb0f0ad78f9cb491278eb4c7f9fe0c2e78b79))
* 当使用运行时编译器支持组件级 `compilerOptions` ([ce0bbe0](https://github.com/vuejs/vue-next/commit/ce0bbe053abaf8ba18de8baf535e175048596ee5))
* **compiler-core:** whitespace 处理策略 ([dee3d6a](https://github.com/vuejs/vue-next/commit/dee3d6ab8b4da6653d15eb148c51d9878007f6b6))
* **config:** 利用 `app.config.compilerOptions` 支持配置运行时编译器 ([091e6d6](https://github.com/vuejs/vue-next/commit/091e6d67bfcc215227d78be578c68ead542481ad))
* **devtools:** 升级对 KeepAlive 的支持 ([03ae300](https://github.com/vuejs/vue-next/commit/03ae3006e1e678ade4377cd10d206e8f7b4ad0cb))
* 支持利用 is="vue:xxx" 将 plain 元素 cast 到组件 ([af9e699](https://github.com/vuejs/vue-next/commit/af9e6999e1779f56b5cf827b97310d8e4e1fe5ec))

### 性能提升

* 仅当实际改变时才会触发 $attrs 的更新 ([5566d39](https://github.com/vuejs/vue-next/commit/5566d39d467ebdd4e4234bc97d62600ff01ea28e))
* **compiler:** 解析结束标签时跳过不必要的检查 ([048ac29](https://github.com/vuejs/vue-next/commit/048ac299f35709b25ae1bc1efa67d2abc53dbc3b))

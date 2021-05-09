# [3.1.0-beta.2](https://github.com/vuejs/vue-next/compare/v3.1.0-beta.1...v3.1.0-beta.2) (2021-05-08)

### Bug Fixes

* **compat:** handle and warn config.optionMergeStrategies ([94e69fd](https://github.com/vuejs/vue-next/commit/94e69fd3896214da6ff8b9fb09ad942c598053c7))

# [3.1.0-beta.1](https://github.com/vuejs/vue-next/compare/v3.0.11...v3.1.0-beta.1) (2021-05-08)

### Bug Fixes

* **compiler-core:** preserve comment content in production when comments option is enabled ([e486254](https://github.com/vuejs/vue-next/commit/e4862544310a4187dfc8b3a49944700888bb60e3))
* **hmr:** don't remove __file key from component type ([9db3cbb](https://github.com/vuejs/vue-next/commit/9db3cbbfc1a072675a8d0e53edf3869af115dc60))
* **hydration:** fix update before async component is hydrated ([#3563](https://github.com/vuejs/vue-next/issues/3563)) ([c8d9683](https://github.com/vuejs/vue-next/commit/c8d96837b871d7ad34cd73b4669338be5fdd59fd)), closes [#3560](https://github.com/vuejs/vue-next/issues/3560)
* **reactivity:** fix tracking for readonly + reactive Map ([#3604](https://github.com/vuejs/vue-next/issues/3604)) ([5036c51](https://github.com/vuejs/vue-next/commit/5036c51cb78435c145ffea5e82cd620d0d056ff7)), closes [#3602](https://github.com/vuejs/vue-next/issues/3602)
* **runtime-core:** ensure declare prop keys are always present ([4fe4de0](https://github.com/vuejs/vue-next/commit/4fe4de0a49ffc2461b0394e74674af38ff5e2a20)), closes [#3288](https://github.com/vuejs/vue-next/issues/3288)
* **runtime-core:** watching multiple sources: computed ([#3066](https://github.com/vuejs/vue-next/issues/3066)) ([e7300eb](https://github.com/vuejs/vue-next/commit/e7300eb47960a153311d568d7976ac5256eb6297)), closes [#3068](https://github.com/vuejs/vue-next/issues/3068)
* **Teleport:** avoid changing the reference of vnode.dynamicChildren ([#3642](https://github.com/vuejs/vue-next/issues/3642)) ([43f7815](https://github.com/vuejs/vue-next/commit/43f78151bfdff2103a9be25e66e3f3be68d03a08)), closes [#3641](https://github.com/vuejs/vue-next/issues/3641)
* **watch:** avoid traversing non-plain objects ([62b8f4a](https://github.com/vuejs/vue-next/commit/62b8f4a39ca56b48a8c8fdf7e200cb80735e16ae))
* **watch:** this.$watch should support watching keypath ([870f2a7](https://github.com/vuejs/vue-next/commit/870f2a7ba35245fd8c008d2ff666ea130a7e4704))

### Features

* onServerPrefetch ([#3070](https://github.com/vuejs/vue-next/issues/3070)) ([349eb0f](https://github.com/vuejs/vue-next/commit/349eb0f0ad78f9cb491278eb4c7f9fe0c2e78b79))
* support component-level `compilerOptions` when using runtime compiler ([ce0bbe0](https://github.com/vuejs/vue-next/commit/ce0bbe053abaf8ba18de8baf535e175048596ee5))
* **compiler-core:** whitespace handling strategy ([dee3d6a](https://github.com/vuejs/vue-next/commit/dee3d6ab8b4da6653d15eb148c51d9878007f6b6))
* **config:** support configuring runtime compiler via `app.config.compilerOptions` ([091e6d6](https://github.com/vuejs/vue-next/commit/091e6d67bfcc215227d78be578c68ead542481ad))
* **devtools:** improved KeepAlive support ([03ae300](https://github.com/vuejs/vue-next/commit/03ae3006e1e678ade4377cd10d206e8f7b4ad0cb))
* support casting plain element to component via is="vue:xxx" ([af9e699](https://github.com/vuejs/vue-next/commit/af9e6999e1779f56b5cf827b97310d8e4e1fe5ec))

### Performance Improvements

* only trigger $attrs update when it has actually changed ([5566d39](https://github.com/vuejs/vue-next/commit/5566d39d467ebdd4e4234bc97d62600ff01ea28e))
* **compiler:** skip unncessary checks when parsing end tag ([048ac29](https://github.com/vuejs/vue-next/commit/048ac299f35709b25ae1bc1efa67d2abc53dbc3b))

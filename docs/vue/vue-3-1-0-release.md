# [3.1.0](https://github.com/vuejs/vue-next/compare/v3.1.0-beta.7...v3.1.0) (2021-06-07)

### Features

- [Migration Build](https://v3.vuejs.org/guide/migration/migration-build.html)
- **compiler-core:** whitespace handling strategy ([dee3d6a](https://github.com/vuejs/vue-next/commit/dee3d6ab8b4da6653d15eb148c51d9878007f6b6))
- support component-level `compilerOptions` when using runtime compiler ([ce0bbe0](https://github.com/vuejs/vue-next/commit/ce0bbe053abaf8ba18de8baf535e175048596ee5))
- **config:** support configuring runtime compiler via `app.config.compilerOptions` ([091e6d6](https://github.com/vuejs/vue-next/commit/091e6d67bfcc215227d78be578c68ead542481ad))
- support casting plain element to component via is="vue:xxx" ([af9e699](https://github.com/vuejs/vue-next/commit/af9e6999e1779f56b5cf827b97310d8e4e1fe5ec))
- **devtools:** improved KeepAlive support ([03ae300](https://github.com/vuejs/vue-next/commit/03ae3006e1e678ade4377cd10d206e8f7b4ad0cb))
- **devtools:** performance events ([f7c54ca](https://github.com/vuejs/vue-next/commit/f7c54caeb1dac69a26b79c98409e9633a7fe4bd3))
- onServerPrefetch ([#3070](https://github.com/vuejs/vue-next/issues/3070)) ([349eb0f](https://github.com/vuejs/vue-next/commit/349eb0f0ad78f9cb491278eb4c7f9fe0c2e78b79))

### Performance Improvements

- only trigger `$attrs` update when it has actually changed ([5566d39](https://github.com/vuejs/vue-next/commit/5566d39d467ebdd4e4234bc97d62600ff01ea28e))
- **compiler:** skip unncessary checks when parsing end tag ([048ac29](https://github.com/vuejs/vue-next/commit/048ac299f35709b25ae1bc1efa67d2abc53dbc3b))
- avoid deopt for props/emits normalization when global mixins are used ([51d2be2](https://github.com/vuejs/vue-next/commit/51d2be20386d4dc59006d31a1cc96676871027ce))

### Deprecations

- `app.config.isCustomElement` has been deprecated and should be now nested under `app.config.compilerOptions`. [[Docs](https://v3.vuejs.org/api/application-config.html#compileroptions)]
- `delimiters` component option has been deprecated and should now be nested under the `compilerOptions` component option. [[Docs](https://v3.vuejs.org/api/options-misc.html#compileroptions)]
- `v-is` has been deprecated in favor of `is="vue:xxx"` [[Docs](https://v3.vuejs.org/api/special-attributes.html#is)]

### Minor Breaking Changes

- `this.$props` and the `props` object passed to `setup()` now always contain all the keys for declared props, even for props that are absent ([4fe4de0](https://github.com/vuejs/vue-next/commit/4fe4de0a49ffc2461b0394e74674af38ff5e2a20)). This has always been the behavior in Vue 2 and is therefore considered a fix (see reasoning in [#3288](https://github.com/vuejs/vue-next/issues/3288)). However, this could break Vue 3 code that relied on the keys for prop absence checks. The workaround is to use a Symbol default value for props that need absence checks:

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

- `optionMergeStrategies` functions no longer receive
  the component instance as the 3rd argument. The argument was technically
  internal in Vue 2 and only used for generating warnings, and should not
  be needed in userland code. This removal enables much more efficient
  caching of option merging.

### Bug Fixes

* **compat:** revert private properties on $options in comapt mode ([ad844cf](https://github.com/vuejs/vue-next/commit/ad844cf1e767137a713f715779969ffb94207c7a)), closes [#3883](https://github.com/vuejs/vue-next/issues/3883)
* **runtime-core:** fix fragment update inside de-opt slots ([5bce2ae](https://github.com/vuejs/vue-next/commit/5bce2ae723d43f23ccfac961f29b80fc870fba1f)), closes [#3881](https://github.com/vuejs/vue-next/issues/3881)
- **compat:** fix deep data merge with extended constructor ([c7efb96](https://github.com/vuejs/vue-next/commit/c7efb967ca5ab42ea2713331b8e53ae5c2746a78)), closes [#3852](https://github.com/vuejs/vue-next/issues/3852)
- **compiler-sfc:** fix style injection when using normal script + setup ([8b94464](https://github.com/vuejs/vue-next/commit/8b94464a3b9759a7a98c23efeafc7a9359c9807d)), closes [#3688](https://github.com/vuejs/vue-next/issues/3688)
- **compiler-sfc:** fix template expression assignment codegen for script setup let refs ([#3626](https://github.com/vuejs/vue-next/issues/3626)) ([2c7bd42](https://github.com/vuejs/vue-next/commit/2c7bd428011e027efa8f66487d2269c8dd79a2b0)), closes [#3625](https://github.com/vuejs/vue-next/issues/3625)
- **runtime-core:** align option merge behavior with Vue 2 ([e2ca67b](https://github.com/vuejs/vue-next/commit/e2ca67b59a4de57a9bce8d3394263ba493a35a39)), closes [#3566](https://github.com/vuejs/vue-next/issues/3566) [#2791](https://github.com/vuejs/vue-next/issues/2791)
- **runtime-dom/v-model:** only set selectedIndex when the value changes ([#3845](https://github.com/vuejs/vue-next/issues/3845)) ([ecd97ee](https://github.com/vuejs/vue-next/commit/ecd97ee6e465ec5c841d58d96833fece4e899785))
- **suspense:** fix suspense regression for errored template component ([44996d1](https://github.com/vuejs/vue-next/commit/44996d1a0a2de1bc6b3abfac6b2b8b3c969d4e01)), closes [#3857](https://github.com/vuejs/vue-next/issues/3857)
- **watch:** avoid traversing objects that are marked non-reactive ([9acc9a1](https://github.com/vuejs/vue-next/commit/9acc9a1fa838bdcdf673d2f7cc3f996b2b69ffbc))
- **compiler-core:** improve the isMemberExpression function ([#3675](https://github.com/vuejs/vue-next/issues/3675)) ([9b2e894](https://github.com/vuejs/vue-next/commit/9b2e8940176b3b75fa052b3c3e9eeaabc46a95e6))
- **compiler-dom:** fix in-browser attribute value decoding w/ html tags ([6690372](https://github.com/vuejs/vue-next/commit/669037277b03bb8e67f517faf2811a8668ea86d6)), closes [#3001](https://github.com/vuejs/vue-next/issues/3001)
- **compiler-sfc:** correctly remove parens used for wrapping ([#3582](https://github.com/vuejs/vue-next/issues/3582)) ([6bfb50a](https://github.com/vuejs/vue-next/commit/6bfb50aff98038a1f854ce24733f545eec2ee796)), closes [#3581](https://github.com/vuejs/vue-next/issues/3581)
- **reactivity:** ensure computed always expose value ([03a7a73](https://github.com/vuejs/vue-next/commit/03a7a73148a9e210a7889c7a2ecf925338735c70)), closes [#3099](https://github.com/vuejs/vue-next/issues/3099) [#910](https://github.com/vuejs/vue-next/issues/910)
- **runtime-core:** fix cases of reused children arrays in render functions ([#3670](https://github.com/vuejs/vue-next/issues/3670)) ([a641eb2](https://github.com/vuejs/vue-next/commit/a641eb201fe51620d50884b988f6fefc3e21a20b)), closes [#3666](https://github.com/vuejs/vue-next/issues/3666)
- **runtime-core:** fix resolving inheritAttrs from mixins ([#3742](https://github.com/vuejs/vue-next/issues/3742)) ([d6607c9](https://github.com/vuejs/vue-next/commit/d6607c9864376fbe17899f3d35fc7b097670a1b1)), closes [#3741](https://github.com/vuejs/vue-next/issues/3741)
- **runtime-core:** should disable tracking inside directive lifecycle hooks ([#3699](https://github.com/vuejs/vue-next/issues/3699)) ([ff50e8d](https://github.com/vuejs/vue-next/commit/ff50e8d78c033252c4ce7ffddb8069b3ddae5936))
- **runtime-core:** stricter compat root mount check ([32e2133](https://github.com/vuejs/vue-next/commit/32e21333dd1197a978cf42802729b2133bda5a0b))
- **runtime-dom:** should remove attribute when binding `null` to `value` ([#3564](https://github.com/vuejs/vue-next/issues/3564)) ([e3f5dcb](https://github.com/vuejs/vue-next/commit/e3f5dcb99bf42fed48d995438e459203dc3f6ed0))
- **suspense:** fix suspense patching in optimized mode ([9f24195](https://github.com/vuejs/vue-next/commit/9f24195d2ce24184ccdc5020793dd9423f0d3148)), closes [#3828](https://github.com/vuejs/vue-next/issues/3828)
- **transition:** fix higher order transition components with merged listeners ([071986a](https://github.com/vuejs/vue-next/commit/071986a2c6459fd99b91a48793a9ab6d6618b52d)), closes [#3227](https://github.com/vuejs/vue-next/issues/3227)
- **keep-alive:** include/exclude should work with async component ([#3531](https://github.com/vuejs/vue-next/issues/3531)) ([9e3708c](https://github.com/vuejs/vue-next/commit/9e3708ca754c0ecd66dbb45984f8d103772bd55c)), closes [#3529](https://github.com/vuejs/vue-next/issues/3529)
- **runtime-core:** properly check forwarded slots type ([#3781](https://github.com/vuejs/vue-next/issues/3781)) ([e8ddf86](https://github.com/vuejs/vue-next/commit/e8ddf8608021785c7b1b6f4211c633b40f26ddfc)), closes [#3779](https://github.com/vuejs/vue-next/issues/3779)
- **runtime-core:** should not track dynamic children when the user calls a compiled slot inside template expression ([#3554](https://github.com/vuejs/vue-next/issues/3554)) ([2010607](https://github.com/vuejs/vue-next/commit/201060717d4498b4b7933bf8a8513866ab9347e4)), closes [#3548](https://github.com/vuejs/vue-next/issues/3548) [#3569](https://github.com/vuejs/vue-next/issues/3569)
- **runtime-core/teleport:** ensure the nested teleport can be unmounted correctly ([#3629](https://github.com/vuejs/vue-next/issues/3629)) ([4e3f82f](https://github.com/vuejs/vue-next/commit/4e3f82f6835472650741896e19fbdc116d86d1eb)), closes [#3623](https://github.com/vuejs/vue-next/issues/3623)
- **scheduler:** handle preFlush cb queued inside postFlush cb ([b57e995](https://github.com/vuejs/vue-next/commit/b57e995edd29eff685aeaf40712e0e029073d1cb)), closes [#3806](https://github.com/vuejs/vue-next/issues/3806)
- **ssr:** handle hydrated async component unmounted before resolve ([b46a4dc](https://github.com/vuejs/vue-next/commit/b46a4dccf656280f9905e1bdc47022cb01c062c3)), closes [#3787](https://github.com/vuejs/vue-next/issues/3787)
- **watch:** should not leak this context to setup watch getters ([1526f94](https://github.com/vuejs/vue-next/commit/1526f94edf023899490d7c58afcf36b051e25b6c)), closes [#3603](https://github.com/vuejs/vue-next/issues/3603)
- **compat:** avoid accidentally delete the modelValue prop ([#3772](https://github.com/vuejs/vue-next/issues/3772)) ([4f17be7](https://github.com/vuejs/vue-next/commit/4f17be7b1ce4872ded085a36b95c1897d8c1f299))
- **compat:** enum coercion warning ([#3755](https://github.com/vuejs/vue-next/issues/3755)) ([f01aadf](https://github.com/vuejs/vue-next/commit/f01aadf2a16a7bef422eb039d7b157bef9ad32fc))
- **compiler-core:** fix whitespace management for slots with whitespace: 'preserve' ([#3767](https://github.com/vuejs/vue-next/issues/3767)) ([47da921](https://github.com/vuejs/vue-next/commit/47da92146c9fb3fa6b1e250e064ca49b74d815e4)), closes [#3766](https://github.com/vuejs/vue-next/issues/3766)
- **compiler-dom:** comments in the v-if branchs should be ignored when used in Transition ([#3622](https://github.com/vuejs/vue-next/issues/3622)) ([7c74feb](https://github.com/vuejs/vue-next/commit/7c74feb3dc6beae7ff3ad22193be3b5a0f4d8aac)), closes [#3619](https://github.com/vuejs/vue-next/issues/3619)
- **compiler-sfc:** support tsx in setup script ([#3825](https://github.com/vuejs/vue-next/issues/3825)) ([01e8ba8](https://github.com/vuejs/vue-next/commit/01e8ba8f873afe3857a23fb68b44fdc057e31781)), closes [#3808](https://github.com/vuejs/vue-next/issues/3808)
- **compiler-ssr:** disable hoisting in compiler-ssr ([3ef1fcc](https://github.com/vuejs/vue-next/commit/3ef1fcc8590da186664197a0a82e7856011c1693)), closes [#3536](https://github.com/vuejs/vue-next/issues/3536)
- **devtools:** send update to component owning the slot ([1355ee2](https://github.com/vuejs/vue-next/commit/1355ee27a65d466bfe8f3a7ba99aa2213e25bc50))
- **runtime-core:** avoid double-setting props when casting ([0255be2](https://github.com/vuejs/vue-next/commit/0255be2f4b3581bfdf4af9368dcd6c1a27a5ee03)), closes [#3371](https://github.com/vuejs/vue-next/issues/3371) [#3384](https://github.com/vuejs/vue-next/issues/3384)
- **runtime-core:** avoid the proxy object polluting the slots of the internal instance ([#3698](https://github.com/vuejs/vue-next/issues/3698)) ([4ce0df6](https://github.com/vuejs/vue-next/commit/4ce0df6ef1a31ee45402e61e01777e3836b2c223)), closes [#3695](https://github.com/vuejs/vue-next/issues/3695)
- **types:** declared prop keys should always exist in `props` argument ([#3726](https://github.com/vuejs/vue-next/issues/3726)) ([9b160b9](https://github.com/vuejs/vue-next/commit/9b160b940555abb6b6ce722fddbd9649ee196f7b))
- **types/reactivity:** error TS4058 caused by `RefSymbol` ([#2548](https://github.com/vuejs/vue-next/issues/2548)) ([90aa835](https://github.com/vuejs/vue-next/commit/90aa8358129f25826bfc4c234325c1442aef8d55))
- **compat:** correctly merge lifecycle hooks when using Vue.extend ([#3762](https://github.com/vuejs/vue-next/issues/3762)) ([2bfb8b5](https://github.com/vuejs/vue-next/commit/2bfb8b574d39a20a0e4da2ff4f2c007680ee2038)), closes [#3761](https://github.com/vuejs/vue-next/issues/3761)
- **compiler-core:** bail out to array children when the element has custom directives + only one text child node ([#3757](https://github.com/vuejs/vue-next/issues/3757)) ([a56ab14](https://github.com/vuejs/vue-next/commit/a56ab148fd1f2702e699d31cdc854800c8283fde))
- **compat:** handle and warn config.optionMergeStrategies ([94e69fd](https://github.com/vuejs/vue-next/commit/94e69fd3896214da6ff8b9fb09ad942c598053c7))
- **compiler-core:** preserve comment content in production when comments option is enabled ([e486254](https://github.com/vuejs/vue-next/commit/e4862544310a4187dfc8b3a49944700888bb60e3))
- **hmr:** don't remove \_\_file key from component type ([9db3cbb](https://github.com/vuejs/vue-next/commit/9db3cbbfc1a072675a8d0e53edf3869af115dc60))
- **hydration:** fix update before async component is hydrated ([#3563](https://github.com/vuejs/vue-next/issues/3563)) ([c8d9683](https://github.com/vuejs/vue-next/commit/c8d96837b871d7ad34cd73b4669338be5fdd59fd)), closes [#3560](https://github.com/vuejs/vue-next/issues/3560)
- **reactivity:** fix tracking for readonly + reactive Map ([#3604](https://github.com/vuejs/vue-next/issues/3604)) ([5036c51](https://github.com/vuejs/vue-next/commit/5036c51cb78435c145ffea5e82cd620d0d056ff7)), closes [#3602](https://github.com/vuejs/vue-next/issues/3602)
- **runtime-core:** ensure declare prop keys are always present ([4fe4de0](https://github.com/vuejs/vue-next/commit/4fe4de0a49ffc2461b0394e74674af38ff5e2a20)), closes [#3288](https://github.com/vuejs/vue-next/issues/3288)
- **runtime-core:** watching multiple sources: computed ([#3066](https://github.com/vuejs/vue-next/issues/3066)) ([e7300eb](https://github.com/vuejs/vue-next/commit/e7300eb47960a153311d568d7976ac5256eb6297)), closes [#3068](https://github.com/vuejs/vue-next/issues/3068)
- **Teleport:** avoid changing the reference of vnode.dynamicChildren ([#3642](https://github.com/vuejs/vue-next/issues/3642)) ([43f7815](https://github.com/vuejs/vue-next/commit/43f78151bfdff2103a9be25e66e3f3be68d03a08)), closes [#3641](https://github.com/vuejs/vue-next/issues/3641)
- **watch:** avoid traversing non-plain objects ([62b8f4a](https://github.com/vuejs/vue-next/commit/62b8f4a39ca56b48a8c8fdf7e200cb80735e16ae))
- **watch:** this.$watch should support watching keypath ([870f2a7](https://github.com/vuejs/vue-next/commit/870f2a7ba35245fd8c008d2ff666ea130a7e4704))
# Vue 3.0 发布

今天，我们非常自豪地宣布 Vue.js 3.0 "One Piece" 发布。本次主版本更新包含性能的改进，更小的 bundle 体积，对 TypeScript 更好的支持，用于处理大规模用例的全新 API，以及为框架未来的长期迭代奠定了夯实的基础。

3.0 的发布意味着两年多以来的努力，其中包含 [30+ RFC](https://github.com/vuejs/rfcs/tree/master/active-rfcs)，2600+ commits，[99 位贡献者](https://github.com/vuejs/vue-next/graphs/contributors)所提交的 [628 个 PR](https://github.com/vuejs/vue-next/pulls?q=is%3Apr+is%3Amerged+-author%3Aapp%2Fdependabot-preview++sort%3Aupdated-desc)，还有许多除核心仓库以外的开发及文档编写工作。在此对 Vue 团队成员表示最深切的感谢，感谢贡献者们提交的 PR，感谢[赞助商和 sponsors](https://github.com/vuejs/vue/blob/dev/BACKERS.md) 的资金支持，感谢广大社区成员参与预发布版本的设计与谈论，并提供反馈。Vue 是一个独立项目，为社区建立，也由社区维护，没有大家的鼎立支持，Vue 3.0 是不可能实现的。

## 进一步推进 "渐进式框架" 感念

Vue 从开始就有一个简单的使命：成为任何人都能快速学习且平易近人的框架。随着我们用户群体的增长，框架的应用范围也在不断扩大，以适应不断迭代的需求。随着时间的流逝，它演变成了 "渐进式框架"：一个可以逐步学习和采用的框架，同时为用户提供持续支持，以应对越来越多的苛刻场景。

时至今日，我们在全球拥有 130 多万的用户，我们看到 Vue 被应用于各种不同的场景中的可能，从在传统的服务端渲染页面之上添加交互，到拥有数百个组件的完整单页应用。Vue 3.0 将这种灵活性进一步提升。

### 分离内部模块

Vue 3.0 核心仍然通过一个简单的 `<script>` 标签来使用，但其内部结构已经被重写，并[解耦成一个个模块的集合](https://github.com/vuejs/vue-next/tree/master/packages)。新的架构下为源码提供了更好的可维护性，并允许终端用户通过 tree-shaking 的形式将减少一半的运行时体积。

这些模块还暴露了底层 API，解锁了许多高级用法：

* 编译器支持自定义 AST 转换，用于在构建时自定义（如，[在构建时进行 i18n 操作](https://github.com/intlify/vue-i18n-extensions)）
* 核心运行时提供了一系列 API，用于针对不同渲染目标（如 [native moile](https://github.com/rigor789/nativescript-vue-next)、[WebGL](https://github.com/Planning-nl/vugel) 或[终端](https://github.com/ycmjason/vuminal)）的自定义容器。默认的 DOM 渲染器也使用这系列 API 构建。
* [`@vue/reactivity` 模块](https://github.com/vuejs/vue-next/tree/master/packages/reactivity)导出的功能，可以直接访问 Vue 的响应式系统。并且可以作为一个独立的包进行使用。它可以与其他模块解决方案配对使用（如 [lit-html](https://github.com/yyx990803/vue-lit)），甚至是在非 UI 场景中使用。

### 用于解决规模问题的全新 API

在 Vue 3.0 中，基于对象的 2.x API 基本没有变化，并引入了 [Composition API](https://v3.vuejs.org/guide/composition-api-introduction.html) — 一套全新的 API，旨在解决 Vue 在大规模应用场景中的痛点。Composition API 构建于响应式 API 之上，实现了类似于 React hook 的逻辑组成与复用，相较于 2.x 基于对象的 API 方式来说，拥有更加灵活的代码组织模式，以及更为可靠的类型推断能力。

Composition API 同时还提供了适用于 Vue 2.x 的版本，可通过 [`@vue/composition-api`](https://github.com/vuejs/composition-api) 插件来在项目中使用，目前已有适用于 Vue 2 和 Vue 3 的 Composition API 实用的工具库（如 [`vueuse`](https://github.com/antfu/vueuse)，[`vue-composable`](https://github.com/pikax/vue-composable)）。

### 性能提升

Vue 3 与 Vue 2 相比，在 bundle 包大小方面（tree-shaking 减少了 41% 的体积），初始渲染速度方面（快了 55%），更新速度方面（快了 133%）以及内存占用方面（减少了 54%）都有着[显著的性能提升](https://docs.google.com/spreadsheets/d/1VJFx-kQ4KjJmnpDXIEaig-cVAAJtpIGLZNbv3Lr4CR0/edit?usp=sharing)。

在 3.0 中，我们采取了 “编译信息虚拟 DOM” 的方式：针对模板编译器进行了优化，并生成渲染函数代码，以提升静态内容的渲染性能，为绑定类型留下运行时提示，最为重要的是，模板内部的动态节点进行了扁平化处理，以减少运行时遍历的开销。因此，用户可以获得两全其美的效果，从模板中获得编译器优化后的性能，或在需要时通过手动渲染函数直接控制。

### 改进与 TypeScript 的兼容

Vue 3 的代码库完全采用 TypeScript 编写，自动生成、测试并构建类型声明，因此它们总为最新。Composition API 可以很好的进行类型推断。Vetur，官方推出的 VSCode 插件，现已支持对模板表达式和 props 的类型检查，同时，[Vue 3 已全面支持 TSX](https://github.com/vuejs/vue-next/blob/master/test-dts/defineComponent.test-d.tsx)。

### 实验特性

我们为单文件组件提出了[两个新特性](https://github.com/vuejs/rfcs/pull/182)（SFC，又称为 `.vue` 文件）:

* [`<script setup>`：在 SFC 内使用 Composition API 的语法糖](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md)
* [`<style vars>`：在 SFC 中支持将状态作为 CSS 变量注入到样式中](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md)

这些特性在 3.0 中均已实现且可用，但提供这些特性的目的只是为了收集反馈。在最终合并到 RFC 之前，这些特性仍是实验性的。

我们还实现了一个目前尚未编写文档的 `<Suspense>` 组件，它允许在初始渲染或 branch 切换时，等待嵌套的异步依赖（异步组件或带有 `async setup()` 组件）。我们正在与 Nuxt.js 团队一起测试和迭代此功能（[Nuxt 3 正在开发中](https://nuxtjs.slides.com/atinux/state-of-nuxt-2020)），并可能会在 3.1 时完善它。

## 分阶段发布

Vue 3.0 的发布意味着本框架的整体准备就绪。虽然框架的一些子项目还需进一步完善才能达到稳定状态（特别是 router 以及 Vuex 与 devtools 的集成），但我们相信现在已经可以使用 Vue 3 开启全新的项目。同时，我们也鼓励库作者开始升级项目以支持 Vue 3。

查阅 [Vue 3 的库指南](https://v3.vuejs.org/guide/migration/introduction.html#supporting-libraries)，以了解框架所有子项目的详细进展。

### 迁移与 IE11 的支持

由于时间限制，我们推迟了迁移构建（v3 兼容 v2 的构建，以及迁移警告）和兼容 IE11 的计划，并打算在 2020 年 Q4 集中进行。因此，计划迁移现有 v2 应用或需要兼容 IE11 的用户，目前应注意限制。

### 下一阶段工作

对于发布后的一段时间内，我们将重点关注：

* 迁移构建
* 支持 IE11
* Router 以及 Vuex 与 devtools 的集成
* 对 Vetur 中模板类型推断的进一步改进

目前，Vue 3 及其相关子项目的文档站、github 分支以及 npm 的 dist 标签都将保持在 `next` 状态。这意味着 `npm install vue` 仍会安装 2.x 版本，而通过 `npm install vue@next` 将会安装 v3 版本。我们计划在 2020 年底之前将所有文档链接，分支以及 npm 标签全部切为 3.0。

与此同时，我们已开始规划 2.7 版本，这将会是 2.x 的最后一个小版本。2.7 将与 v3 进行兼容改进，并对使用 v3 中已删除/更改的 API 发出警告，以更好的帮助 3.0 的迁移升级工作。我们计划在 2021 年 Q1 进行 2.7 的工作，发布后将直接成为 LTS 版本，维护周期为 18 个月。

## 试用

欲了解更多关于 Vue 3.0 的信息，请查阅[全新的文档站](https://v3.vuejs.org/)，如果你是 2.x 的老用户，请直接查看[迁移指南章节](https://v3.vuejs.org/guide/migration/introduction.html)。

# Vue 3 is now in RC!

非常高兴的宣布 Vue 3.0 已经进入候选（Release Candidate）阶段！

进入 RC 阶段，意味着 Vue 3 的核心 API 及实现均已稳定。原则上，我们不希望在正式版发布之前再引入新的特性或重大更新（breaking changes）。许多官方维护的 framework 已基本支持 v3。请参阅[此链接](https://github.com/vuejs/vue-next#status-of-the-rest-of-the-framework)了解最新更改。

## 全新的文档

Vue 文档团队已经将文档更新至 v3，可直接访问 [v3.vuejs.org](https://v3.vuejs.org)！这是一项艰巨的工作，要感谢文档团队的辛勤付出： [@NataliaTepluhina](https://github.com/NataliaTepluhina)，[@bencodezen](https://github.com/bencodezen)，[@phanan](https://github.com/phanan) 以及 [@sdras](https://github.com/sdras)。新文档经过了精心设计，以涵盖 v2 和 v3 之间的差异，可以直接运行在 VuePress 上，并且改进了代码示例，可以内联编辑。

有关新特性和更改的快速概览，请参阅[迁移指南](https://v3.vuejs.org/guide/migration/introduction.html)。

请注意，新文档（尤其是『迁移指南』）仍在开发中，我们将会在整个 RC 阶段继续完善它，

## DevTools 初步支持 v3

由于 [@Akryum](https://github.com/Akryum) 出色工作，我们还发布了[初步支持 v3 的 Vue DevTools](https://github.com/vuejs/vue-devtools/releases/tag/v6.0.0-beta.1) 的 Beta 版本。

通过对 devtool 的深度重构，现在已经可能很好地将其核心逻辑与对不同 Vue 版本的支持进行分离。此界面还拥有使用了 Tailwind CSS 实现的新外观。目前，仅支持了组件检查的功能 —— 但很快就会支持其他功能。

~Vue DevTools 的 beta 版本仍在 Chrome 的网上应用商店进行审核，你可以通过访问上方链接中的说明在本地下载并进行安装。~

## 试用

如果你想试用 Vue 3，可以通过以下几种方式进行：

在 [Codepen](https://codepen.io/team/Vue/pen/KKpRVpx) 上试用。

使用 [Vite](https://github.com/vitejs/vite) 启动一个项目:

```bash
npm init vite-app hello-vue3
```

Vite 在单文件组件（SFC）中默认支持了 [`<script setup>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md?rgh-link-date=2020-07-17T20%3A58%3A23Z) 和 [`<style vars>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md?rgh-link-date=2020-07-17T20%3A58%3A23Z)。

我们有一个进行中的 PR，会在 [vue-cli 中针对 v3 的 first-class 进行支持](https://github.com/vuejs/vue-cli/pull/5637) —— 即将发布。

## 接下来的工作

目前尚未完成 RC 版本对 IE11 完整支持，因此会继续努力完成。

同时，我们会将工作的重心转向文档，迁移以及兼容性方面。我们目前的目标是为使用 v3 开发新项目提供完善的文档，并帮助相关库的作者升级其 package 以更好地支持 v3。文档团队将根据社区的反馈继续完善迁移指南和 v3 的文档。

将零散的应用程序从 v2 升级到 v3 进展可能会非常缓慢。我们将提供 codemods 和工具来帮助大家进行此类项目的迁移，但是大多数情况下，这取决于项目本身的依赖能以多快的速度升级至 v3。因此，升级前需评估风险和时间成本，再决定是否升级 —— Vue 2 会继续维护。我们计划在 3.0 发布后，设置一个过渡期，以通过兼容性插件将新特性反向移植到 v2 中。我们已经在 [@vue/composition-api](https://github.com/vuejs/composition-api) 中验证了此方法的可行性。

## 实验阶段特性

RC 版本中提供了一些特性，但已标记为实验性特性：

* `<Suspense>`
* [`<script setup>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md?rgh-link-date=2020-07-17T20%3A58%3A23Z)
* [`<style vars>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md?rgh-link-date=2020-07-17T20%3A58%3A23Z)

这些特性已发布，目的是收集大家在实际项目中使用情况的反馈，但是它们可能仍会进行重大更改/调整。并且它们会在 3.0 中保持实验状态，并最终成为 3.1 的一部分。

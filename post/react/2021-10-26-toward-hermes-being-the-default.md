---
title: Toward Hermes being the Default
authors: [huxpro]
tags: [announcement]
---

自 [2019 年我们发布 Hermes 以来](https://engineering.fb.com/2019/07/12/android/hermes/)，它在社区已经获得了越来越多的认可。比如，React Native 应用程序元框架团队 [Expo](https://expo.dev/) 就在最近宣布了对 Hermes 的[实验性](https://blog.expo.dev/expo-sdk-42-579aee2348b6) [支持](https://blog.expo.dev/expo-sdk-43-beta-is-now-available-47dc54a8d29f)，这一度是 [Expo 论坛上呼声最高的功能之一](https://expo.canny.io/feature-requests/p/enabling-hermes)。移动端数据库团队 [Realm](https://realm.io/) 也在近期对 Hermes 提供了 [alpha 支持](https://github.com/realm/realm-js/issues/3940)。本文将重点介绍过去两年以来，我们在推动 Hermes 成为 React Native *最佳* JavaScript 引擎方面取得的最令人兴奋的一些成果。展望未来，我们有信心通过进一步的改进，使 Hermes 成为 React Native 在所有平台上的默认 JavaScript 引擎。

<!--truncate-->

## 为 React Native 优化

Hermes 的核心特性是如何将编译工作提前进行（ahead-of-time），这意味着启用 Hermes 的 React Native 应用会携带预编译的优化后字节码，而非原始的 JavaScript 源代码。这极大地减少了用户启动产品所需的工作量。由 Facebook 以及社区应用的测量数据表明，启用 Hermes 往往能将产品的 TTI（全称 [Time-To-Interactive](https://web.dev/interactive/)，即可交互时间）指标减少近一半。

但我们并不想止步于此，我们一直在对 Hermes 进行全方位的改进，致力于让其在作为 React Native 专用 JavaScript 引擎方面更加出色。

### 为 Fabric 构建的全新 GC

随着 React Native 新架构中 [Fabric](https://github.com/react-native-community/discussions-and-proposals/issues/4) 渲染器的推出，它使得在 UI 线程中同步调用 JavaScript 成为可能。然而，这意味着如果 JavaScript 线程的执行时间过长，就会出现非常明显的 UI 掉帧，并且会阻塞用户的输入。React [Fiber](https://reactjs.org/docs/faq-internals.html#what-is-react-fiber) 启用的 [并发渲染](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html) 将通过把渲染工作分片来避免调度过长的 JavaScript 任务。但是，在 JavaScript 线程中还有一个非常常见的延迟来源，那就是在 JavaScript 引擎不得不 “停止一切” 以进行垃圾回收（GC）时。

之前 Hermes 中默认的垃圾回收器是 [GenGC](https://hermesengine.dev/docs/gengc/)，它是一款单线程的分代式垃圾回收器。新生代采用了典型的半区复制（semi-space copying）策略，而老生代则使用了标记整理（mark-compact）策略，使其特别擅于将未使用的内存返还给操作系统。但由于是单线程，GenGC 存在导致长时间 GC 暂停的缺陷。在类似安卓版 Facebook 这样复杂的应用程序上，我们观察到平均暂停时间为 200ms，p99 大概是 1.4s。在安卓版 Facebook 庞大且多样的用户群体中，甚至还曾达到 7s 之久。

为了解决这个问题，我们实现了一个全新且**高并发**的 GC，名为 [Hades](https://hermesengine.dev/docs/hades)。Hades 回收新生代的方式与 GenGC 完全一致，但它采用原始快照（SATB）式标记擦除（mark-sweep）回收器来管理老生代。它可以通过在后台线程中执行大部分工作，而不会阻塞引擎主线程执行 JavaScript 代码，来显著减少 GC 的暂停时间。**根据我们统计数据显示，Hades 在 64 位设备上 p99.9 的暂停时间仅为 48ms（比 GenGC 要快 34 倍！）**，并且在 32 位上 p99.9 的暂停时间约为 88ms（此时它会作为一个单线程**增量** CG 运行）。这些暂停时间的改进以整体吞吐量为代价，因为需要更昂贵的写屏障，更慢的 freelist 分配（相对于使用 bump pointer 分配），甚至还会额外增加堆的碎片化程度。但我们认为这都是正确的取舍，通过合并（coalescing）以及其他接下来会讨论到的内存优化机制，我们最终其实达到了更低的整体内存占用。

### 攻克性能痛点

App 的启动时间对于许多 App 的成功来说至关重要，我们希望能不断提升 React Native 的上限。对于在 Hermes 中实现的任何 JavaScript 功能，我们都会仔细监控它在生产环境对性能造成的影响，确保它们不会倒退任何指标。在 Facebook，我们目前正在 [Metro（React Native 使用的 bundler）中试验为 Hermes 提供一个专用的 Babel 转换配置文件](https://github.com/facebook/metro/blob/c9a6fd75937c56645ab9e4d88fa820e63e057cd6/packages/metro-react-native-babel-preset/src/configs/main.js#L42) 来用 Hermes 原生的 ESNext 实现替换掉十多个 Babel 转换。我们的内部数据显示 **TTI 有 18-25% 的提升**，同时 **整体字节码的大小也随之减少**，我们目测在开源环境也能有类似的改进效果。

除了启动性能外，我们还注意到内存占用也是 React Native 需要改进的痛点，特别是在 [VR](https://reactnative.dev/blog/2021/08/26/many-platform-vision#expanding-to-new-platforms) 场景下的内存占用。得益于我们作为 JavaScript 引擎所拥有的底层控制能力，我们能够从二进制层面提供诸多内存方面的优化：

1. 之前，所有的 JavaScript 值都会被表示为 64 位 NaN-boxing 编码的标记值，用以表示 64 位架构上的双精度浮点数和指针。但这在实践过程中非常浪费资源，因为大多数数字其实都是 SMI（小整数，全称 Small Integer），并且客户端应用的 JavaScript 堆一般也不会超过 4GiB。为了解决此问题，我们引入了全新的 32 位编码，其中 SMI 和指针都会被编码为 29 位（因为指针会以 8 字节对齐，我们可以假设底部 3 位都是 0），而其余的 JS 数字都会被装箱到堆中。**这个优化最终使得 JavaScript 堆大小整体减少了 30% 左右**。
2. 不同种类的 JavaScript 对象在 JavaScript 堆中被表示为不同种类的 GC 管理单元。通过对这些单元头文件的内存布局进行压榨，**我们能够再减少近 15% 的内存占用**。

我们对 Hermes 的一大关键举措是不实现 [JIT 编译器](https://en.wikipedia.org/wiki/Just-in-time_compilation)，因为我们坚信对于大多数 React Native 应用来说，额外的预热开销以及对二进制文件与内存占用的增加并不值得。多年以来，我们在解释器性能优化和编译器优化方面投入了大量精力，以使 Hermes 的吞吐量在 React Native 的负载风格上能与其他引擎不相伯仲。我们将继续通过专注于解决各方面性能瓶颈（解释器调度循环、堆栈布局、对象模型、GC等）来进一步提高吞吐量。敬请期待！

### 垂直领域整合先驱

<!-- alex ignore host -->

在 Facebook，我们倾向于把项目整合在一个大的 [monorepo](https://en.wikipedia.org/wiki/Monorepo) 中，所以引擎（Hermes）与宿主环境（React Native）是一起迭代得，这使得我们有很多空间去做垂直整合。举例来说：

- Hermes 遵循了 [Chrome DevTools 协议](https://chromedevtools.github.io/devtools-protocol/)，因此，它支持 [用 Chrome 调试器对设备上的 JavaScript 进行调试](https://reactnative.dev/docs/hermes#debugging-js-on-hermes-using-google-chromes-devtools)。它比传统的 “[远程 JS 调试](https://reactnative.dev/docs/debugging#chrome-developer-tools)”（使用应用内代理到桌面端 Chrome 中运行 JS）更好，因为它支持有同步的原生调用的场景，并且能保证与真机一致的运行时环境。Hermes 调试工具与 React DevTools，Metro，Inspector 等一并成为 [Flipper](https://reactnative.dev/blog/2020/03/26/version-0.62) 的一部分，为大家提供了一站式开发方案。
- 在 React Native 应用的初始化过程中分配的对象往往是长期存在的，并且不会遵循分代 GC 所利用的分代假说。因此，我们 [在 React Native 中配置 Hermes 时](https://github.com/facebook/react-native/blob/main/ReactAndroid/src/main/java/com/facebook/hermes/reactexecutor/OnLoad.cpp#L37-L42)，会将前 32MB 直接分配到老生代（称为 _pre-tenuring_），以避免触发 GC 暂停造成 TTI 的延迟。
- 新 React Native 架构在很大程度上是基于 [JSI (即 JavaScript Interface)](https://github.com/react-native-community/discussions-and-proposals/issues/91) 实现的，这是一个轻量级的通用 API，主要用于将 JavaScript 引擎嵌入到 C++ 程序中。介于我们的 JSI API 集成实现是由我们 JS 引擎团队自己维护得，所以我们有信心能提供最正确与性能最好的实现，而且是在 Facebook 的规模上实战检验过得。
- 让 JavaScript 并发原语（例如，[promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)）和平台并发原语（例如 [微任务（microtasks）](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)）语义正确同时兼具高性能，对于 React 并发渲染和 React Native 应用的未来显得至关重要。过去，React Native 中的 Promise 是基于非标准化的 [`setImmediate`](https://developer.mozilla.org/en-US/docs/Web/API/Window/setImmediate) API 实现的 [polyfill](https://github.com/facebook/react-native/blob/main/Libraries/Core/polyfillPromise.js#L37)。我们正在努力将 JS 引擎的原生 Promise 和微任务通过 JSI 实现，并在平台上引入 [`queueMicrotask`](https://developer.mozilla.org/en-US/docs/Web/API/queueMicrotask)，这是最近引入的 web 标准，以更好地支持现代异步 JavaScript 代码。

## 带动社区发展

Hermes 对 Facebook 来说足够好用。但是，我们的工作远不止于此，我们的终极目标是让整个社区能都够使用 Hermes，这样我们才能让整个生态一起向前，并真正发掘出 Hermes 的潜力。

### 扩展到新平台

Hermes 起初只为 Android 上的 React Native 开放了源码。从那以后，我们看到社区成员将 Hermes 的支持扩展到 [React Native 生态系统所能扩展到的诸多平台之上](https://reactnative.dev/blog/2021/08/26/many-platform-vision)。

在 React Native 0.64 中，由 [Callstack](https://callstack.com/) 牵头完成了将 [Hermes 引入 iOS 平台的工作](https://reactnative.dev/blog/2021/03/12/version-0.64)。他们编写了 [系列文章](https://callstack.com/blog/bringing-hermes-to-ios-in-react-native/) 并主持了 [播客](https://callstack.com/podcasts/react-native-0-64-with-hermes-for-ios-ep-5) 来介绍他们的实现过程。从他们提供的跑分结果来看，与 JSC 相比，Hermes **在 iOS 上能为 Mattermost （一个开源的 React Native 应用）稳定提供近 ~40% 的启动优化同时减少了近 ~18% 的内存占用**，而应用程序只增加了 2.4 MiB 开销。眼见为实，推荐你去看下 [原文](https://callstack.com/blog/hermes-performance-on-ios/)。

微软则一直在推进将 [Hermes 引入 React Native for Windows 和 React Native for macOS](https://microsoft.github.io/react-native-windows/docs/hermes) 中。[在微软 Build 2020 大会](https://youtu.be/QMFbrHZnvvw?t=389) 上，微软分享了 Hermes 的内存占用（[工作集](https://en.wikipedia.org/wiki/Working_set)）比 React Native for Windows 中的 Chakra 引擎低 13%。另外，在最近的一些测试跑分中，他们发现（使用 Hades GC 并且包含了上文提到的 SMI 与指针压缩优化的）Hermes v0.8 版本，**在内存占用上比其他引擎少近 30%-40%**。你可能已经猜到了，[Messenger 桌面端应用](https://www.messenger.com/desktop) 基于 React Native 构建的视频通话体验，也是跑在 Hermes 上得。

值得一提得是，所有 Oculus 上基于 React 技术构建的虚拟现实体验，包括 Oculus Home（一打开的主屏），也都是由 Hermes 在底下驱动得。

### 支持我们的社区

我们知道目前仍有不少问题阻碍了部分社区采用 Hermes，我们承诺将会补上这些坑。我们的目标是让 Hermes 功能足够齐全到可以满足并成为大多数 React Native 应用程序的选择。Hermes 的路线其实已经在被社区影响了，比如：

<!-- alex ignore just -->
<!-- alex ignore fellowship -->

- [`Proxy` 和 `Reflect`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Meta_programming) 最初未被 Hermes 实现，因为 Facebook 并不使用它们。我们曾担心添加 Proxy 会损害整个属性查询的性能（即便代码中没有用到）。但由于 [MobX](https://mobx.js.org/README.html) 和 [Immer](https://immerjs.github.io/immer/) 等库的流行，Proxy 很快成为了 Hermes [呼声最高的功能](https://github.com/facebook/hermes/issues/33)。经过慎重评估后，我们决定专门为了社区去实现 Proxy，而且我们最终找到了对性能代价极小的方式来实现。由于这是我们不使用的功能，所以我们只能依靠社区来证明其稳定性。我们在 [v4.0](https://github.com/facebook/hermes/issues/33#issuecomment-668374607) 和 [v0.5](https://github.com/facebook/hermes/issues/33#issuecomment-668374607) 中提供了可选（opt-in）Proxy 支持的 npm 包。并最终在 [v0.7 起默认启用 Proxy](https://github.com/facebook/hermes/releases/tag/v0.7.0)。
- [ECMAScript 国际化 API 规范（ECMA-402，也称为 `Intl`）](https://hermesengine.dev/docs/intl) 是 [呼声第二高的功能](https://github.com/facebook/hermes/issues/23)。`Intl` 是一组庞大的 API，通常需要实现包含 **6MB 大小** 的 [Unicode CLDR](https://cldr.unicode.org/index) 数据。这就是为什么类似于 [FormatJS (a.k.a. `react-intl`)](https://github.com/formatjs/formatjs) 的 polyfills 以及像社区为 [JSC 构建的国际版](https://github.com/react-native-community/jsc-android-buildscripts#international-variant) JS 引擎如此庞大的原因。为了避免大幅增加 Hermes 的二进制大小，我们决定通过直接访问与映射操作系统中所提供的 ICU 库的方式来实现，这种实现策略的代价是不同平台的行为可能会存在一些（轻微）差异。
  - Android 端的支持是由微软合作完成得。它几乎涵盖了从 ECMA-402 到 ES2020 的所有内容，**对体积的影响只有仅仅 3%（每个 ABI 约为 57-62K）**。我们在 [Twitter 上发起了投票](https://twitter.com/tmikov/status/1336442786694893568)，投票结果是强烈要求默认开启 `Intl`，因此，我们在 [v0.8](https://github.com/facebook/hermes/releases/tag/v0.8.0) 中对其进行了默认支持。
  - Facebook 赞助了 [Major League Hacking](https://mlh.io/) 的一个 [远程开源奖学金](https://news.mlh.io/welcoming-facebook-back-as-a-sponsor-of-the-2020-2021-mlh-fellowship-08-12-2020) 项目。去年，我们和学生一起推出了 [Hermes 采样分析器](https://reactnative.dev/docs/profile-hermes)。今年，我们的学生将会和 Hermes，React Native 以及 Callstack 的成员一起，添加 Hermes 在 iOS 上的 `Intl` 的支持，敬请期待！
- 我们很感谢各位帮助我们发现与解决那些影响社区使用问题的人。
  - 有的人帮助我们确定了关键性规范中的分歧，如 [ES2019](https://github.com/tc39/ecma262/pull/1340) 中添加了 [`Array.prototype.sort` 要求使用稳定排序的修订](https://github.com/facebook/hermes/issues/212)。我们已经修复了这个问题，并将在下个版本中发布。
  - 有些人发现，我们堆的默认大小限制过小，对于许多不熟悉如何自定义 Hermes GC 配置的用户来说，会造成 [不必要的 GC 压力](https://github.com/facebook/hermes/issues/295) 和 [内存溢出崩溃](https://github.com/facebook/hermes/issues/511)。因此，我们将默认堆大小从 512MiB 提升至 3GiB，这对大多数用户来说应该足够了。
  - 还有的人提出，我们特殊的 `Function.prototype.toString` 实现会 [导致一些使用不恰当功能检测的库性能下降](https://github.com/facebook/hermes/issues/471#issuecomment-820123463)，亦或是 [无法做到源码注入](https://github.com/facebook/hermes/issues/114#issuecomment-887106990)。这些需求帮助我们确定了我们对 Hermes 的立场是它应尽可能不妨碍开发者的工作，且尽可能支持事实标准和实践。

## 总结

综上所述，我们的愿景是让 Hermes 做好成为所有 React Native 平台默认 JavaScript 引擎的准备，而且我们已经在朝这个方向努力了。我们希望充分听取大家来自各方各面的反馈意见。

让整个生态都能够顺利迁移对我们来说非常重要。我们鼓励大家试用 Hermes，并根据情况在 [GitHub 仓库](https://github.com/facebook/hermes) 中提交 issue，让我们知道你的使用反馈、遇到哪些问题与不兼容性、以及需要什么功能等等。

## 致谢

衷心的感谢 Hermes 团队、React Native 团队以及 React Native 社区的众多贡献者们，感谢他们为改进 Hermes 做出的贡献。

<!-- alex ignore White -->

我个人还想特此感谢一下（按字母排序）Eli White，Luna Wei，Neil Dhar，Tim Yung，Tzvetan Mikov 以及其他同事在我写作期间提供的帮助。

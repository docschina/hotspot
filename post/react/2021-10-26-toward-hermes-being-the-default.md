---
title: Toward Hermes being the Default
authors: [huxpro]
tags: [announcement]
---

自 [2019 年我们发布 Hermes 以来](https://engineering.fb.com/2019/07/12/android/hermes/)，它在社区已经获得了广泛认可。比如，由 [Expo](https://expo.dev/) 维护的 React Native 应用程序元框架，最近宣布对 Hermes 进行了 [实验性](https://blog.expo.dev/expo-sdk-42-579aee2348b6) [支持](https://blog.expo.dev/expo-sdk-43-beta-is-now-available-47dc54a8d29f)，因为这是 [Expo 最需要的功能之一](https://expo.canny.io/feature-requests/p/enabling-hermes)。[Realm](https://realm.io/) 团队也在近期对 Hermes 提供了 [alpha 支持](https://github.com/realm/realm-js/issues/3940)。本文旨在强调过去两年中我们所取得的一些令人兴奋的成果，以推动 Hermes 成为 React Native 的**最佳** JavaScript 引擎。展望未来，我们有信心通过进一步的改进，使 Hermes 成为 React Native 在所有平台上的默认 JavaScript 引擎。

<!--truncate-->

## 与 React Native 的相关优化

Hermes 的核心特性是如何将编译工作提前进行，这意味着启用 Hermes 的 React Native 应用会携带预编译的优化字节码，而非普通的 JavaScript 源代码。这极大地减少了用户启动产品所需的工作量。由 Facebook 以及社区应用的测量数据表明，启用 Hermes 往往能将产品的 TTI（也称为 [Time-To-Interactive](https://web.dev/interactive/)）指标减少近一半。

这意味着，我们一直在努力改善 Hermes 的各个方面，以使其作为专属于 React Native 的 JavaScript 引擎，并变得越来越好。

### 为 Fabric 构建一个全新的 GC

随着 React Native 架构中 [Fabric](https://github.com/react-native-community/discussions-and-proposals/issues/4) 渲染器的推出，它使得在 UI 线程中同步调用 JavaScript 成为可能。然而，这意味着如果 JavaScript 线程的执行时间过长，就会出现非常明显的 UI 掉帧，并且会阻塞用户的输入。React [Fiber](https://reactjs.org/docs/faq-internals.html#what-is-react-fiber) 启用的 [并发渲染](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html) 将通过把渲染工作分片来避免调度过长的 JavaScript 任务。但是，在 JavaScript 线程中还有一个非常常见的阻塞源，那就是在 JavaScript 引擎不得不 “停止一切” 以进行垃圾回收（GC）时。

之前 Hermes 中默认的垃圾回收器是 [GenGC](https://hermesengine.dev/docs/gengc/)，它是一款单线程的生代垃圾回收器。新生代采用了典型的半区复制策略，而老生代则使用了标记整理策略，使其能将未使用的内存返还给操作系统。但由于是单线程，GenGC 存在导致长时间 GC 暂停的缺陷。在类似安卓版 Facebook 这样复杂的应用程序上，我们观察到平均暂停时间为 200ms，p99 大概是 1.4s。在安卓版 Facebook 庞大且多样的用户群体中，甚至还曾达到 7s 之久。

为了解决这个问题，我们实现了一个全新且**高并发**的 GC，名为 [Hades](https://hermesengine.dev/docs/hades)。Hades 回收新生代的方式与 GenGC 完全一致，但它采用原始快照（SATB）式标记擦除回收器来管理老生代。它可以通过在后台线程中执行大部分工作，而不会阻塞引擎主线程执行 JavaScript 代码，来显著减少 GC 的暂停时间。**根据我们统计数据显示，Hades 在 64 位设备上 p99.9 的暂停时间仅为 48ms（比 GenGC 要快 34 倍！）**，并且在 32 位上 p99.9 的暂停时间约为 88ms（此时它会作为一个单线程**增量** CG 运行）。这些暂停时间的改进以整体吞吐量为代价，因为需要更昂贵的写屏障，更慢的 freelist 分配（与碰撞指针相反），甚至还会额外增加堆碎片。但这是我们权衡过后的结果，而且我们能够通过合并和额外的内存优化，来降低整体的内存消耗，接下来讲讲我们的优化思路。

### 解决性能上的痛点

App 的启动时间对于许多 App 的成功来说至关重要，我们在不断提升 React Native 的边界。我们在 Hermes 中实现的任何 JavaScript 功能，我们都会仔细监控它对生产性能造成的影响，并确保它们不会回归指标。在 Facebook，目前我们正在试验 [Metro 中 Hermes 的专用 Babel 转换配置文件](https://github.com/facebook/metro/blob/c9a6fd75937c56645ab9e4d88fa820e63e057cd6/packages/metro-react-native-babel-preset/src/configs/main.js#L42)，采用 Hermes 内置的 ESNext 实现替换十几个 Babel 的转换。初步数据显示 **TTI 有 18-25% 的提升**，同时 **整体字节码的大小也随之减少**，我们希望最终效果与 OSS 类似。

除了启动性能外，我们还注意到内存占用也是 React Native 需要改进的痛点，特别是在 [VR](https://reactnative.dev/blog/2021/08/26/many-platform-vision#expanding-to-new-platforms) 场景下的内存占用。得益于我们作为 JavaScript 引擎，能够进行底层控制，因此我们能压缩位和字节来提供更多内存方面的优化：

1. 之前，所有的 JavaScript 值都会被表示为 64 位 NaN-boxing 编码的标签值，用以表示 64 位架构上的双精度浮点数和指针。但这在实践过程中非常浪费资源，因为大多数情况下，数字均为短整数（SMI），并且客户端应用的 JavaScript 堆一般不会超过 4GiB，为了解决此问题，我们引入了全新的 32 位编码，其中 SMI 和指针都被编码为 29 位（因为指针会以 8 字节对齐，我们可以假设底部 3 位都是 0），其余的 JS 数字都会被装箱到堆中。**这可以使 JavaScript 的堆大小减少近 ~30%**。
2. 不同种类的 JavaScript 对象在 JavaScript 堆中被表示为不同种类的 GC 管理单元。通过进一步优化这些单元头文件的内存布局，**我们能够再减少近 15% 的内存占用**。

我们对 Hermes 的一大关键举措是不实现 [JIT 编译器](https://en.wikipedia.org/wiki/Just-in-time_compilation)，因为我们坚信对于大多数 React Native 应用来说，额外的预编译、二进制以及内存占用实际上并不值得。多年以来，我们在解释器性能优化和编译器优化方面投入了大量精力，以使 Hermes 的吞吐量在 React Native 负载方面与其他引擎更有竞争力。我们将继续通过专注于解决各方面性能瓶颈（解释器调度循环、堆栈布局、对象模型、GC等）来提高吞吐量。预计在新版本发布时会公布更多的数字，敬请期待！

### 垂直领域整合先驱

<!-- alex ignore host -->

在 Facebook，我们倾向于把项目整合在一个大的 [monorepo](https://en.wikipedia.org/wiki/Monorepo) 中。通过让引擎（Hermes）和主程序（React Native）紧密结合，这使得我们进行垂直整合有了更多可能。简单举几个例子：

- Hermes 遵循了 [Chrome DevTools 协议](https://chromedevtools.github.io/devtools-protocol/)，因此，它支持 [用 Chrome 调试器对设备上的 JavaScript 进行调试](https://reactnative.dev/docs/hermes#debugging-js-on-hermes-using-google-chromes-devtools)。它比传统的 “[远程 JS 调试](https://reactnative.dev/docs/debugging#chrome-developer-tools)”（使用应用内代理到桌面端 Chrome 中运行 JS）更好，因为它支持原生同步调用，并且能保证真机一致的运行时环境。Hermes 调试工具与 React DevTools，Metro，Inspector 等一并成为 [Flipper](https://reactnative.dev/blog/2020/03/26/version-0.62) 的一部分，为大家提供了一站式开发方案。
- 在 React Native 应用的初始化过程中分配的对象往往是长期存在的，并且不会遵循生代 GC 所利用的生代假说。因此，我们 [在 React Native 中配置了 Hermes](https://github.com/facebook/react-native/blob/main/ReactAndroid/src/main/java/com/facebook/hermes/reactexecutor/OnLoad.cpp#L37-L42)，将前 32MB 直接分配到老生代（称为 _pre-tenuring_），以避免触发 GC 暂停和延迟 TTI。
- 全新的 React Native 架构在很大程度上是基于 [JSI (or JavaScript Interface)](https://github.com/react-native-community/discussions-and-proposals/issues/91) 实现的，这是一个轻量级的通用 API，主要用于将 JavaScript 引擎嵌入到 C++ 程序中。我们非常有信心提供可靠、高性能且经过 Facebook 实战检验的最佳集成给大家，因为维护 JS 引擎的团队也同时维护 JSI API。
- 让 JavaScript 并发原语（例如，[promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)）和平台并发原语（例如 [microtasks](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)）语义正确同时兼具高性能，对于 React 并发渲染和 React Native 应用的未来显得至关重要。过去，React Native 中的 Promise 是基于非标准化的 [`setImmediate`](https://developer.mozilla.org/en-US/docs/Web/API/Window/setImmediate) API 实现的 [polyfilled](https://github.com/facebook/react-native/blob/main/Libraries/Core/polyfillPromise.js#L37)。我们正在努力将 JS 引擎的原生 Promise 和微任务通过 JSI 实现，并在平台上引入 [`queueMicrotask`](https://developer.mozilla.org/en-US/docs/Web/API/queueMicrotask)，这是最近引入的 web 标准，以更好地支持现代异步 JavaScript 代码。

## 带动社区发展

Hermes 对 Facebook 来说现已足够。但是，我们的工作远不止于此，终极目标是社区能够使用 Hermes 来推动整个生态系统的体验，使每个人都能利用它的所有功能，并进一步发掘它的潜力。

### 扩展到新平台

Hermes 起初只为 Android 上的 React Native 开放了源码。从那以后，我们看到社区成员将 Hermes 的支持扩展到 [React Native 生态系统所能扩展到的诸多平台之上](https://reactnative.dev/blog/2021/08/26/many-platform-vision)。

在 React Native 0.64 中，由 [Callstack](https://callstack.com/) 牵头完成了将 [Hermes 引入 iOS 平台的工作](https://reactnative.dev/blog/2021/03/12/version-0.64)。他们编写了 [系列文章](https://callstack.com/blog/bringing-hermes-to-ios-in-react-native/) 并主持了 [播客](https://callstack.com/podcasts/react-native-0-64-with-hermes-for-ios-ep-5) 来介绍他们的实现过程。从他们提供的 benchmark 来看，与 JSC 相比，Hermes **在 iOS 上能为 Mattermost 应用稳定提供近 ~40% 的启动优化同时减少了近 ~18% 的内存占用**，而应用程序只增加了 2.4 MiB 开销。强烈推荐你去看下 [原文](https://callstack.com/blog/hermes-performance-on-ios/)。

微软则一直在推进将 [Hermes 引入 React Native for Windows 和 React Native for macOS](https://microsoft.github.io/react-native-windows/docs/hermes) 中。[在 Microsoft Build 2020](https://youtu.be/QMFbrHZnvvw?t=389) 上，微软分享了 Hermes 的内存占用（[工作集](https://en.wikipedia.org/wiki/Working_set)）比 React Native for Windows 中的 Chakra 引擎低 13%。最近，在一些综合的 benchmarks 中，他们发现 Hermes v0.8（发布了 Hades 以及上文提到的 SMI 和指针压缩优化）**内存占用比其他引擎少近 30%-40%**。[桌面版的 Messenger](https://www.messenger.com/desktop) 的视频通话功能就是基于 React Native 构建的，也由 Hermes 提供支持。

最后，但并非不重要的是 Hermes 也一直在为 Oculus 上用 React 全家桶构建的所有 VR 提供支持，也包括 Oculus Home。

### 支持我们的社区

我们了解到仍有些缺陷阻碍了部分社区采用 Hermes，我们致力于对这些缺失的功能建立支持。我们的目标是让 Hermes 功能齐全，使其成为大多数 React Native 应用程序的正确选择。以下是社区如何优化 Hermes 的路线图：

<!-- alex ignore just -->
<!-- alex ignore fellowship -->

- [`Proxy` 和 `Reflect`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Meta_programming) 最初未被 Hermes 实现，因为在 Facebook 并不使用它们。即使不使用 Proxy，我们也担心添加 Proxy 会损害属性查询的性能。但由于 [MobX](https://mobx.js.org/README.html) 和 [Immer](https://immerjs.github.io/immer/) 等库的流行，Proxy 很快成为了 Hermes [最需要的功能](https://github.com/facebook/hermes/issues/33)。我们经过慎重评估后，决定只为社区建立它，而且我们会以最低的成本来实现它。由于这是我们不使用的功能，我们会依靠社区来证明其稳定性。我们起初在某个 flag 中测试了 Proxy，后来为 [v4.0](https://github.com/facebook/hermes/issues/33#issuecomment-668374607) 和 [v0.5](https://github.com/facebook/hermes/issues/33#issuecomment-668374607) 提供了可选的 npm 包。自 [v0.7 起默认启用](https://github.com/facebook/hermes/releases/tag/v0.7.0)。
- [ECMAScript 国际化 API 规范（ECMA-402，也称为 `Intl`）](https://hermesengine.dev/docs/intl) 是 [第二大需求的功能](https://github.com/facebook/hermes/issues/23)。`Intl` 是一组庞大的 API，通常需要实现包含 **6MB 大小** 的 [Unicode CLDR](https://cldr.unicode.org/index) 数据。这就是为什么类似于 [FormatJS (a.k.a. `react-intl`)](https://github.com/formatjs/formatjs) 的 polyfills 以及像社区为 [JSC 构建的国际版](https://github.com/react-native-community/jsc-android-buildscripts#international-variant) JS 引擎如此庞大的原因。为了避免大幅增加 Hermes 的二进制大小，我们决定用另一种方式实现它，就是采用消耗和映射操作系统中所提供的 ICU 设施，而代价则是不同平台的行为会存在（轻微）差异。
  - Microsoft 合作建立了对 Android 端的支持。它几乎涵盖了从 ECMA-402 到 ES2020 的所有内容，**对体积的影响只占 3%（每个 ABI 约为 57-62K）**。我们在 [Twitter 上发起了投票](https://twitter.com/tmikov/status/1336442786694893568)，投票结果是强烈要求默认支持 `Intl`，因此，我们在 [v0.8](https://github.com/facebook/hermes/releases/tag/v0.8.0) 中对其进行了默认支持。
  - Facebook 赞助了 [美国职业棒球联盟](https://mlh.io/)，并发起了一项 [远程开源奖学金](https://news.mlh.io/welcoming-facebook-back-as-a-sponsor-of-the-2020-2021-mlh-fellowship-08-12-2020)。去年，我们推出了 [Hermes 采样分析器](https://reactnative.dev/docs/profile-hermes)。今年，我们的研究员将月 Hermes，React Native 以及 Callstack 的成员合作，在 iOS 上添加对 Hermes `Intl` 的支持，敬请期待！
- 感谢一直与我们合作的伙伴，帮助我们发现影响社区的问题。
  - 有些伙伴帮助我们确定了关键性规范中的分歧，如 [ES2019](https://github.com/tc39/ecma262/pull/1340) 中修正了 [`Array.prototype.sort` 的稳定性](https://github.com/facebook/hermes/issues/212)。已修复，并将在下个版本中发布。
  - 有些伙伴发现，我们的默认堆大小限制过小，对于许多不熟悉自定义 Hermes 的 GC 配置的用户来说，会造成 [不必要的 GC 压力](https://github.com/facebook/hermes/issues/295) 和 [OOM 崩溃](https://github.com/facebook/hermes/issues/511)。因此，我们将默认堆大小从 512MiB 提升至 3GiB，这对大多数用户来说已足够。
  - 还有些伙伴提出，我们专用的 `Function.prototype.toString` 实现 [导致库执行不正确的行为检测时性能下降](https://github.com/facebook/hermes/issues/471#issuecomment-820123463) 并 [阻止用户进行源码注入](https://github.com/facebook/hermes/issues/114#issuecomment-887106990)。这非常符合我们的愿景，Hermes 应尽可能不妨碍开发者的工作，并尊重他们的想法。

## 摘要

总而言之，我们的愿景是让 Hermes 做好成为所有 React Native 平台默认 JavaScript 引擎的准备。我们已经朝着这个方向努力了，我们希望听到所有伙伴对这个方向的建议。

对我们来说，为生态系统顺利采用是极其重要的。我们鼓励大家试用 Hermes，并根据情况在 [GitHub 仓库](https://github.com/facebook/hermes) 中提交 issue，让我们知道你的使用反馈、问题、功能请求以及不兼容的情况等等。

## 致谢

衷心的感谢 Hermes 团队、React Native 团队以及 React Native 社区的众多贡献者们，感谢他们为 Hermes 优化做出的贡献。

<!-- alex ignore White -->

我还想亲自（按字母排序）Eli White，Luna Wei，Neil Dhar，Tim Yung，Tzvetan Mikov 以及其他伙伴在我写作期间提供的帮助。

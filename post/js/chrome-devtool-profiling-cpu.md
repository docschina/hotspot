# 再见 JavaScript 性能分析器，使用 “Performance” 面板对 CPU 进行性能分析

**[原文链接](https://developer.chrome.com/blog/profiling-cpu?hl=zh-cn)**

> 译注：**JavaScript 性能分析器** 即 **JavaScript Profiler** 面板；**性能**面板即 **Performance** 面板

Chrome 124 即将停用  **JavaScript 性能分析器**面板。今后，您可以使用**性能**面板分析 Node.js CPU 性能。

## 我们为什么要弃用 JavaScript 性能分析器？

早在 Chrome 58 时，开发者工具团队就计划最终弃用 **JavaScript 性能分析器**。有以下几个原因：

- **它目前已不再处于积极开发阶段**。**JavaScript 性能分析器**已经几年没有收到任何重大更新，团队也没有继续开发它的资源。
- **更顺畅的分析体验**。**性能**面板已用于各种性能分析，由于它能够在 Node.js 中分析 JavaScript CPU 性能，因此有必要将所有内容整合到一处，以确保一致性和效率。
- **“性能”面板更好**。我们会继续添加新功能和增强功能，使其成为功能更强大、更易用的性能分析工具。

## 弃用后该怎么进行性能分析？

如需详细了解如何分析 JavaScript CPU 性能，请参阅[分析 Node.js 性能](https://developer.chrome.com/docs/devtools/performance/nodejs?hl=zh-cn)。

以下是关于使用**性能**面板的一些技巧：

- 使用**火焰图**确定性能瓶颈。

![火焰图。](https://developer.chrome.com/static/blog/profiling-cpu/image/flame-chart.png?hl=zh-cn)

- 使用  **自下而上**  和 **调用树** 标签页了解函数之间的关系。

![“自下而上”标签页。](https://developer.chrome.com/static/blog/profiling-cpu/image/bottom-up.png?hl=zh-cn)

![“调用树”标签页。](https://developer.chrome.com/static/blog/profiling-cpu/image/call-tree.png?hl=zh-cn)

## 我们将如何逐步弃用 JavaScript 性能分析器？

我们开发了一个原型并在 GitHub 上公开发布了[征求意见稿 (RFC)](https://github.com/ChromeDevTools/rfcs/discussions/2)，以征求开发者的反馈。

此外，我们还会积极联系开发者专家来测试原型，解决各种顾虑或问题，以确保**性能**面板满足核心的性能分析需求。

我们将分  [4 个阶段](https://github.com/ChromeDevTools/rfcs/discussions/2#discussioncomment-5189668)逐步停用 JavaScript 性能分析器，以便开发者有足够的时间进行调整和采用。

> 译注：在 Chrome 115 及以后，这四个阶段已经全部完成。

## 主要问题及解决方法

在我们收到的反馈中，最迫切的担忧主要围绕以下三个问题：

- **支持  `.cpuprofile`  文件格式**。**JS 性能分析器**使用另一种文件格式。**性能**面板应支持此功能。
- **加载速度缓慢**。面板的加载速度似乎很慢，干扰了分析过程。
- **缺少 JavaScript 虚拟机选择器。**在某些情况下，缺少 JavaScript 虚拟机实例选择器会限制分析功能。

我们来看看每个问题，看看我们是如何解决这些问题的。

### 加载速度缓慢

开发者告诉我们，**性能**面板加载大型数据文件的用时过长，有时甚至会崩溃。

我们使用开发者工具分析开发者工具（我们称之为“DevTools-on-DevTools”）。我们发现了一些问题，并做出了多项优化：

- 已将  `Set`  替换为  `Array`  数据结构。
- 移除了不必要的  `Map`  数据结构。
- 将递归函数重构为迭代函数（for 循环），以减少内存堆栈使用量。

通过修复这些瓶颈，我们将大文件的加载速度提升了 80%！🎉

详细了解我们在这篇博文中了解到的信息：[通过性能感知功能将性能面板提高 400%](https://developer.chrome.com/blog/perf-panel-4x-faster?hl=zh-cn)。

### 缺少 JavaScript 虚拟机选择器

初始原型缺少 JavaScript 虚拟机选择器。开发者使用此报告来深入了解并专注于分析特定的虚拟机实例。

现在，我们向**性能**面板添加了一个 JavaScript 虚拟机选择器。其中显示了所有可用的 JavaScript 虚拟机实例的下拉列表。选择某个实例后，**性能**面板会加载该特定实例的 CPU 配置文件。

![“调用树”标签页。](https://developer.chrome.com/static/blog/profiling-cpu/image/vm-selector.png?hl=zh-cn)

### 支持  `cpuprofile`  文件格式

以前，**性能**面板仅支持跟踪文件，即包含跟踪事件数组的 JSON 文件。

另一方面，**JavaScript 性能分析器**  支持 CPU 配置文件，即扩展名为  `.cpuprofile`  且包含 JSON 对象的文件。如下所示：

```javascript
{
    // 性能节点列表，第一个是根节点
    nodes: ProfileNode[];
    // 性能分析开始时间戳（微秒）
    startTime: number;
    // 性能分析结束时间戳（微秒）
    endTime: number;
    // 顶部节点的样本 ID
    samples?: integer[];
    // 相邻样本之间的时间间隔（微秒）
    // 第一个 delta 是相对于 profile.startTime 的
    timeDeltas?: integer[];
}
```

新工作流不应阻止开发者分析现有  `cpuprofile`。因此，**性能**面板现在支持跟踪文件和 CPU 配置文件。您可以将  `cpuprofile`  文件导入到  **性能**面板   中，该文件将正确加载。

在后台，我们使用正则表达式检测对象结构差异。如果文件内容以  `{"nodes":[`  开头，则表示它是 CPU 配置文件。否则为跟踪文件。

一旦确定了内容类型，我们就会进行相应处理。对于跟踪文件，我们会解析事件并构建一个时间轴。对于 CPU 配置文件，我们会解析 JSON 对象并构建火焰图。

## 总结

无论是网站分析还是 Node.js 和 Deno 应用的 CPU 性能分析，使用**性能**面板都可以获得更顺畅的分析体验。

如果您有反馈或建议，请为此  [bug](https://issues.chromium.org/issues/40235609)  添加评论，或通过以下某种方式与我们联系。

## 下载预览渠道

您可以考虑将 Chrome [Canary 版](https://www.google.com/chrome/canary/?hl=zh-cn)、[Dev 版](https://www.google.com/chrome/dev/?hl=zh-cn)或  [Beta 版](https://www.google.com/chrome/beta/?hl=zh-cn)用作默认开发浏览器。通过这些预览渠道，您可以使用最新的开发者工具功能，测试先进的网络平台 API，并在用户采取行动之前发现网站上的问题！

## 与 Chrome 开发者工具团队联系

使用以下选项讨论博文中的新功能和变化，或讨论与开发者工具有关的任何其他内容。

- 通过  [crbug.com](https://crbug.com/)  提交建议或反馈。
- 使用开发者工具中的**更多选项**   ![了解详情](https://developer.chrome.com/static/images/devtools-more.png?hl=zh-cn)   > **帮助** > **报告 DevTools 问题**来报告开发者工具问题。
- 发推文并[@ChromeDevTools](https://twitter.com/intent/tweet?text=@ChromeDevTools)。
- 请在 [YouTube 视频](https://goo.gle/devtools-youtube)或“开发者工具提示” [YouTube 视频](https://goo.gle/devtools-tips)中留言说明“开发者工具的新变化”。

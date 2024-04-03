# Goodbye JS Profiler, profiling CPU with the Performance panel

The **JavaScript Profiler** panel is going away in Chrome 124. Moving forward, use the **Performance** panel to profile Node.js CPU performance.

## Why are we deprecating the JavaScript Profiler? (JS Profiler)

As early as Chrome 58, the DevTools team planned to eventually deprecate the **JS Profiler**. There are several reasons why:

- **It is no longer being actively developed**. The **JS Profiler** hasn't received any major updates in several years, and the team doesn't have the resources to continue developing it.
- **A more streamlined profiling experience**. The **Performance** panel is already used for all kinds of performance analysis, and with its ability to profile JavaScript CPU performance in Node.js, it makes sense to consolidate everything in one place for consistency and efficiency.
- **The Performance panel is better**. We continue to improve it by adding new features and enhancements, making it a more powerful and user-friendly tool for performance analysis.

## What should you do after deprecation?

To learn more about how to profile JavaScript CPU performance, see [Profile Node.js performance](https://developer.chrome.com/docs/devtools/performance/nodejs).

Here are some tips for using the **Performance** panel:

- Use the **flame chart** to identify performance bottlenecks.

![The flame chart.](https://developer.chrome.com/static/blog/profiling-cpu/image/flame-chart.png)

- Use the **Bottom-up** and **Call tree** tabs to understand the relationships between functions.

![The Bottom-up tab.](https://developer.chrome.com/static/blog/profiling-cpu/image/bottom-up.png)

![The Call tree tab.](https://developer.chrome.com/static/blog/profiling-cpu/image/call-tree.png)

## How do we handle the deprecation?

We developed a prototype and published the [Request for Comments (RFC)](https://github.com/ChromeDevTools/rfcs/discussions/2) publicly on GitHub to seek feedback from developers.

On top of that, we actively reach out to developer experts to test the prototype, addressing any concerns or issues to ensure that the **Performance** panel meets the core profiling needs.

We are gradually phasing out the JS Profiler in [4 stages](https://github.com/ChromeDevTools/rfcs/discussions/2#discussioncomment-5189668) to give developers enough time to adjust and adopt.

## Key issues and how we fixed them

Among the feedback we received, the most pressing concerns centered around three main issues:

- **Supporting `.cpuprofile` file format**. The **JS Profiler** uses a different file format. The **Performance** panel should support it.
- **Slow loading speed.** The panel's loading speed seemed slow, interfering with the profiling process.
- **Missing JavaScript VM selector.** The absence of a JavaScript VM instance selector limited profiling capabilities in certain scenarios.

Let's take a look at each of these issues and see how we fixed them.

### Slow loading speed

Developers told us the **Performance** panel took too long to load large data files and sometimes it even crashed.

We used DevTools to analyze DevTools (we call it "DevTools-on-DevTools"). We found problems and made several optimizations:

- Replaced `Set` with `Array` data structures.
- Removed unnecessary `Map` data structures.
- Refactored recursive functions to iterative (for loops) to reduce the memory stack usage.

By fixing these bottlenecks, we made loading 80% faster for large files! 🎉

Read more about what we learned in this blog post: [A 400% faster Performance panel through perf-ception](https://developer.chrome.com/blog/perf-panel-4x-faster).

### The missing JavaScript VM selector

The initial prototype was missing the JavaScript VM selector. Developers use it to drill down and focus on analyzing a specific VM instance.

We've now added a JavaScript VM selector to the **Performance** panel. It shows a drop-down list of all available JavaScript VM instances. When you select an instance, the **Performance** panel loads the CPU profile for that specific instance.

![The Call tree tab.](https://developer.chrome.com/static/blog/profiling-cpu/image/vm-selector.png)

### Supporting the `cpuprofile` file format

Previously, the **Performance** panel only supported trace files, which are JSON files with an array of trace events.

On the other hand, the **JS Profiler** supported CPU profiles, which are files with the `.cpuprofile` extension that contain a JSON object. They look like this:

```
<span>{</span><span><br>&nbsp; &nbsp; </span><span>// The list of profile nodes. First item is the root node.</span><span><br>&nbsp; &nbsp; nodes</span><span>:</span><span> </span><span>ProfileNode</span><span>[];</span><span><br>&nbsp; &nbsp; </span><span>// Profiling start timestamp in microseconds.</span><span><br>&nbsp; &nbsp; startTime</span><span>:</span><span> number</span><span>;</span><span><br>&nbsp; &nbsp; </span><span>// Profiling end timestamp in microseconds.</span><span><br>&nbsp; &nbsp; endTime</span><span>:</span><span> number</span><span>;</span><span><br>&nbsp; &nbsp; </span><span>// Ids of samples at top nodes.</span><span><br>&nbsp; &nbsp; samples</span><span>?:</span><span> integer</span><span>[];</span><span><br>&nbsp; &nbsp; </span><span>// Time intervals between adjacent samples in microseconds.</span><span><br>&nbsp; &nbsp; </span><span>// The first delta is relative to the profile startTime.</span><span><br>&nbsp; &nbsp; timeDeltas</span><span>?:</span><span> integer</span><span>[];</span><span><br></span><span>}</span><span><br></span>
```

The new workflow shouldn't prevent developers from analyzing the existing `cpuprofile`. Therefore, the **Performance** panel now supports both trace files and CPU profiles. You can import the `cpuprofile` file into the **Performance** and it will load correctly.

Behind the scenes, we detect the object structure differences by using a regular expression. If the file content starts with `{"nodes":[`, then it is a CPU profile. Otherwise, it is a trace file.

Once the type of the content is identified, we process it accordingly. For a trace file, we parse the events and build a timeline. For a CPU profile, we parse the JSON object and build a flame chart.

## Conclusion

Use the **Performance** panel for a more streamlined profiling experience, both for websites and profiling CPU performance in Node.js and Deno applications.

If you have feedback or suggestions, add a comment to this [bug](https://issues.chromium.org/issues/40235609) or reach out using one of the following options.

## Download the preview channels

Consider using the Chrome [Canary](https://www.google.com/chrome/canary/), [Dev](https://www.google.com/chrome/dev/) or [Beta](https://www.google.com/chrome/beta/) as your default development browser. These preview channels give you access to the latest DevTools features, test cutting-edge web platform APIs, and find issues on your site before your users do!

Use the following options to discuss the new features and changes in the post, or anything else related to DevTools.

- Submit a suggestion or feedback to us via [crbug.com](https://crbug.com/).
- Report a DevTools issue using the **More options**   ![More](https://developer.chrome.com/static/images/devtools-more.png)   > **Help** > **Report a DevTools issues** in DevTools.
- Tweet at [@ChromeDevTools](https://twitter.com/intent/tweet?text=@ChromeDevTools).
- Leave comments on our What's new in DevTools [YouTube videos](https://goo.gle/devtools-youtube) or DevTools Tips [YouTube videos](https://goo.gle/devtools-tips).

# 打包分割

把你的代码分割成小的、可复用的块

当构建一个现代网页应用的时候，打包器如[Webpack](https://webpack.js.org/)和[rollup](https://rollupjs.org/guide/en/)基于应用的源代码，把它打包成一个或者多个 bundles - 打包器的产物，有相关的代码组成，所有都被打包进一个文件。当用户访问网页时，为了在屏幕上展示相应的数据，bundle（包）就会被请求和加载。

JS 引擎如 V8 能够解析和编译被请求的数据当它在加载完之后。尽管现代浏览器已经进化到可以尽可能的高效和高性能的解析和编译代码，但开发者还是能自主控制对请求数据的加载时间和执行时间这两个步骤的优化。我们想确保尽可能的缩短执行时间以免对主线程造成阻塞。

虽然现代浏览器能够对收到的 bundle 进行流化处理，但在第一个像素被渲染到用户的设备上之前仍然可能需要相当一部分时间。bundle 体积越大，浏览器引擎就会越迟开始第一个渲染的调用。直到那个时候，用户都不得不瞪大眼睛等着空白的屏幕相当一段时间，而这往往让人抓狂 😫。

我们想要尽可能块的展示数据给用户。更大的包导致更长的加载时间、处理时间和执行时间。为了可以加快页面展示的速度，如果我们可以减少打包的体积那就太棒了！

相比于请求一个包含了不必要的代码的大体积包，我们可以把包分割成几个更小的包。

<a href="https://res.cloudinary.com/ddxwdqwkr/video/upload/v1609056516/patterns.dev/bundle-splitting-7.mp4" target="__blank">点击观看原文视频</a>

通过打包分割应用，我们可以减少加载、处理和执行一个 bundle 的时间。通过减少加载和执行时间，我们可以减少内容刚开始出现在用户屏幕上（也就是内容首次渲染）的等待时间，以及最大的组件被渲染到屏幕上（也就是最大内容渲染）的等待时间。

虽然在屏幕上看到东西很好，但我没不仅仅想看到内容。为了能够有一个具体完备功能的应用，我们想要用户也能够和应用交互。UI 界面之后在 bundle 被加载和执行之后才会变得可交互。我们把在页面所有内容被渲染并被弄的可交互所需要等待的时间称为 TTI（Time To Interactive）。

<a href="https://res.cloudinary.com/ddxwdqwkr/video/upload/v1609056514/patterns.dev/bundle-splitting-2.mp4" target="__blank">点击观看原文视频</a>

一个越大的 bundle 不一定意味着更长的执行时间。有时候我们加载了一大堆代码，但是用户却根本用不到。也许 bundle 的一些部分只会在特定的用户交互时才会被执行，而这些特定的交互并不一定会发生。

在用户能在屏幕上看到任何内容之前，引擎还是必需加载、解析和编译那些不会在初始化渲染中被使用的代码。尽管解析和编译的花销实际上可以忽略，这得益于浏览器高性能的处理这两个步骤，但是获取一个比实际需要更大的 bundle 还是会损害应用的性能。使用低端设备或者弱网的用户会看到在 bundle 被获取前加载时间的巨大延长。

<a href="https://res.cloudinary.com/ddxwdqwkr/video/upload/v1609056515/patterns.dev/bundle-splitting-3.mp4" target="__blank">点击观看原文视频</a>

第一部分仍然必需被加载和处理，即使引擎只使用文件的最后一部分。我们可以将这部分代码从初始渲染所需要的代码中分离出来，而不是一开始就请求在当前到导航中优先级不高的代码部分。

<a href="https://res.cloudinary.com/ddxwdqwkr/video/upload/v1609244173/patterns.dev/bundlesplits_hp5st4.mp4" target="__blank">点击观看原文视频</a>

通过打包分割将大体积的 bundle 分成两个更小的 bundle，main.bundle.js 和 emoji-picker.bundle.js，我们通过请求更少量的数据减少了初始化加载时间。

<a href="https://res.cloudinary.com/ddxwdqwkr/video/upload/v1609056514/patterns.dev/bundle-splitting-4.mp4" target="__blank">点击观看原文视频</a>

本项目中，我们会覆盖一些允许我们将应用打包分割成几个小包的方法，以及用最有效和性能最优的方式加载资源。

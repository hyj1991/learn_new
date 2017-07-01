# Easy-Monitor 2.0: 开启你的  Node.js 内核性能监控

# I. 写在最前面

Easy-Monitor 1.x 的版本在今年三月的时候发布了，陆续从最初的仅仅为了做一个短时间 ( 5s ) 的 cpu-profiling 查看函数瓶颈，到后面加入了针对疑似内存泄漏点的排查功能。

整个 1.x 版本主要还是试验一下自己的做内核监控的可行性和想法，所以在使用上和设计架构上没有做太多的考量，导致易用性和兼容性上存在一些问题，因此从 5 月中旬开始到现在，对这个项目做了一个彻底的重构，也是现在这个 2.0 版本的初衷~

# II. 功能

* 服务器状态概览信息展示
* 实时 CPU 函数性能分析，帮助定位程序的性能瓶颈点
* 实时 Memory 堆内内存结构分析，帮助定位到内存疑似泄漏点

## 2.0 的一些新特性

* 基于 vue.js 和 iview 组件全新设计的 UI
* 全面兼容 v4.x ~ v8.x
* 新增概览 Overview 展示页
* 支持 动态更新配置，无需重启一键生效
* 支持 Stream 流式解析更大的 HeapSnapshot
* 支持 Cluster 集群部署，支持定制 私有协议

# III. 快速开始

## - 安装模块

执行如下命令安装 Easy-Monitor：

```js
npm install easy-monitor
```

## - 项目中引入

在你的项目入口文件中按照如下方式引入，当然请传入你的项目名称：

```js
'use strict';
const easyMonitor = require('easy-monitor');
easyMonitor('你的项目名称');
```

好了，此时你所需要做的一切都已就绪，接下来以你喜欢的方式运行项目即可，不管是 ```nohup``` 还是 ```pm2```，亦或是直接 ```node``` 启动均可。

## - 访问监控页面

打开你的浏览器，访问 http://localhost:12333 ，即可看到进程界面。

## - 完整样例 & Demo

为了帮助大家更好的理解使用，下面编写一个 Easy-Monitor 嵌入 Express 应用的完整例子

```js
'use strict';
const easyMonitor = require('easy-monitor');
easyMonitor('Mercury');
const express = require('express');
const app = express();

app.get('/hello', function (req, res, next) {
    res.send('hello');
});

app.listen(8082);
```

将上述的内容保存成一个 js 文件，启动后访问 http://127.0.0.1:12333 即进入 Easy-Monitor 的首页，就是这样的简单！

这里有一个在线真实的 Demo 地址：[Easy-Monitor Demo](http://easy-monitor.cn)，可以点击进入自行尝试一番。

## 更多关于 集群部署、深度定制化开发、通用配置以及动态更新配置的说明可以查看 [Easy-Monitor 详细文档](http://easy-monitor.cn/document) 。


# IV. 设计架构

这个项目旨在帮助大家更加深入地理解自己的 Node 项目运行时状态，以便能够在想做性能优化时更有针对性，也考虑到使用的难易程度，所以对于 **快速部署** 和 **集群 C/S  模式部署** 两种都做了支持，整体设计架构如下:

## 快速部署

快速部署模式下，web 页面展示的进程会以 fork 的形式自动启动，采集内核数据的客户端则会绑定到业务进程，两者间采用默认的 tcp 通信模式:

![jiagou1.png](//dn-cnode.qbox.me/FtCoXxJCTkNDpXC7sgYcIMeJtNpv)

如上图所示，这种部署模式下，优点是仅需要 require 即可，无任何额外操作，开箱即用。缺点也比较明显，如果项目是分布在不同的服务器上，则需要每次都输入对应的项目所在服务器地址才能访问。

## 集群 C/S 部署

这种部署模式其实就是独立部署 dashboard 进程，采集内核数据的客户端则分散到各个业务进程，数据统一上报给独立部署的 dashboard 进程，整体设计架构如下:

![jiagou2.png](//dn-cnode.qbox.me/FpkM_4gCB8HBIpCFit2rSU40hvAS)

这种部署模式下，在业务项目本身集群化部署的情况下非常易于统一查看，省去了快速部署模式下每次访问不同地址的烦恼。

# V. 图文使用说明

## 动态更新配置

进入监控 web 页面首页后，点击项目标题，即可打开动态更新配置的 Modal 框 (需要版本 >= v2.1.0)，如图：

![dynimic1.png](//dn-cnode.qbox.me/Fs5uR6fP4CNATRKOub6FztDGteam)

弹出框具体每一项的内容和含义详见 [开启动态更新配置](http://easy-monitor.cn/document/#/?id=-开启动态更新配置) 。

## OS 信息概览

进入监控 web 页面首页后，找到对应的项目，所在服务器选择你想查看的服务器，进程一项选择你想查看的进程，解析类型一项选择 ***OS***，最后点击项目标题旁边的 ***start*** 按钮，如下图所示：

![gailan2.png](//dn-cnode.qbox.me/FgpZXKZ-IQL1qbc25H5yQnNjHC9B)

即可进入当前选中进程的概览信息页面，此页面可以看到服务器的 CPU 使用率，以及选中的进程的 Memory 占用情况，其中内存占用展示了三类：

* heapUsed: 正在使用的堆内内存大小
* heapTotal: 申请的总堆内内存大小
* rss: 堆外分配的内存大小

如下图所示:

![gailan3.png](//dn-cnode.qbox.me/FnYogKudNxd92qVSZ19cl3e4dWF5)

这个 line-charts 展示模块的设计实现参考了 @JerryC8080 的 **Memeye** 项目，本文末和源码中做了特别标注。

## CPU 数据采集分析函数运算瓶颈

### 使用方法

进入监控 web 页面首页后，找到对应的项目，所在服务器选择你想查看的服务器，进程一项选择你想查看的进程，解析类型一项选择 ***CPU***，最后点击项目标题旁边的 ***start*** 按钮，如下图所示：

![cpu1.png](//dn-cnode.qbox.me/FmBkBDdxuGvnXZWKGMqt8U-6DOUC)

如果你没有更改过启动参数，那么会进行 5s(***默认值***) 的 CPU-Profiling，然后进行分析，展示结果包含：

* 执行耗费时间大于 500ms(***默认值***) 的函数列表
* 执行耗费时间最长的 5个(***默认值***) 函数
* V8 引擎逆优化最频繁的 5个(***默认值***) 函数

如图所示:

![cpu2.png](//dn-cnode.qbox.me/FtKNJdfj59JdBP3NX75jFIwgoVgZ)

这里可以看到，我们将鼠标移至 ***系统路径*** 一栏后，会在气泡框内展示出对应函数的完整的在服务器上的路径。下面解释下图表中的元素含义:

#### 函数运算耗费时长相关

* **函数名称:** 即为编写的 js 函数名称
* **执行时长:** 该函数的运算时长，注意如果函数内包含异步操作，那么异步操作花费的时间是不统计在内的。这个时长就是指纯函数本身占据CPU运算的时间，所以可以定位到 CPU 运算瓶颈点。
* **占据调用者百分比:** 这里指的是本函数执行的时长占据调用者函数的总执行时长的百分比
* **系统路径:** 函数在服务器的具体文件路径

#### 引擎逆优化相关

* **函数名称:** 即为编写的 js 函数名称
* **逆优化原因:** 此函数被 V8 引擎逆优化的原因
* **系统路径:** 函数在服务器的具体文件路径

### 动态更改 CPU 配置，获得更多数据

上面使用方法一节中看到很多数值被标注为 **默认值**，这表示这些值我们可以通过动态修改 CPU-Profiling 的配置来进行调整，这是一个动态调整的例子: 

想对部署的项目进行压测 30 分钟，期间采集 CPU 数据，过滤出执行耗费大于 100ms 的函数，并且分析的结果每一项展示 50 条。

那么可以按照上面提到的方法进入动态更新配置的 Modal 框，修改参数如下图所示:

![cpu3.png](//dn-cnode.qbox.me/FoBe9uR_3WLbIPQi1eA3n-tHQWmd)

修改完毕后，点击 Modal 弹出框右下角的 ***确定*** 按钮，即可通知服务器更改配置，保存成功后再按照上面的使用方法进行 CPU 数据采集和分析，等待约 10 分钟就可以看到结果。

#### 关于CPU 动态配置中的自定义过滤

在上面的截图中可以看到，CPU 动态配置项中还有个按钮是 **自定义过滤**，这个是用来对 CPU 采集数据分析结过做过滤用的。

项目默认会过滤掉路径中包含 ```node_modules```、```anonymous``` 等系统函数，这样可以保证结果中只包含开发者编写的函数，那么关闭自定义过滤的话会将系统函数也展示在内。

自定义过滤函数大家也可以根据项目需求而自行注入，具体可以看开发文档中的介绍: [自定义过滤](http://127.0.0.1:12333/document/#/?id=-自定义过滤)。

## Memory 数据采集分析定位疑似内存泄漏点

### 使用方法

进入监控 web 页面首页后，找到对应的项目，所在服务器选择你想查看的服务器，进程一项选择你想查看的进程，解析类型一项选择 ***MEM***，最后点击项目标题旁边的 ***start*** 按钮，如下图所示：

![mem1.png](//dn-cnode.qbox.me/FraJnyH-LE9GFMdeBBtcwk5fxGwT)

页面会对堆内内存分析中间过程进行一些展示，提高体验，等分析完毕后，即会展示出对应的结果，如下图所示:

![mem2.png](//dn-cnode.qbox.me/Fo-Dq8Rj1fifLWqZKi-MBt1mPuhv)

这里根据分析当前进程堆内分配内存的结构，计算得出 RetainedSize 最大的节点占据总的堆内分配内存占比来判断是否有内存泄漏风险，以及对应的疑似泄漏节点引力图。

这一部分引力图详细定位内存泄漏点的逻辑和 Easy-Monitor 1.x 没有变化，可以参见上一篇文章来获取详情: [轻松排查线上Node内存泄漏问题](https://cnodejs.org/topic/58eb5d378cda07442731569f)

### 动态更改 Memory 配置，获得更多数据

Memory 分析同样也提供了一些可以动态更改的配置，试看如下场景: 

Node 项目发生内存泄漏时，想获取更多的疑似内存泄漏点，以及需要更多的子节点信息和引力图深度来排查定位内存泄漏点，那么我们可以通过修改提供的动态配置来达到不重启服务器即可生效的目的，如下图所示:

![mem3.png](//dn-cnode.qbox.me/FoGh6Ewdp2AmGj0CnrykNy49xHxh)

这里将默认的展示 5 个疑似内存泄漏点更改为 10 个，将引力图的每一个节点的展开子节点数从默认的 5 个更改为 8 个，将引力图深度从默认的 25 更改为 50，这样就能获取到更多的内存信息。

#### 关于 Memory 动态配置中的 Root 节点配置

Root 节点信息的展示是帮助大家更深入地理解当前的堆内内存结构，对于排查内存泄漏并没有太大帮助，但是这一项打开后会增加浏览器下载的 HeapSnapshot 分析结果数据大小，可以将其关闭掉。


# VI. 特别感谢

重构过程中思路参考了一些开源项目，在最后特别感谢下 @JerryC8080：

* **Memeye:** 作者为 [JerryC8080](https://github.com/JerryC8080)，2.0 版本的 overview 页面实现和 logger 模块实现参考了 [Memeye](https://github.com/JerryC8080/Memeye) 项目

源代码中对应位置也做了标注，感谢作者们的开源精神。

# VII. 结语

Easy-Monitor 项目我个人会长期维护下去，下一阶段的计划大致如下：

* 1. 研究下 Node 8 提供的 N-API，以将计算量巨大的 CPU 和 HeapsnapSnapshot 运算解析部分移植成扩展，放入单独的计算线程中来进一步提升性能。
* 2. 研究下 Chrome 最新提供的 Record Allocation Profiler 和 Record Allocation Timeline 的实现，看看能不能合并入 Easy-Monitor 的 Memory 分析部分实现线上 Node 项目的实时分析。

最后本项目地址在 [Easy-Monitor](https://github.com/hyj1991/easy-monitor) ，如果使用中遇到问题可以提 [issue](https://github.com/hyj1991/easy-monitor/issues) ，保证快速响应。

如果本项目对您有用欢迎 star，如果想一起开发或者有更多想法也欢迎联系我~
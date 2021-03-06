# Serverless

> Server：服务端（流量进入反向代理开始都算服务端），less：较少关心；Serverless：较少关心服务端也就是**服务端免运维**。

Serverless 是对运维体系的极端抽象，它给应用开发和部署提供了一个极简模型。这种高度抽象的模型，可以让一个零运维经验的人，几分钟就部署一个 Web 应用上线，并对外提供服务。**Serverless要解决的问题就是省钱和省力**。

Serverless的优势：

1. 有效降低企业中中长尾应用（每天大部分时间都没有流量或者有很少流量的应用）的运营成本。在落地微服务架构后，一些边缘的微服务被调用的概率其实很低。又很难通过人工来控制中长尾应用，因为这里面不少应用还是被强依赖的，不可以直接下线处理。Serverless 的极速冷启动特性，就可以节省这部分开销。
2. 提高研发效能。Serverless 应用架构的设计，其中，SFF（Serverless For Frontend）可以让前端同学自行负责数据接口的编排，微服务 BaaS 化则让后端同学更加关注领域设计。这是一个颠覆性的变化，它能够进一步放大前端工程师的价值。
3. Serverless 作为一门新兴技术，未来的想象空间很大。
   1. 创业公司用 FaaS 来做基础设施编排和云服务编排；
   2. 外包公司利用 Serverless 应用架构的快速迭代能力，提升开发效率，降低出错率，沉淀领域的解决方案；

## 发展

1. Serverfull时代：服务端运维都交给运维（工具人），纯人力处理，研发和运维隔离。
2. DevOps时代【目前】：重复性的工作（发布版本、日志采集）由自动化工具完成，出现问题研发通过DevOps平台处理，研发兼运维DevOps（server开始less）。
3. GitOps时代：代码自动化发布流水线：代码扫描-测试-灰度验证-上线，代码合并到Git分支即可，研发也不需要运维（serverless）。
4. NoOps时代：优化基础架构、提供更智能、更节省资源、更周到的服务，研发完全依赖Serverless，做好业务提升价值。

## 定义

Serverless要让运维工作彻底透明化，让研发只关心业务逻辑，不用关系部署运维和线上的各种问题。Serverless 对 Web 服务开发的革命之一，就是极度简化了服务端运维模型，使一个零经验的新手，也能快速搭建一套低成本、高性能的服务端应用。

- 狭义 Serverless = Serverless computing 架构 = FaaS 架构 = Trigger（事件驱动）+ FaaS（函数即服务）+ BaaS（后端即服务，持久化或第三方服务）= FaaS + BaaS
- 广义 Serverless = 服务端免运维 = 具备 Serverless 特性的云服务

![Serverless定义](/images/serverless-defination.png)

### FaaS

函数即服务（也叫作Serverless Computing），可以随时随地创建、使用、销毁一个函数。

> 通常函数的使用过程：
>
> 1. 先从代码加载到内存（实例化），
> 2. 然后被其它函数调用时执行。

在 FaaS 中也是一样的，函数需要实例化，然后被触发器 Trigger 或者被其他的函数调用。

二者最大的区别就是在 Runtime（函数的上下文），函数执行时的语境。

FaaS 的 Runtime 是预先设置好的，Runtime 里面加载的函数和资源都是云服务商提供的，我们可以使用却无法控制（一个函数只要参数固定，返回的结果也必须是固定的）。FaaS 的 Runtime 是临时的，函数调用完后，这个临时 Runtime 和函数一起销毁。FaaS 的函数调用完后，云服务商会销毁实例，回收资源，所以 FaaS 推荐无状态的函数。

> 在MVC架构中，Controller层就是函数的典型应用场景。一个 HTTP 的数据请求，就会对应一个 Control 函数，完全可以用 FaaS 函数来代替 Control 函数。在 HTTP 的数据请求量大的时候，FaaS 函数会自动扩容多实例同时运行；在 HTTP 的数据请求量小时，又会自动缩容；当没有 HTTP 数据请求时，还会缩容到 0 实例，节省开支。

![MVC架构中的Controller层](/images/MVC-Controller.png)

### BaaS

Runtime 不可控，FaaS 函数无状态，函数的实例又不停地扩容缩容，如何持久化存储一些数据，也就是MVC 里面的 Model 层怎么解决？

> BaaS 是一个集合，是指具备高可用性和弹性，而且免运维的后端服务，就是专门支撑 FaaS 的服务。

MVC 架构中的 Model 层，就需要用 BaaS 来解决。Model 层以 MySQL 为例，后端服务最好是将 FaaS 操作的数据库的命令，封装成 HTTP 的 OpenAPI，提供给 FaaS 调用，自己控制这个 API 的请求频率以及限流降级。这个后端服务本身则可以通过连接池、MySQL 集群等方式去优化。

![MVC架构中的Model层](/images/MVC-Module.png)

狭义Serverless：基于 Serverless 架构，可以把传统的 MVC 架构转换为 BaaS+View+FaaS 的组合，重构或实现。

广义Serverless（服务端免运维）：

1. 无需用户关心服务端的事情（容错、容灾、安全验证、自动扩缩容、日志调试等等）。
2. 按使用量（调用次数、时长等）付费，低费用和高性能并行，大多数场景下节省开支。
3. 快速迭代 & 试错能力（多版本控制，灰度，CI&CD 等等）。

## 痛点

虽然 FaaS 最擅长是事件响应，但是如果可以改造 Serverless 应用，无疑 FaaS 的附加值会更高。FaaS 在 Serverless 应用过程中切实存在的痛点：

1. 首先，调试困难，每次在本地修改和线上运行的函数都无法保持一致，而且线上的报错也很奇怪，常见的错误都是超时、内存不足等等，根本无法用这些信息去定位问题。
2. 其次，部署麻烦，每次改动都要压缩上传代码包，而且因为每个云服务商的函数 Runtime 不一致，导致很难兼容多云服务商。
3. 最后，很多问题客服无法跟进，需要自己到处打听。

各大云服务商都会提供命令行调试工具，例如阿里云提供的"fun"工具、腾讯云合作的 Serverless Framework 等等。不过云服务商提供的工具，都是云服务商锁定的。

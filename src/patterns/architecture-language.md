# 架构设计语言

如《[领域特定语言设计技巧](https://www.phodal.com/blog/step-by-step-domain-specific-language-design/)》一文中所描述的过程，在这个上下文之下就是：

1.  定义呈现模式。寻找适合于呈现架构的方式，如 UML 图、依赖图、时序图等。
2.  提炼领域特定名词。一系列的架构相关元素，如架构风格：微内核等、架构分层：MVC 等。
3.  设计关联关系与语法。如何以自然的方式来关联这些架构元素，如关键词、解析占位符等。
4.  实现语法解析。除了实现之后，另外一种还要考虑的是：如何提供更灵活的扩展能力？
5.  演进语言的设计。版本迭代

# Fklang 示例

过去的几个月的业余时间里，一直在设计一个名为 Fklang （ <https://github.com/feakin/fklang> ）的架构 DSL，以 DDD（领域驱动设计）为指导思想构建，除了完成 MVP 原型的编译器与代码生成，还可以使用 Jetbrains IDE 开发（搜索 Feakin）。

首先，架构描述语言或者设计语言并不是一个新的东西，Fklang 也是旧瓶新装。我们只是按自己的理解去实现了一遍，只是在实现的过程中，我们发现：**基于标准化的方法论，可以实现规模化的软件开发**。为此，在开发 Fklang 的过程中，便尝试结合了 “大魔头” 的类型流（[Typeflow](https://zhuanlan.zhihu.com/p/341089716)）思想，便也以**软件开发工业化**作为 Fklang 的目标之一。只是呢，对于 Fklang 而言，要实现开发工业化，还需要对于基础设施做一系列抽象（后面详细展开）。这也就是为什么文章的标题是探索。

TL；DR 版本：立即开始你的吐槽之旅途：<https://book.feakin.com/quick-start> 。

## 引子 1：Fklang 缘由：ArchGuard 架构治理前移

我们开发 Fklang 的初衷是，为了实践在 [ArchGuard](https://github.com/archguard) 中定义的三态模型中的设计态。对应三态如下：

* 设计态：目标架构。通过 DSL（领域特定语言） + 架构工作台来构建 。
* 开发态：实现架构。关注于：可视化 + 自定义分析  + 架构治理。
* 运行态：运行架构。结合  APM 工具，构建完整的分析链。

在 ArchGuard 中，我们关注于对开发态的治理，而其中的手段之一是：**规范工具化**。规范本身是应该内建的，诸如于我们应该制定好分层架构，诸如于 DDD 分层模式。并将这个分层架构与代码实现相绑定，再结合到开发工具中。诸如于 Fklang 的 `layer` 分层语法便是基于这个理念设计的：

```javascript
layered DDD {
  dependency {
    interface -> application
    application -> domain
    interface -> domain
    application -> infrastructure
    interface -> infrastructure
  }
  
  layer interface {
    package: "com.example.book";
  }
  ...
}
```

在与 IDE 结合的情况下，我们就能在开发的过程中，避免开发人员破坏分层架构。这便是 Fklang 的第一个设计理念：**显性化意图设计**。

## 引子 2：领域驱动设计的标准化方法

在设计 Fklang 的过程中，我们也探索了一系列的架构描述语言，它们都有自己的标准方法论。与此不同的是，我们觉得采用现行的标准化方法，才能让架构语言更容易落地。考虑到，现在更流行的架构设计方法论是 DDD 模式，在进行 [DDD 建模工作坊](http://domain-driven-design.org/zh/ddd-design-workshop-guide.html) 时，采用事件风暴或者其它方法，都是通过协作设计的方式进行的，而最后需要一个规范化的输出。

Fklang 便是承载了规范化输出部分，将图形设计代码化，将与实现代码相结合（如代码生成等）。在设计 Fklang 的 DDD 部分语法，我们参考了 [ContextMapper](https://contextmapper.org/docs/context-map/) 部分（主要也是设计不出差异），示例如下所示：

```javascript
ContextMap TicketBooking {
    Reservation -> Cinema;
    Reservation -> Movie;
    Reservation -> User;
}

Context Reservation {
  Aggregate Reservation;
}

Context Cinema {
  Aggregate Cinema;
}
```

通过 Feakin 在线工具（<https://online.feakin.com/>），可以将上述的 DSL 显性化出来，用于与架构师和开发人员进行交流。

PS：因为 Fklang 还没有实现完整的类型系统，所以在现在的实现是与 DDD 相绑定。

## 引子 3：实现细节与基础设施抽象

五年前，在编写《[Serverless 应用开发指南](http://serverless.phodal.com/)》时，我便觉得 Serverless 对于规模型团队来说，并不是一个很好的解决方案。但是呢，它提供了一个非常好的架构思考方式：**对实现细节的抽象化**。所以，再回到 Bob 大叔对于《架构整洁之道》的【第 6 部分：实现细节】：

* 数据库是实现细节
* Web 是实现细节
* 应用框架是实现细节

然后呢，然后呢，我们需要一个渐进式的 Darklang（<https://darklang.com/>），它与框架、Web、数据库无关。我们在写代码的时候，往往只会配置过一次数据库，剩下的数据库操作可能是在**删表与重建**。也因此，在描述数据库时，我们要配置的应该是 env，配置怎样的数据库，怎么的 http server 等等。Fklang 示例如下：

```javascript
env Local {
  datasource {
    driver: postgresql
    host: "localhost"
    port: 5432
    database: "test"
  }

  server {
    port: 9090;
  }
}
```

既然，我们在设计阶段已经定义好了 Context、Aggregate、Entity 等等，那么这个时候它是不是就可以作为一个 Web Server 运行起来呢？如下图所示：

这便引发了我们对于软件开发工业化的思考。

## 软件开发工业化：定义下一代架构

虽然，现在我们并没有在 Fklang 中实现真正的软件开发工业化。但是呢，我想在这里分享一下探索过程中的一些理解：

> 软件开发工业化是一种批量式加工的软件模式，充分利用机器的学习能力与人工设计的智慧，以在部分工序上由机器取代人，进而实现软件开发的快速规模化。

简单来说，对于每个功能而言，开发人员接收到需求之后，只需要编写**对应函数**中的功能，剩下的交由 AI 自行去生成与判断。诸如于，我们要新添加一个创建待办事项的 API，那么就自动生成 Controller、Repository 的代码，开发人员只需要编写 Service 中的那个对应 `createTodo` 方法即可。至于重构嘛，我觉得也不需要，发现重复代码之后，AI 应该自动帮你重构。

基于这样的考虑，我们觉得实现工业化应该达到三个核心点：

* 设计与实现细节分离。只编写核心业务逻辑，无需关注于所有的输入和输出，如数据库、Web API 等。
* 全生命周期半智能化。对于每一个环节进行量化，便可以实现架构进行自调节。
* 模式内建于工具。工业化意味着标准化，也意味着知识的固化到系统中，模式是软件开发的核心知识，应该由工具来继承。

而一旦实现了架构治理的前置，我们便不再需要关注于如何治理。

### 设计与实现细节分离：基础设施抽象

在这一点上，我们与传统的 “甩手架构师” 是保持一样的考虑，设计与一部分的实现细节是相分离的。只是核心的差异之处在于，我们作为架构师，应该构建出基础设施抽象，并显示化出设计意图，诸如于我们前面强调的《实现细节与基础设施抽象》。

我们可以拿 Serverless 架构或者 Faas（函数即服务）作为一个参照物。在采用 FaaS 架构的模式之下，我们并不关注于基础设施，云厂商或者基础设施部门会提供弹性的架构支持。但是呢，FaaS 粒度过细，过多的进程使得我们无法接受这个成本。所以，在考虑工业化时，我们需要实现：**基于微服务之上的函数即服务（FaaS on Microservices）**。从模式上类似于 OSGI 模型，只是实现机制是不一样的。我们理解的类似 **FaaS on Microservices** 的架构，可以每个聚合（aggregate ）在开发时独立运行，又可以与其他聚合一起工作。

而作为开发人员，他们不需要关注于 Web 接口与数据库接口，只需要编写核心业务逻辑即可，Controller 和 JPA 接口可以由设计生成，以达到框架、数据库与设计无关。简单来说，开发人员只需要编写**处理函数**就足够了，应对于 MVC 的 service 中的一个方法，处理输入并返回输出结果。

### 全生命周期半智能化：量化与架构自调节

我们坚信软件开发工业化另外一个点在于：**基于代码模型的 AI 代码生成**。

* 设计态。通过在 DSL 中写入基本的设计，来生成代码与空函数，让开发人员选择与填空。
* 开发态。结合 AI 与现有的代码库能力，来判断逻辑是否正确，并进行调整。
* 运行态。通过监测 API 的运行情况，来自动调整 DDD 中的聚合、限界上下文。

在设计  Fklang 的过程中，我们构建了一个 flow 语法，它是用来生成注释给 AI 看的：

```javascript
impl UserCreated {
  endpoint {
    POST "/user/{id}";
  }

  flow {
    via UserRepository::getUserById receive user: User
    via UserRepository::save(user: User) receive user: User;
    via Kafak send User to "user.create";
  }
}
```

其设计思想来源于，我们日常沟通中的，你这个 API 需要先查询哪个表，再 xxx，最后再 xxx。虽然，设计得还比较粗糙，重点还在于输入和输出，在配置了分层之后，会在对应的 Controller （UserController）中插入对应的代码：

```javascript
        @PostMapping("/user/{id}")
        public User createUser() {
            // 1. get user:User from UserRepository.getUserById with ()
            // 2. get user:User from UserRepository.save with (user:User)
            // 3. send User from Kafak to "user.create"
        }
```

在引入 GitHub Copilot 之后，便可以自动生成靠谱，还有不靠谱的代码。

除此，既然 AI 训练可以实现由 AI 自行调参，并发方面是不是应该自行有机器来学习和设计？而要实现自调节要做的第一件事就是量化，定义成功，以反馈驱动设计。

### 模式内建于工具

工业化与手工作坊的区别在于，手工作坊做出的东西品质差异比较大，好的非常好，差的非常差。而工业化，虽然比不上好的，但是至少品质如一。他们所做的事情便是，将模式化的套路内建于机器的流水线之中。对于软件开发来说，也是相似的：

* 采用诸如于 DDD 的 “标准化” 设计方法。
* 采用统一的开发语言。
* ……

这其实也是每个公司想去做提效的部分。从工业化的角度来说，这里我们认为：复用、效率与规模化是类似于 CAP 的不可能三角。在设计 [Uncode](https://github.com/inherd/uncode/) 我们一直想做的事情是：将软件开发过程代码化，以实现自动化。相似的，对于工业化来说，我们也需要在现有的工具中，实现对于这些成熟模式的集成。

从另外一个角度来说，我觉得现有的 IDE 依旧有很大的改进空间。诸如于，既然大部分开发人员不用 TDD，那么 IDE 在接受了 debug 之后，是不是可以记下参数，自动生成测试？

## Fklang：DDD 驱动的架构 DSL

最后，让我们简单再介绍一下 Fklang，一个由 DDD 思想驱动的架构设计语言。

### 核心设计理念

Fklang 的目标是：通过声明式 DSL 来绑定代码实现与架构设计，保证架构设计与实现的一致性。为此，我们有三个核心设计理念：


1. 架构孪生：双态绑定。提供架构设计态与实现态的双向绑定，保证架构设计与实现的一致性。
2. 显性化设计意图。将软件设计的意图化，借助于 DSL 语言的特性，将意图转换化代码。
3. 类型与事件驱动。通过事件驱动的方式，将数据类型与领域事件进行绑定。

PS：详细介绍见：<https://book.feakin.com/design-principles> （还没写完）

大部分的内容已经在上面的内容里介绍了。回到 Fklang 中，我们面临的第一个挑战是：如何在不影响开发效率的前提下，保证架构设计与实现的一致性？对于一个架构语言来说，要让开发人员采用的一个关键点是：**如何真正地提升开发效率**？所以，这也就是我们依赖在探索的地方。

### Fklang 的其他效能探索

**基于 DDD 产物的 Mock Server**。既然 Fklang 能作为 DDD 设计结果的承载物，那么考虑到  API 设计也是其中的一部分，自然而然地 Mock Server  也是可以跑起来的 —— 读取 Aggregate、Entity 等生成  API。所以，一个 Mock Server 日志示例：

```javascript
fkl run --main /Volumes/source/feakin/fklang/docs/samples/impl.fkl --func mock-server
Running at http://localhost:9090 !
Routes: 
http://localhost:9090/api/cinema/cinema
http://localhost:9090/api/cinema/cinema/1
http://localhost:9090/api/cinema/screeningroom
http://localhost:9090/api/cinema/screeningroom/1
http://localhost:9090/api/cinema/seat
http://localhost:9090/api/cinema/seat/1
http://localhost:9090/api/movie/movie
```

**基于 API 的契约测试。**相似的，我们也将 API 契约作为测试的一部分，可用于测试 API 的实现是否是正确的，如下所示：

```javascript
impl UserUpdated {
  endpoint {
    PUT "/user/{id}";
    request: UpdateUser;
    response: User;
  }
}
```

不过，现在支持最好的是 GET 请求：

```javascript
[2022-11-20T08:58:39Z INFO  fkl] runOpt: RunOpt { main: "/Volumes/source/feakin/fklang/docs/samples/impl.fkl", path: None, impl_name: Some("PackageJsonGet"), env: None, func_name: HttpRequest, custom_func: None }
[2022-11-20T08:58:39Z INFO  fkl::builtin::funcs::http_request] headers: {"user-agent": "Mozilla/5.0 (Windows; U; Windows NT 5.1) AppleWebKit/533.2.1 (KHTML, like Gecko) Chrome/24.0.811.0 Safari/533.2.1"}
[2022-11-20T08:58:39Z INFO  fkl::builtin::funcs::http_request] Content-Type: text/plain; charset=utf-8
```

在未来，它也可以作为自动化测试的核心部分。

## 小结

尽管，在当前的版本里，我们使用的是 Rust + Pest 的方式开发。但是呢，在设计上我们已经趋近于 Kotlin DSL 的风格，方便于未来进行扩展。最后，再 Show 一下代码

* 文档地址：<https://book.feakin.com/>
* IDEA 插件下载：<https://plugins.jetbrains.com/plugin/20026-feakin/versions/stable/229113>
* Fklang 项目地址：<https://github.com/feakin/fklang>
* IDEA 插件源码：<https://github.com/feakin/intellij-feakin>

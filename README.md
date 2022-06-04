# ArchGuard Book

架构治理指南与实践

- [ ] 什么是架构
  - [ ] 架构的定义
- [ ] 架构的模型
  - [ ] 架构的风格
  - [ ] 架构的模式
  - [ ] 架构的规范
  - [ ] xxxx
- [ ] 什么是架构治理
  - [ ] 架构治理的定义
  - [ ] 架构治理的模型
- [ ] 什么是架构治理系统
  - [ ] 架构分析
  - [ ] 架构可视化

ArchGuard 相关

- [ ] ArchGuard 插件化
  - [ ] 插件化

# 架构建模：如何用代码描述软件架构


在架构治理平台 ArchGuard 中，为了实现对架构的治理，我们需要**代码** + **模型**描述所要处理的内容和数据。所以，在 ArchGuard 中，我们有了[代码的模型](https://www.phodal.com/blog/modeling-for-code/)、依赖的模型、变更的模型等，剩下的两个核心的部分就是**架构的模型**、**架构的治理模型**，其它的还有诸如[构建的模型](https://www.phodal.com/blog/abstract-build/)等，会在后续的过程中持续引入到系统中。

PS：本文里的架构展开是基于自动化分析需求的，模型也是基于这个动机出发的。

## 架构是什么？？

对单个语言的代码建模并不难，对于一个语言有特别的概念，如 package、class、field、function 等等。在有了明确概念的基础之下，结合我们的业务上的需求，就能构建一个太差不差的模型。在采用 DDD 这一类建模方式的时候，产生共识，提炼知识，形成概念等，便能构建出模型的雏形。

### 起点：架构是重要的元素

然而，对于架构来说，业内没有统一的定义。于是乎，诸如 Martin Fowler 喜欢引用 GoF（《设计模式》作者们） 之一的 Ralph Johnson 对于架构的描述：

> 架构是那些重要的东西……，无论它具体是什么。

同样的 Grady Booch （UML 的发明者之一）也是惟类似的方式来概括架构的：

> 软件架构是系统设计过程中的重要设计决定的集合，可以通过变更成本来衡量每个设计决定的重要维度。

所以呢，这让我们感觉说了等于没说，我们得去定义什么是**重要的东西**。而重要的东西，在不同人、不同场景之下，它是存在差异的。哪怕是同一个类型的软件，在不同的公司、不同的利益相关者的背景之下，重要的东西也尽相同。

### 原则：可是到底哪些是重要的？

于是乎，我再尝试去引用最新的架构相关的书籍，诸如于我编写这篇文章时，参考的书《软件架构：架构模式、特征及实践指南》一书中 Neal Ford 对于架构的定义：

> 软件架构中包含系统的结构、系统必须支持的架构特征、架构决策以及设计原则。**系统的结构**是指实现该系统的一种或多种架构风格（如微服务、分层和微内核等）。**架构特征**定义了系统的成功标准。**架构决策**定义了一组关于如何构建系统的规则。**设计原则**是关于如何构建系统的非必须遵循的指导原则。

从模型构筑的层面来说，书中的定义也提供了一个灵活性。诸如于在架构特征的定义里，关注的是各类能力（ability），如互操作性、可适用性、可测试性等等。

在现有的 ArchGuard 这个业务场景之下，我们难以自动化地识别出各类的特征。因为从实践的层面上来说，这些能力并不一定实现了，它是目标架构，可能还只存在于架构蓝图之上的。在这个层面上来说，这里定义的架构，偏向于是**设计层面**的定义。

从另外一方面来说，架构决策则是在架构治理的过程中，我们所关注的核心。可以在后续针对于这一系列的原则的规则，构建出一个描述架构特征的 DSL。

### 重要的元素：组件、边界与通信

接着，让我们再回到 Bob 大叔（Robert C. Martin）的《架构整洁之道》书中的定义：

> 软件系统的质量是由它的构建者所决定的，软件架构这项工作的实质就是**规划如何将系统切分成组件，并安排好组件之间的关系，以及组件之间互相通信的方式**。

再从 Clean Architecture 模式来说，Bob 大叔一直在强调的是：顶层抽象策略与底层实现要实现解耦。诸如于如何划定合理的边界？如何组合相关的策略与层次？从这个模式上来说，我们得到了一个越来越清晰的定义。

然而，我们还遇到一个更难的问题是，**如何定义一个组件是什么**？\*\*还有关系是什么？\*\*在书里的序言， Kevlin Henney（《面向模式的软件架构》卷4、卷 5的作者之一）给了一个更精确的描述词：**组织结构**（structure），从宏观到微观的构筑过程，其中的构件包含了组件、类、函数、模块、层级、服务等。

对于大型软件来说，其组织结构方式异常复杂，它像极了一个国家的层级关系，一级部门、二级部门等等。而部门之间又有复杂的关系，正是层级关系 + 层级的构件构建成了这个复杂的系统。（PS：而了让系统能良好的运行，即其中的组件（螺丝钉）按规则执行，则需要一个督察组织。）

### 层次结构：组件和关系

软件架构已经有了几十年的历史，我们已经用 ”模式“ 这一词对过去的架构进行了一系列的总结。二十年前，人们初步总结了《面向模式的软件架构》（POSA）。在这里，就引述 POSA 1 的第 6 章里，有一个完整的层级关系介绍：

> **软件架构**描述了软件系统的子系统和组件以及它们之间的关系。通常使用不同的视图来说明子系统和组件，以展示软件系统的功能特征和非功能特征。
>
> **组件**是被封装起来的软件系统的一部分，包含一个接口。组件是用于打造系统的构件。在编程语言层面，组件可能由模块、类、对象或一组相关的函数表示。
>
> **关系**描述了组件之间的联系，可能是静态的，也可能是动态的。静态关系会在源代码中直接显示出来，它们指出了架构中组件的布局；动态关系指出了组件之间 的临时关系和动态交互，可能不容易通过源代码的静态结构看出来。
>
> **视图**呈现软件架构的某个方面，展示软件系统的某些具体特征。
>
> ……

在今天来看，从模式上来说，软件架构本身并没有发生太大的变化。只是呢，一些定义发生了变化，诸如于组件和接口。在微服务架构风格流行的今天，一个微服务也可以视为一个组件，它包含了一系列的接口，对外提供了复用的能力。而用来描述它们的关系的元素，则不再是过去的函数调用，变为了远程调用、事件触发。

现在，我们有了一详尽的定义，在建模上，可能还欠缺一些元素，诸如于，如何分析出**组件间的关系**。

### 第 3 种架构视图：展示工程关注点

在 ArchGuard 中，我们使用了 C4 架构可视化模型作为一种参考视图。这种实现的方式主要是从分析和可视化的层面来考虑的。除了 C4 之外，另外一种主流的方式是 4 + 1 视图。顺带一提，在 4 + 1 的论文《Architectural Blueprints—The “4+1” View Model of Software Architecture》，同样也有一个描述架构的表示公式：`Software architecture = {Elements, Forms, Rationale/Constraints}`。

从通识的角度来看，采用 4 + 1 视图是一个比较理想的方式。只是，由于存在大量的 PaaS、IaaS 等 xx 即服务设计的不合理性，使得这些记录基础设计相关信息的代码，并没有与代码库一起存放，使得在辩识上存在一定的难度。

因此，从实现的层面来说，在这里，我们要引用的是《面向模式的软件架构》中，提到的《Software Architecture in Industrial Applications》（也可以参考《实用软件体系结构》一书）架构视图：

* 概念视图：描述了整个系统需求向整个体系结构的转化。
* 模块视图：描述了如何将系统划分成模块并将模块组织成层。
* 执行视图：描述了系统的动态元素以及它们之间的交互。
* 代码视图：描述了源代码的组织结构。

在这个视图的定义里，它更能清晰的划分开几个不同层面的考虑因素。采用作者们在最早的论文里提到的示例：

| 软件架构 | 使用示例 | 影响因素的例子 |
|----|----|----|
| 代码架构 | 配置管理, 系统构建、OEM 定价 | 编程语言，开发工具和环境，扩展子系统 |
| 模块架构 | 模块接口管控、变更影响分析、接口约束一致性检查、配置管理 | 使能软件技术、组织结构、设计原则 |
| 执行架构 | 性能和可调度性分析，系统的静态和动态配置，将系统移植到不同的执行环境 | 硬件架构、运行时环境性能标准、通信机制 |
| 概念架构 | 使用特定领域的组件和连接器进行设计、性能评估、安全性和可靠性分析、了解系统的静态和动态可配置性 | 应用领域、抽象软件范式、设计方法 |

从表格的右边里，我们就可以直接对应到系统所需要的每个层面的设计因素，诸如于编程语言等元素放在代码架构上。换句话来说，在微服务、单体架构下，都能找到自己合适的位置。

### 概念的最后：描述模型的类型系统

最后，为了保证本文在概念的完整性，我们还需要一种方式来描述这个系统种的模型和一系列的概念，从形式上来说，它是一个类型系统。诸如于，我们在 UML中所表示的 （PlantUML 表示方式）：

```javascript
class Architecture {
   Component[] components
   System[]    subSystems
   Relation[]  relations
   ArchStyle   archStyle
   Rule[]      archRules
   ...
}
```

一个用来描述类型的系统，就是一个类型系统，和编程语言里的类型是等同的。它可以用来解释一系列的概念，以及概念之间如何连接。

顺带一题，如果我们把编程语言看作是一个系统，那么我们就会发现其在设计的有趣之处。类型系统与结构体（或者类）可以用于构建系统中的概念，一个个的表达式则是用于构建概念之间的关系。

## 建模

对于概念来说，哪怕是有了如此多丰富的展开，要做一个小结也并非一件容易的事情。更何况于模型而言，它是数值上的标准化，一种接近于通用的模型形态。

### 设计：第一个版本的架构模型

所以，我的第一个尝试是从 《面向模式的软件架构》的定义下手：

```javascript
class SystemArchitecture(
    val archStyle: ArchitectureStyle,
    val subSystem: List<SubSystem>,
    var components: List<ArchComponent>,
    val connections: List<ArchConnection>
)
```

示例：对于一个系统来说，它存在一个主的架构模式，如微服务架构。而在每个微服务相当于是一个子系统或者说组件。当然了，一个子系统可以包含多个微服务。而对于个组件来说，它包含了输入和输出，以及一系列的计算逻辑。所以，它的一个模型可能是这样的：

```javascript
class ArchComponent(
    val name: String,
    val type: ArchComponentType,
    val inbounds: List<String>,
    val outbound: List<String>,
    val components: List<ArchComponent>
)
```

而麻烦的一点在于，组件是动态的，组件之间是存在关系的，组件内部也存在关系。所以，我们还需要考虑如何表示组件间的关系？

```javascript
class Connection(
    val connectors: String,
    val source: String,
    val target: String,
    var connectionType: ConnectionType,
    val connectorStyles: ConnectorStyle,
)
```

所以，如果以宽泛的定义来看，组件其实是树形结构的。同样的，对于架构来说，这种 Connection 也是存在的。根据上面的一系列形态展开，我们就能得到一个初始版本的架构的架构模型。

更多模型相关内容，详见 ArchGuard Scanner 中的初始版本 [Architecture.kt](https://github.com/archguard/scanner/blob/master/analyser_architecture/src/main/kotlin/org/archguard/architecture/core/WorkspaceArchitecture.kt) 。


### 实现思路：生成架构模型

ArchGuard 中的模型是通过代码来生成的。在这种业务场景之下，在构建模型的时候，需要平衡设计与实现。由此，基于代码分析的视图方式更适用我们。从代码和依赖中，我们可以：

* 基于目录结构分析出分层关系，进而得到代码的结构视图。
* 分析依赖管理工具，进而得到一个模块的视图。
* 分析依赖的软件，如 Spring、Dubbo 等，进而得到一个执行的视图。
* 分析软件中的模型，进而得到概念视图。

结合之下，我们就能构建出一个更完整的架构模型。

### 实现：放弃第一个版本的模型

设计挺美好的，但是当我开始写第一行代码的时候，发现不对劲了：我们有了一个分析的模型，但是输入呢？

仅从项目的分析来说，我们拿到的只是一个代码仓库的代码。一个代码仓库可能是一个模块，一个系统，又或者是一个服务。所以，我们的输入是什么？基于已有的分析情况，如项目依赖（依赖管理工具）、源码结构、语言等？我们缺少构建一个项目所需要的上下文。

我们从源码所能分析到的内容如下所示：

```kotlin
data class PotentialExecArch(
    var protocols: List<String> = listOf(),
    var appTypes: List<String> = listOf(),
    var connectorTypes: List<ConnectorType> = listOf(),
    var coreStacks: List<String> = listOf(),
    var concepts: List<CodeDataStruct> = listOf()
)
```

对应的一些分析细节：

1. 协议信息。解析源码和 Gradle、Maven、NPM 中的依赖信息，如含 Dubbo 就视为带 RPC；如带 `java.io.File` 就认为带 FileIO
2. 应用类型。解析依赖信息，如包含 `clikt` 就视为 Kotlin 的 CLI 应用。
3. 连接类型。同上
4. 核心技术栈。根据依赖类型分析。
5. 概念模型。需要先分析潜在的分层架构模型，根据分层架构模型，就能得到概念集。

当然了， 这部分代码还没写完，如果有兴趣，也欢迎来加入我们。

### 多种架构模型

于是，在 ArchGuard 的背景下，“架构” 的架构模型有多种形态的:

- 扫描形态下的架构模型（对应到 ArchGuard Scanner）。即，从各个项目的源码中得到的架构模型，从源码、依赖、数据库等中计算出来。
- 分析形态下的架构模型（对应到 ArchGuard Backend）。基于分析形态下的架构模型，构建出包含架构相关知识的架构模型。
- 展示形态下的架构模型（对应到 ArchGuard Frontend）。结合可视化的知识，展示出对应的架构模型。

每一个模型都是在上一步的基础上添加了领域知识，那么什么是领域知识呢？如何做好架构？

## 小结：挑战

模型，一个开始，它需要不断地演进才能适用需要。而有了一个凑合能用的模型，只是这一系列工作的开始，我们还会遇到一系列的挑战。

### 描绘目标架构

系统中的架构反应的只是现状，如何去描述未来的架构，并将两者进行匹配，又是一个非常有意思的话题。

### 衡量变化性

我们还将面临的另外一个问题是，软件架构并非是不变的。

软件只要一直在开发，就会以细微地方式变化着。从宏观的层面来说，尽可能架构师都在努力地不去大范围地变动结构。而我们需要面对于这些挑战，诸如于基础设施变化，而这种变化带来的是临时性的中间状态。

如何去表示这种临时性的中间状态，就变得更加有意思。

在这里只开始了迈出了第一步，如果大家有兴趣，欢迎来 ArchGuard 为技术建模：[https://github.com/archguard/archguard/discussions/67](https://github.com/archguard/archguard/discussions/67)

# 架构即代码：编码下一代企业（应用）架构体系


> 架构即代码，是一种架构设计和治理的思想，它围绕于架构的一系列模式，将架构元素、特征进行组合与呈现，并将架构决策与设计原则等紧密的与系统相结合。

如我的上一篇文章《[为“架构”再建个模：如何用代码描述软件架构？](https://www.phodal.com/blog/architecture-model/)》中所说，要准确描述软件的架构是一件颇具难度的事情。仅就实现的层面来说，也已经很难通过一个标准模型来让所有人达成一致，“哦，这就是架构”。也因此，在无法定义架构的情况下，也很难无法给出一个让所有人信服的架构治理模型。毕竟：**模型只有合适的，永远没有对的**。

但是呢，我们（ArchGuard Team）依旧会在 ArchGuard 构建出一个架构模型，以及架构治理模型，作为推荐的 “**最佳实践**”。除此，我们还应该提供一种**自定义**企业应用架构的可能性，这就是**架构即代码**。面向初级架构师来说，他们只需要按照 ArchGuard 的最佳实践来实施即可；面向中高级架师，他们可以基于 ArchGuard 提供的插件化能力 + DSL 构建自己的架构体系。

所以，如你在其它系统中所看到的那样，要提供这样的能力，需要一定的编码、配置等。所以，我们就需要构建一个**架构即代码**的系统。那么，问题来了，即代码又是什么鬼。

## 架构即代码是什么？

在先前的一系列的代码化（<https://ascode.ink/>）文章中，描述了如何将软件开发完全代码化，包含了将文档、需求、设计、代码、构建、部署、运营等变成代码化。设计和实现一个领域特定语言并不难，如《[领域特定语言设计技巧](https://www.phodal.com/blog/step-by-step-domain-specific-language-design/)》一文中所描述的过程，在这个上下文之下就是：


1. 定义呈现模式。寻找适合于呈现架构的方式，如 UML 图、依赖图、时序图等。
2. 提炼领域特定名词。一系列的架构相关元素，如架构风格：微内核等、架构分层：MVC 等。
3. 设计关联关系与语法。如何以自然的方式来关联这些架构元素，如关键词、解析占位符等。
4. 实现语法解析。除了实现之后，另外一种还要考虑的是：如何提供更灵活的扩展能力？
5. 演进语言的设计。版本迭代

也因此，我们将架构即代码定义为：

> 架构即代码，是一种架构设计和治理的思想，它围绕于架构的一系列模式，将架构元素、特征进行组合与呈现，并将架构决策与设计原则等紧密的与系统相结合。

接下来的问题就是，如何将这个理念有机的与系统结合在一起？并友好地提供这样的 API 接口（DSL）？

于是放到当前 ArchGuard 的 PoC，架构即代码的呈现方式是 “ArchDoc”，一种基于 Markdown 的交互式代码分析和治理方式。即所有的 “代码” 都通过 markdown 来管理，优点有一大堆：

* 使用内嵌 DSL （用语法块管理）表述架构
* 可以记录系统的架构文档，如架构决策、业务架构等
* 拥有广泛的解析库，能提供更灵活的定制灵感（Ctrl + C, Ctrl + V）。
* 自定义 Render
* 广泛的编辑工具支持

唯一的缺点就是实现这样一个工具并不简单。

## 架构即代码的特点

不过，我们已经实现了一个简单的 PoC（概念证明）版本，在这个版本里，它的特点是：

* 显式地描述与呈现架构。
* 架构文档即是规则
* 设计、文档与实现一致

当然了，还有各种的可扩展能力（这是一个再普通不过的特点了）。

### 显式地描述与呈现架构

回到日常里，我们经常听架构师说，“我们的服务采用的是标准的 DDD 的分层架构”。但是，这个分层是不是诸如于 “Interface 层依赖于 application、domain、infrastructure 层” 等一系列的依赖关系？开发人员是否知道这些规则？这些都是问题。所以，一个架构即代码的系统，它应该能显式地呈现出系统中的那些隐性知识。

诸如于，我们应该将分层中的依赖关系，显式地声明写出来：

```javascript
layered {
    prefixId("org.archguard")
    component("interface") dependentOn component("application")
    组件("interface") 依赖于 组件("domain")
    component("interface") dependentOn component("infrastructure")

    组件("application") 依赖于 组件("domain")
    组件("application") 依赖于 组件("infrastructure")

    组件("domain") 依赖于 组件("infrastructure")
}
```

PS：请忽视上面 Kotlin 代码中的中文元素，它只是用来说明使用中文描述的可能性。毕竟，开心的话，也可以使用文言文。

结合 ArchGuard 中的 DSL 与可视化工具（这里采用的是 Mermaid.js），就能呈现我们所设计的分层架构：

 ![](/processor/blog/images?file_name=2022-05-14T12:01:03.999Z.svg)

再再结合一下设计的分层 Linter 工具（正在实现中）：

```javascript
linter('Backend').layer()
```

一旦分层中的依赖关系错了，就可以在持续集成中阻断这些代码的提交 —— 类似于 ArchUnit 这样的机制。稍有区别的是，你不需要将测试和代码放在代码库中，而是可以统一的去管理它们。

而对于其它一系列的更复杂的规则来说，我们可以自定义它们，并将他们与文档结合在一起。

### 架构文档即是规则

在这种模式之下，我们还可以将文档与代码相结合 —— 前提是：我们已经编写了一系列的规则。如我们在 ArchGuard 中，针对于不同的场景编写了一系列的规则：

* SQL，如不允许 `select *` 等
* Test Code，用于检测代码中的坏味道
* Web API ，分析 API 的设计是否 RESTful
* Layer （待实现），分析代码中的分层实现
* Arch （待实现），类似于 [ArchUnit](https://github.com/TNG/ArchUnit) 或者 [Guarding](https://github.com/modernizing/guarding) 制定更细的依赖规则
* Change（待实现），编写自定义的变更影响范围规则，如某个类不应该被其它的变更影响到

有了基本架构文档规范之后，我们可以规则化它们，并结合到一起。如下是一个结合 Checklist 和规则的列表示例：

```
- [x] 不应该存在被忽略（Ignore、Disabled）的测试用例 (#no-ignore-test)
- [ ] 允许存在重复的 assertion (#redundant-assertion) 
```

`#no-ignore-test` 对应于正在实现的 ArchGuard 中的规则，而 GFM 的 Checklist 中，如果 check 了，则可以表示为开启规则；如果没有 check，则为不开启。前面的文字部分，则是对应的规则描述，与传统的 linter 相比较，略显灵活。

而不论是编写文档还是阅读文档的人，他们可以很轻松地构建起对应的上下文。

### 设计、文档与代码一致

有了设计和文档之后，就需要结合到已有的代码中，让三者保持一致和准确。在我们的场景之下，就是 ArchGuard 已有的 API，它包含了：


1. 创建对于代码仓库的分析
2. 分析代码的语法和构建工具、变更历史等
3. 分析代码是否满足规则等

如下是 ArchGuard 中对于 repo 设计的 DSL（基于 Kotlin），用于创建代码仓库的分析：

```javascript
repos { 
    repo(name = "Backend", language = "Kotlin", scmUrl = "https://github.com/archguard/archguard")
    repo(name = "Frontend", language = "TypeScript", scmUrl = "https://github.com/archguard/archguard-frontend")
    repo(name = "Scanner", language = "Kotlin", scmUrl = "https://github.com/archguard/scanner")
}
```

只有三者保持了一致，我们才能确保架构的设计与实现是一致的。

## 架构即代码是个什么系统？

从实现的层面来说，一个架构即代码系统是一个支持编排的数据系统。原因在于，我们并不想关心数据处理的过程，但是想获取数据的结果，从结果中获取洞见。正如，我们所见到的一个个大数据系统，构建了一个个的可视化能力，以祈祷从中得到洞见。

不过，和祈祷稍有不同的是，我们是带着 N% 可能性的猜想，所以叫做探索。

### 一种探索式的架构治理

传统的软件开发模型是：**编辑-编译-运行**（edit-compile-run），这种开发模型的前提是，我们拥有足够的业务洞见。对于一个带着丰富领域知识的业务系统来说，构建这样一个系统并不是一件困难。但是，当我们缺乏足够的领域专家，我们应该如何往下走呢？复杂问题，你只能探索 (Probe)  -> 感知 (Sense) -> 响应 (Respond)。

而既然我们本身和很多新生代的架构师一样，也需要探索，也需要分析，然后才是得到结论。那么，我们不妨再尝试切换一下模式。如同，我们构建 ArchGuard 的软件开发模型，也是**执行-探索**（execute-explore），先从分析一下系统（发布一个分析功能），再配合已有的模式，最后得到 “结论” 或者规则（再发布一个 linter 功能）。

在数据领域，这种方式相当的流行，过去人们用 IPython，现在都改用 Jupyter；另外一个类型则是类似于 RMarkdown 提供的报表式的思路。

* IPython。 is a command shell for interactive computing in multiple programming languages.
* Jupyter Notebook. is a web-based interactive computing platform.
* R Markdown。Turn your analyses into high quality documents, reports, presentations and dashboards with *R Markdown*.
* D3.js 社区的 Observable。用于 Explore, analyze, and explain data. As a team.

从模式上来说，ArchGuard 更偏向于 RStudio 的模式，只是从社区的资源上来说，Jupyter 相关的实现比较多。

### 一个经常 OOM 的 “大数据系统”

在我们（ArchGuard core team）的 “数次讨论” 中，最终认为 ArchGuard 是一个大数据分析，而不是简单的数据分析。原因是系统中存在大量的 bug 和大数据相关的（狗头）：

* 存在一定数量的 Out of Memory。
* 大数据量情况下的可视化优化。

也就是所谓的 ”bug 驱动的架构设计“。

除此，之后另外一个颇有意思的点是，对于更大型的系统来说，它存在大量的新的提交，又或者是新的分支。我们即需要考虑：**应对持续提交的代码，构建增量分析的功能**。

当我们尝试使用大数据的思路，如 MapReduce、Streaming Analysis 相关的模式来解决相关的问题时，发现它是可以 work 的不错的 —— 毕竟都是数据分析。

## 在 ArchGuard 是如何实现的？

ArchGuard 围绕于 DSL + Kotlin REPL + 数据可视化，构建了一个可交互的架构分析与治理平台。因为还在实现中，所以叫下一代。

### 1. 提炼架构元素

上文中的（<https://ascode.ink/>）系列中，也包含了两个架构相关的工具，一个是代码生成 DSL：[Forming](https://github.com/inherd/forming)、另外一个则是架构守护 DSL：[Guarding](https://github.com/modernizing/guarding)。两个 DSL 所做的事情是，围绕**特定的规则**将**架构元素**组合到一起，这里的架构元素。

如果没有做过，这一个过程看上去是挺麻烦的，实现上有一些颇为简单的东西可以参考（复制）：

* 架构描述语言论文（ADL）。ADL 已经是一个很成熟的领域了，在设计模式火的那个年代，架构模式（《面向模式的软件架构》）也特别的火。
* 架构相关书籍的目录。一本好的架构书，只需要看目录就能有个索引，所以也就有了基本的架构元素。
* 架构的模式语言。模式语言所呈现的是模式之间的关系
* ……

仅仅是复制那多没意思，要是能自己做做抽象，也是一种非常好玩的事情。

### 2. 构建插件化与规则分析

如上所述，在 ArchGuard 中，我们尝试以一系列的规则，构建系统的规则，而这些规则是以插件化的形式暴露的。

这就意味着，这样一个系统应该是支持自定义的插件化能力，它即可以让你：


1. 接入一个新的语言
2. 编写新的规则
3. 构建新数据的 pipeline

在 ArchGuard 中还需要改进的是，提供一种元数据的能力。

### 3. 抽象 DSL 作为胶水

从实现层面来说，为了支撑粘合的能力，我们目前计划设计了三种能力的 DSL：**后端架构查询 DSL、架构 DSL、特征 DSL**。

**后端架构查询 DSL**

类似于 LINQ （Language Integrated Query，语言集成查询）封装 CRUD 接口，以提供编译时类型检查或智能感知支持，在 Kotlin 中有诸如于：[KtOrm](https://github.com/kotlin-orm/ktorm) 的形式。如：

```javascript
database
    .from(Employees)
    .select(Employees.name)
    .where { (Employees.departmentId eq 1) and (Employees.name like "%vince%") }
    .forEach { row -> 
        println(row[Employees.name])
    }
```

像一个编程语言编写，可以提供更友好的语法性支持。

**架构 DSL**

即架构描述语言（Architecture Description Language），以提供一种有效的方式来描述软件架构。

**特征 DSL：分析、扫描与 Linter**

即封装 ArchGuard Scanner、Analyser、Linter 等，用于构建系统所需要的基础性架构特征。

### 4. 构建可交互的环境

两年前，在与众多的 Thoughtworker 一起构建 [Ledge](https://github.com/phodal/ledge) 的时候，我们就一直在强调[文档代码化](https://www.phodal.com/blog/document-as-code/)，并提供可交互的文档环境。在 Ledge 里，你可以使用 Markdown 来绘制各类的图表，只需要借助声明图表类型，示例见：<https://devops.phodal.com/helper> 。

从模式上来说，ArchGuard 更像是一个  RStudio + Jupyter 的结合版，即提供了大量自定义图形 + 组件能力的 REPL。

在 REPL 上，由于我们计划使用 Kotlin 构建 DSL，所以需要寻找的是 Kotlin 的 REPL。Kotlin 官方创建的 [kotlin-jupyter](https://github.com/Kotlin/kotlin-jupyter) 便成为了一个很好的参考，可惜还没有用得上。与此同时，Kotlin 在设计初期就有了 Kotlin Scripting 的场景，所以其实 `kotlin-scripting-compiler-embeddable` 就能满足需求。于是，在 PoC 里，我们参考了 Apache Zeppelin 引入了 Kotlin REPL，并创建了一个 WebSocket 作为服务。

在可视化上，稍微复杂一些，需要构建一个 Markdown 解析器、Block 编辑器等。我们暂时采用了 Mermaid.js 作为可视化的图形库之一，另外的还有 D3.js、Echarts 也是其中之一。剩下的问题，便是如何通过 DSL 来整合它们？构建前后端的数据模型是一个临时的方案？


# “分布式” 开发规范治理

PS：本文只是先开个头，思考如何应对这种挑战。

如果只是从系统来考虑，标题里虽然说的是 “分布式” 规范治理，但是更多的时候是指多仓库的规范治理。而多仓库本身也充斥着一些不合理性，诸如于一个代码仓库内，可能包含着多个模块，如 monorepo。从这个角度来看，只是讨论分布式系统，可能有一些单薄。但是呢，我们在写规范，针对的是系统吗？难道不是团队中的开发人员？所以，我们所想的治理的是分布式协作的规范性问题。

## 回顾开发规范及其工具化

对于软件研发来说，效能的提升是一个非常宏大的史诗级话题，在这个话题里，规范的建立是一个非常有效的方案 —— 当且仅当，我们建立了配套的相关执行机制和工具。在确保了拥有统一规范的情况下，A 团队的开发人员，可以快速地到  B 团队开发，而不需要一些额外的讨论。简单来说，规范就是一种用于规模化提升效能的**模式**。

多年前，对于软件开发的规范，我们主要依赖于口头约定 + code review，这依赖于团队拥有比较好的技术能力。应对于规模化时，这样的模式是无法实施的。特别是**开发团队质量不齐**的情况下，依附于个人的自觉，已经难于控制团队的质量。特别是，我们会因为越来越多的 quick fix，导致一次又一次性破坏系统的规范。

人们开发了一系列的 Lint、Checkstyle、守护工具，以确保我们设计的规范能被实施下去。诸如于：

* 针对于前端，我们有 ESLint、Prettier
* 针对于后端，我们也有一系列的工具，如：PMD/CheckStyle。还有国内流行的阿里、华为 Java 规范。
* 针对于 Java 架构，我们有：ArchUnit
* 针对于 API，我们有：API Linter、Spectral 
* 针对于数据库，我们有：SQLFluff

于是，在单体系统里，上述的一系列情况得到了有效的改善，但是我们来到了微服务时代、微前端时代等，整体又发现了一系列的变化。为了应对于这种变化，我们还需要一些额外的工具，以确保这些规范化的工具能被安装和使用。

在那之前，让我们先总结一下规范工具化的时机，以明确我们应该在哪个时机来应对分布式下的挑战。

## 规范工具化的时机

从模式上来说，我们通常会在如下的一些时机里，来检查软件是否符合规范。（按顺序排序）

* 创建态。即将规范内嵌到每个应用的创建模板中。典型的形式是应用脚手架 等。
* 开发态。即结合开发过程中的工具（如  IDE、Git、CLI），将规范内置到开发流程中。典型的有 Git Hooks、IDE 插件等。
* 测试态。即结合自动化测试、契约测试等，在运行测试的时机，检查已有的系统是否遵循相关的规范。
* 集成态。即对于规范的检查配置在持续集成中，有时会作为一种强制的软件质量门禁。典型的有 SonarQube 等。
* 运行态。即结合软件运行的信息（如 APM、日志、分布式链路系统等），对系统进行系统进行分析。典型的有 NewRelic、Skywalking 等。

从执行顺序来时机来说，越往前便意料着越能及早的发现错误，成本也越低。当然了，每种不同的时期，都应该有各自的重点。

| 时机 | 关注点 | 工具示例 |
|----|----|----|
| 创建态 | 代码规范内建、规范执行机制、分层规范等  | 应用脚手架 |
| 开发态 | 代码规范 | CheckStyle 的 Intellij IDEA插件 |
| 测试态 | 代码规范、分层架构、API 规范等 | ArchUnit |
| 集成态 | 质量门禁 | Sonarqube |
| 运行态 | 服务依赖 | Skywalking |

当然了，还有一些是跨越了多个不同的时机，诸如于契约测试，它是在开发时期定义的，但是可能会在测试态、集成态才验证的。然而，在过程中，很大一部分的内容都是在代码中，由开发人员控制的。作为一个开发者，也是一个 hacker，我们会习惯性的：


1. 跳过不需要的自动化检查。
2. 路过不需要的测试。它可能跑起来很慢
3. 删除或者禁用一些不需要的规范代码或者配置。

这样一来，哪怕我们做了再好的规范设计，代码不，没有 code review 的保障，那么系统就会被进一步地腐化。

## 分布式场景下的规范

现在，让我们回到先前我们定义的分布式场景，思考一下如何在这种场景下，构建规范工具化？

### 分布式的规范工具化

对于这些规范来说，它们的工具化思路类似于，我们在《[代码分析与自动化重构](https://github.com/modernizing/modernization)》所说的：源码分析 → 构建模型 → 识别模式 → 得到结果。为了支撑到分布式场景，一些潜在的方案便是：

* 工具化代码块。使用额外的代码模块（如 Git Submodule、软件包等）来执行规范的自动化，诸如于 npm 包、jar 包的形式。
* 工具检查器。检查是否安装了对应的工具，是否执行了对应的步骤。（并不推荐）
* 构建新的工具。如 Guarding 这种模式。
* 设计成熟度指标。用于指导和改善系统的架构设计。

去年，在设计 Guarding 这个多语言的架构守护工具时，其与 ArchUnit 相比的场景是：多语言、多代码库。与 ArchUnit 相比，Guarding 推荐的这种守护方式是：

* 以 CLI 的方式运行。无需额外的编码工作，不担心系统被破坏。
* 配置在持续集成中。
* 多系统多语言守护。

当然了，它更多的是在测试态、开发态来解决问题。理想情况下，应该包含 IDE 插件，在开发时能提醒开发人员，系统架构有哪些问题。

### 指标模型：架构适应度函数

虽然，我们可以构建一个基于“分布式”场景的规范，但是从某种意义上来说，这些规范是一种约束。对于开发人员来说，我们需要一种更好的指导指标，而不是我们破坏了哪些规则。所以，我们应该考虑架构适应度函数的方式，从多个不同的维度，来帮助开发人员：


1. 理解系统的当前状态
2. 理解指标对于系统的意义
3. 指导系统更好的演进
4. 知悉什么是好的模式和设计

也因此，从这个层面来考虑，单体系统里的 Sonarqube 就是一个非常好的工具。

## 其它

最后，回到我们所推崇的敏捷实践，**个体和互动**高于**流程和工具**。让架构和系统知识能在团队中流动起来，远远比工具更加重要。


# 架构治理调研：规则、表达式还有语言

在上一篇文章《[“分布式” 开发规范治理](https://www.phodal.com/blog/distributed-guard/)》中，我们谈论到了 “分布式” 场景下，对于架构治理和规范治理的一系列问题。在那篇文章里，我们提及了一系列的工具，如 API Linter 工具 Spectral，数据库 Linter 工具 SQLFluff。而为了在 ArchGuard 中完善分布式规范的能力，便分析了几个现有的工具。

对于我们来说，构建一个类似的工具，需要考虑的一些因素有：

* 插件化。开发人员可以根据已有的守护规则，开发一些新的架构守护规则，如针对于 API 的，针对于数据库调用链路的。
* 可测试性。如果采用的是完全 DSL 或者 半 DSL，那么如何让后续的
* 语言无关。如何不绑定于语言的语法树，而实现对于多种语言的支持。

出于这个目的，只好拿起现有的代码进行一番分析，主要有四个工具，适用于 Kotlin 语言的 KtLint、适用于 OpenAPI 的 Spectral、适用于多数据库的 SQLFluff，以及被诸如 MyBatis 采用的表达式语言 Ognl。

## Kotlin 代码的治理：KtLint

KtLint 与一般的 Lint 工具稍有区别的是，它自带了一个自动格式化的功能。KtLint 整体的逻辑还是比较简单的，基于单个文件进行 AST 生成，随后针对于 AST 进行规则匹配。Ktlint 围绕于 Rule、Rulesets、RulesetsProvider 构建了规则的层级关系，同时用 Vistor （即 VisitorProvider）模式围绕 AST 进行分析，如下是 KtLint 的抽象 Rule：

```javascript
    /**
     * This method is going to be executed for each node in AST (in DFS fashion).
     *
     * @param node AST node
     * @param autoCorrect indicates whether rule should attempt auto-correction
     * @param emit a way for rule to notify about a violation (lint error)
     */
    abstract fun visit(
        node: ASTNode,
        autoCorrect: Boolean,
        emit: (offset: Int, errorMessage: String, canBeAutoCorrected: Boolean) -> Unit
    )
```

如注释中所说的，三个参数代表了各自的用途。这里的 ASTNode 是来源于 Kotlin 的 AST 树（ `kotlin-compiler-embeddable` 包）。模式上也是获取配置，然后运行检测规则：

```javascript
val ruleSets = ruleSetProviders.map { it.value.get() }
val visitorProvider = VisitorProvider(ruleSets, debug)
```

其中对应的 visit：

```javascript
          visitorProvider
            .visitor(
                params.ruleSets,
                preparedCode.rootNode,
                concurrent = false
            ).invoke { node, rule, fqRuleId ->    }
```

在 VistorProvider 中会过滤对应的规则：

```javascript
        val enabledRuleReferences =
            ruleReferences
                .filter { ruleReference -> isNotDisabled(rootNode, ruleReference.toQualifiedRuleId()) }
        val enabledQualifiedRuleIds = enabledRuleReferences.map { it.toQualifiedRuleId() }
        val enabledRules = ruleSets
            .flatMap { ruleSet ->
                ruleSet
                    .rules
                    .filter { rule -> toQualifiedRuleId(ruleSet.id, rule.id) in enabledQualifiedRuleIds }
                    .filter { rule -> isNotDisabled(rootNode, toQualifiedRuleId(ruleSet.id, rule.id)) }
                    .map { rule -> "${ruleSet.id}:${rule.id}" to rule }
            }.toMap()
....
```

然后，再去并行或者串行地运行 Rule 里的 visit。

而对于规则的方式是通过 ServicesLoader 进行的插件化方式：

```javascript
private fun getRuleSetProvidersByUrl(
    url: URL?,
    debug: Boolean
): Pair<URL?, List<RuleSetProvider>> {
    if (url != null && debug) {
        logger.debug { "JAR ruleset provided with path \"${url.path}\"" }
    }
    val ruleSetProviders = ServiceLoader.load(
        RuleSetProvider::class.java,
        URLClassLoader(listOfNotNull(url).toTypedArray())
    ).toList()
    return url to ruleSetProviders.toList()
}
```

如果粒度更大的情况下，采用 Java 9 的模块是不是会更加方便？

### 基于 API 数据的 Spectral

 与 Ktlint 不同的是 Spectral 是一个针对于 JSON/YAML Lint 的工具，特别是针对于 OpenAPI 文档（就是 swagger 的 yaml/json 文件）。与 Ktlint 相比，Spectral 最有趣的地方是，它提供了一个 JSON Path（类似于 XPath）的功能，可以针对于对象中的特定部分，进采用特定的规则。如下是 Spectral 的示例：

```javascript
'oas3-valid-schema-example': {
  description: 'Examples must be valid against their defined schema.',
  message: '{{error}}',
  severity: 0,
  formats: [oas3],
  recommended: true,
  type: 'validation',
  given: [
    "$.components.schemas..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
    "$..content..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
    "$..headers..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
    "$..parameters..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
  ],
  then: {
    function: oasExample,
    functionOptions: {
      schemaField: '$',
      oasVersion: 3,
      type: 'schema',
    },
  },
}
```

上面对象中的 `given` 即是针对于对象中的相关属性作为条件，执行后面的 `then` 函数，详细可以见官方的文档：《[Custom Rulesets](https://meta.stoplight.io/docs/spectral/ZG9jOjI1MTg5-custom-rulesets#then)》。顺带一提：Spectral 采用的是 [nimma](https://github.com/P0lip/nimma) 作为 JSON Path 表达式。

### Spectral 的模型

与 Ktlint 相比，由于 Spectral 是与 OpenAPI/Async API 进行了相关的绑定，加上特定的规则表达式，所以其数据模型稍微复杂一些。其数据模型包含了：描述，消息级别，given - then，上下文。如下所示：

* recommended。是否是推荐配置。
* enabled。是否允许
* description。规则描述
* message。错误信息
* documentationUrl。文档地址。
* severity。严重程度，\`error\`, \`warn\`, \`info\`, or \`hint\`。
* formats。格式化标准，如 OpenAPI 2.0、OpenAPI 3.0 等。
* resolved。是否已解决。
* given。类似于 CSS 中的选择器，使用类似于 XPath 的 JsonPath，[JSONPath](https://goessner.net/articles/JsonPath/index.html)
* then。
  * field，字段
  * function，函数，模式
  * functionOptions

此外，它还有一个简单的类型系统，以及对应的表达式判断。如下：

* CASES。flat、camel、pascal、kebab、cobol、snake、macro
* 长度：最大值、最小值。
* 数字
* Boolean 判断。
* 类型系统。枚举

总的来说，Spectral 在实现上比较灵活有趣。

### SQLFluff

与 Ktlint 和 Spectral 这种基于已有的数据模型的应用来说，SQLFluff 显得更有挑战性 —— 它是基于多种不同的数据库方言来构建规则的。SQLFluff 是直接基于源码来进行分析的，将不同的数据库方言转换为基本元素（分词）。随后，基于分词的类型 + 规则 ，来对它们进行处理。简单来说，就是更抽象的分词上下文，构建对应的规则上下文。如下是

* segement。位于其核心的是 BaseSegment，它定义了 Lexing、Parsing 和 Linting 三个基本的元素，产生诸如：`groupby_clause`、`orderby_clause` 、`select_clause` 等分词。
* parent_stack。
* siblings_pre。
* siblings_post。
* raw_stack。
* memory。
* dialect。作为语法运行时解析的基础。
* path。路径。
* templated_file。模板文件。

示例：

```javascript
{
    "file": {
        "statement": {
            "select_statement": {
                "select_clause": {
                    "keyword": "SELECT",
                    "whitespace": " ",
                    "select_clause_element": {
                        "column_reference": {
                            "identifier": "foo"
                        }
                    }
                },
                "whitespace": " ",
                "from_clause": {
                    "keyword": "FROM",
                    "whitespace": " ",
                    "from_expression": {
                        "from_expression_element": {
                            "table_expression": {
                                "table_reference": {
                                    "identifier": "bar"
                                }
                            }
                        }
                    }
                }
            }
        },
        "statement_terminator": ";",
        "newline": "\n"
    }
}
```

随后的规则，便是在对这些规则进行 `eval`，如下示例：

```javascript
class Rule_L021(BaseRule):
    def _eval(self, context: RuleContext) -> Optional[LintResult]:
        """Ambiguous use of DISTINCT in select statement with GROUP BY."""
        segment = context.functional.segment
        if (
            segment.all(sp.is_type("select_statement"))
            # Do we have a group by clause
            and segment.children(sp.is_type("groupby_clause"))
        ):
            # Do we have the "DISTINCT" keyword in the select clause
            distinct = (
                segment.children(sp.is_type("select_clause"))
                .children(sp.is_type("select_clause_modifier"))
                .children(sp.is_type("keyword"))
                .select(sp.is_name("distinct"))
            )
            if distinct:
                return LintResult(anchor=distinct[0])
        return None
```

在这里所有的规则判断都是基于这种抽象的语法树。从某种意义上来说，构建了一个统一的抽象。本来想进一步分析，但是发现各种 SQL dialect 里是各种正则表达式，我就选择了临时性撤退。

## 表达式语言：OGNL

起初，我是在实现 ArchGuard Scanner 对于 MyBatis 的 SQL 生成支持时，看到了 XML 中嵌套的 OGNL 表达式，发现了 OGNL。从实现上来说，它比我之前设想的 TreeSitter 中的 S 表达式，在与数据结合的完善度上更高。同样，也可以用于这里的规则判断，可以用表达式来对数据进行匹配。

对象导航图语言（Object Graph Navigation Language），简称 OGNL，是应用于 Java 中的一个开源的表达式语言（Expression Language），用于获取和设置 Java 对象的属性，以及其他附加功能，例如列表投影（projection）和选择以及 lambda 表达式。您可以使用相同的表达式来获取和设置属性的值。Ognl 类包含了评估 OGNL 表达式快捷方式。它可以分两个阶段执行此操作，将表达式解析为内部形式，然后使用该内部形式设置或获取属性的值；或者可以在一个阶段完成，并直接使用表达式的字符串形式获取或设置属性。

```javascript
Ognl.getValue("name='jerry'", oc, oc.getRoot());
String name2 = (String) Ognl.getValue("#user1.name='jack',#user1.name", oc, oc.getRoot());
```

本来想模仿 OGNL 编写一个表达式语言，但是发现使用的是 Jacc，也没有 Antlr 实现。所以，在寻找一种更合理的方式。

# 架构工作台：构建企业（应用）架构的数字孪生


> 架构工作台是一个环境，其设计初衷用于帮助人们设计架构、演进架构、观测架构，并有效地运用架构所需要的高质量工具，如交互式的架构开发和分析。

在上一篇文章《[架构即代码：编码下一代企业（应用）架构体系](https://www.phodal.com/blog/architecture-as-code/)》中，我们介绍了架构即代码的思想，它是如何围绕于架构的一系列模式，将架构元素、特征进行组合与呈现，并将架构决策与设计原则等紧密的与系统相结合。

而为了实施及落地**架构即代码**的理念，还需要构建一个运行这些代码的平台，我们称它称为架构工作台。可是，为什么我们要构建一个架构工作台？仅仅是为了好玩。

## 为什么构建架构工作台？

在 ArchGuard 中，我们想治理的是架构的三种形态：**设计态**、**开发态**和**运行态**。对应于：

* 设计新的企业（应用）架构。诸如于描述和设计系统的当前架构。
* 理解和管控系统的现状。诸如于通过可视化的手段展示系统的现状、以规则来管理系统。
* 观测系统 <=> 架构的运行。

  同样，对于诸多中大型组织师的架构相关的部门来说，他们同样存在上述的这些问题。并且，我相信他们也面临着同我们构建 ArchGuard 时一样的困境：


1. 架构是多维的。包含技术、数据、安全、运维与系统等
2. 缺乏统一的架构**语言**。用于沟通的人类语言，诸如于什么是组件？
3. 系统的架构千奇百怪。架构风格或模式差异，如微服务架构、插件化架构等。
4. 缺乏业务上下文。作为一个外部架构师，帮助治理时缺乏一些上下文。
5. 细节是魔鬼。架构的世界丰富多彩，没有办法一一展现出来，比如一个小小的接口，可能会反转我们对于理解的假设。
6. 我们（ArchGuard 团队）目前的架构能力有限（\~\~这个不会写出来的\~\~）——  资深架构师太少。

所以，在实现这样一个标准化的架构模式系统之前，不如尝试构建一个更灵活的形式：架构工作台。它可以帮助我们更好地探索系统，也更符合我们的初期体验。

## 什么是架构工作台

对于工作台这一概念来说，作为一个活跃的 DSL 创造者，我比较熟悉的是 Martin Fowler 在《领域特定语言》中对于语言工作台的定义：

> 语言工作台是一个环境，其设计初衷就是帮助人们构建新的 DSL，以及有效地运用这些 DSL 所需的高质量工具。

也因此在定义上，我们参考了老马的定义，并借鉴了现代化的工作台理念，主要有：RStudio 的**代码文档化**、Jupyter 的**交互性分析**。所以，我们的定义是：

> 架构工作台是一个环境，其设计初衷用于帮助人们设计架构、演进架构、观测架构，并有效地运用架构所需要的高质量工具，如交互式的架构开发和分析。

代码文档化，用于帮助我们更好地管理**架构代码**，将它融入软件开发生命周期里，如架构文档、用户故事、持续集成等。而交互性分析，则是用于帮助我们有效减少定制的代码，进而演变为提供的是 API 接口，与其定制化的代码。

对于架构工作台来说，它应用包含了以下一些核心功能与特性：

* 设计架构、演进架构与观测架构。
* 构建架构的数字孪生
* 说明性编程与显式设计

在这些特性中，我们认为**构建架构的数字孪生**是这个工作台最应该被重视的部分，而基础块则是：设计架构、演进架构与观测架构。

### 基础：设计架构、演进架构与观测架构

在治理架构时，我们通常会关注于如何设计，如何演进，以及观测架构在运行时的状态。

* **设计架构。**这样的功能其并不能理解，难点是，如何提供高效的设计机制？采用 UI 拖拉拽的方式，又或者是 DSL，都有各种的博弈。总体来说使用代码会更加友好。而为了直观的展示架构的设计，我们会通过架构图来表示，这也就是系统的核心。
* **演进架构。**其核心是**构建架构治理模型**和**设计架构的适应度函数**，以引导系统进行有序的变更。从实现的层面来说，就是从对实现出来的软件架构（即代码等）进行分析，设计度量指标，并提供高质量的工具，来引导架构回归到合理的状态。
* **观测架构**。即观察架构的应用的运行状态，主要依靠于各类的 APM（application performance management，应用性能监测）工具。在云原生时代，相关的工具也非常的流行，如 Skywalking 等。

一个基础的架构工作台，应该考虑上述的三个要素。

### 进阶：构建架构的数字孪生

理想的架构工作台，它应该提供一个可交互的架构的映射版本。它意味着：它是对系统或对象在其整个生命周期中的虚拟表示，我们可以根据实时数据进行更新，并使用**模拟、机器学习和推理**来帮助决策。

从概念上来说，它提供了一个非常美好的未来，难点就在于如何实现这样的系统？

我们正在设计 ArchGuard 的数字孪生版本，则围绕于这三种形态的架构形式（从实现的层面考虑的）：

* 设计态。关注于**边界**、**概念**与**交互**。
* 开发态。关注于**层级、依赖**与**模块**。
* 运行态。关注于**性能**、**链路层级**与**资源使用**。

这个版本相当于是设计态，在实现的时候，还会受到开发能力和数据上的制约。未来，我们是不是也应该代码及其架构孪生的双向绑定？

### 核心：说明性编程与显性设计

我们所熟悉的各类架构相关的规范，它存在着诸多的问题，诸如于：**规范不直观、规范没有自动化**等。为了有效的演进架构，我们需要有效地结合规范、代码、设计，也就是说明性编程与显性设计，它们都是来自于 PL（编程语言）/ DSL（领域特定语言）中的概念。

* **说明性编程**（illustrative programming）是用于加强执行结果的解释性，如在架构治理的场景下，通过**生成架构图**来理解 DSL 编写的结果。另外一类觉的方式，有诸如于 Excel 中的表格和图表等。详细可以查看文末 Martin Fowler 的相关文章。
* **显式设计**。**显式**是通过明确编写要完成的指示来完成所需更改的手动方法。显示设计则是指引我们如何构建好系统的描述语言，诸如于 ADL（架构描述语言）的 DSL。除了，使用贴进架构描述的语言，还有用于支撑 DSL 的工具支撑，如编辑器智能感知。

这两个特性是围绕于**架构即代码**这一理念所改善的，诸如于 PlantUML、Graphviz 这一类图表即代码（Diagrams as code）也具备了这样的特质。

### 其它特质

当然，为了让这个系统更好用，我们还需要其它的一系列特性：

* 架构演进的模拟验证。在构建了架构的数字孪生之后，我们就可以
* 易于扩展的功能。即采用微内核架构，即插件化、微前端化等。

依旧还有诸多东西可以探索，等待我们构建完第一个版本后，再展开讨论。

## 我们应该如何去设计与实现的？

我们现在 ArchGuard 中实践一个这样的系统，它相当地令人激动。作为一个早期的版本，它勉强可以工作，如下是我们构建系统的一些思路。

### 1. 大数据的工作台作为灵感

由于，系统是一个类似于数据分析的系统，所以我们的诸多流感来源于大数据领域。在工作台上，起初，我们的想法是构建一个采用 RStudio 似的可编译报告，并提供类似于 Jupyter 的交互体验。

可由于 Jupyter 的交互体验太差，下载的文件又没有可读性，不能方便地编辑 + 运行。所以，我们采用了 Markdown 作为数据格式，方便于开发人员编写文档，也可以在任意的 IDE 中打开和编辑。在 UI 上，则是采用 ProseMirror 主编辑器，即 Markdown 的编辑器，采用 Monaco Editor 作为副编辑器，即代码的编辑器。

### 2. 构建基于 REPL 的抽象

考虑到了 IDE 支持，我们采用了 Kotlin 来构建**内部 DSL**，这样就无需外部 DSL 需要构建编辑器支持。接着，就采用了 Kotlin Scripting Compiler 作为底层的 REPL（read–eval–print loop，也称为**交互式顶层构件**），简单来说，就是 Kotlin 语言的命令行模式。从形态上来说，类似于 MySQL Workbench 的形式，又或者说是 Python 的 CLI 模式。

随后，便是抽象系统（ArchGuard）、设计态的架构、架构适应度函数的 DSL。

### 3. 请求和响应程序的运行

在有了编译器后端的支持及 Kotlin DSL 的 IDE 之后，我们需要构建双者的桥梁。于是，我们采用 WebSocket 作为通信方式，返回结果上按不同的类型处理。诸如于：

* 集成后端 API 的运行结果。
* 图形结果。Mermaid、D3.js、Echart.js 是针对于不同图形展示。
* 执行对应的扫描。

与通常的 C/S 结构开发无异。图形化的结果，用于支持我们的**说明性编程与显性设计**。

# 构建 ArchGuard 架构工作台

## 原型参考与设计：可交互环境与文档体验

什么是文档？什么是代码？两者没有一个明确的界限，文档是可执行的，代码也是可执行的。不过，从最终的形态上来说，它们都是知识。所以，重点依旧在于如何将这些知识显式化。所以从原型参考上，我们关注于：可交互环境与文档体验设计。

### 可交互环境：Jupyter & Zeppelin &  Nteract

作为交互性编程的业内代表，Jupyter 成为了我们研究的第一个对象。不过，从实现上，我们并没有从它本身的源码上汲取到太多的内容（ “代码”）。反而是，围绕于它的生态及竞争对手上，我们看到了一些更有意思的亮点，诸如于 Kotlin Jupyter、Zeppelin、Nteract 等。

* Nteract 提供了一系列的组件、SDK 来，用来构建交互式应用，诸如于消息通信等等。然而 Nteract ，在设计的时候主要是在 Electron 环境下使用，所以有一些库是无法使用的，如 ZeroMQ —— 设计时是只针对于 Node 环境的。
* Zeppelin 构建了一个更简单的执行环境（Interpreter），与 Jupyter 的 Kernel API 相比，它可以提供一些更有意思的实现层面的抽象。
* Kotlin Jupyter 则成了我们现在实现的一个基石。因为它还处于早期试验阶段，我们在构建的过程中，遇到过一系列的依赖包丢失的情况。

回过头来看，我们应该需要再回去看看 Jupyter 的抽象接口，或许能再提供更多的思路。

### 文档阅读体验与文档工程体验

对于文档体验来说，我一直主张应该关注于两个部分：

* 文档阅读体验。即向读者提供的文档体验。
* 文档工程体验。即向工程提供的文档编写体验。（这一部分往往容易被忽视）

对于文档体验来说，除了用于说明性编程的各类架构图，还需要提供各类的定义化能力。其参考来源来源主要是：我们日常的开发中的编程语言的文档编写，详细可以参考《[API 库的文档体系支持：主流编程语言的文档设计](https://www.phodal.com/blog/api-ducumentation-design-dsl-base/)》与《[文档工程体验设计：重塑开发者体验](https://www.phodal.com/blog/documentation-enginnering-experience-design/)》。

而诸如于 Mermaid、Graphviz 这一类的图即代码（diagram as code），它们在两者提供了一个很好的平衡（只针对于程序员）。

## 技术评估：DSL、REPL 与编辑器

再回到实现上来，在进行架构工作台的技术评估时，我们关注于架构师编写的 DSL（领域特定语言）语法、REPL（read–eval–print loop） 运行环境以及用于交互的编辑器。其核心关注点是：如何构建更好的[开发者体验](https://github.com/phodal/dx)，一个老生常谈的、难话题。

### DSL 语法：Antlr vs Kotlin DSL

在 ArchGuard 中，主要使用的是 Antlr 框架来进行不同语言的语法解析（即 Chapi）。因此，使用 Antlr 来设计一个新的 DSL 及其编译器前端，对于我们而言，并不存在技术上的挑战。甚至于，在以往的经历中，我们也有大型 IDEA 插件架构设计与开发的经历。

然而对于 DSL 来说，我们要考虑的核心因素是：

* **语法的学习成本。**
* **语法的体验设计。**
* **语法的编辑器/IDE 支持。**

如果语法只是是个语言的 API，那它能大大降低学习成本。虽然 Kotlin 有点陌生，但是 Groovy + Gradle 都很熟吧。于是乎，我们采用的方式是基于 Kotlin 语言自带的 [Type-safe builders﻿](https://kotlinlang.org/docs/type-safe-builders.html) 来构建构建 DSL。官方给的一个参考示例是 Ktor 的路由示例：

```javascript
routing {
    get("/hello") {
        call.respondText("Hello")
    }
}
```

除了已经有丰富的 IDE、编辑器的支持之外。在构建**架构适应度函数**时，也可以使用语言库提供的数学功能，以便于定制各类的计算规则。

### 架构 REPL：Kotlin Scripting vs Kotlin Jupyter

而对于构建一个交互式架构 REPL 来说，我们需要需要考虑的一个核心点是：**构建执行上下文（EvalContext）**。即后面运行的代码是依赖于前面代码提供的上下文的，如变量等：`val x = 2 * 3`，后续就可以使用 `x` 。

对于我们来说，有两个选择：

* Kotlin 语言自带的试验性功能：Kotlin Scripting 提供了一种无需事先编译或打包成可执行文件即可将 Kotlin 代码作为脚本执行的技术。因为，对于我们来说，只需要构建我们的 DSL 包，就可以直接执行。


* Kotlin Jupyter 的实现也是基于 Kotlin Scripting 提供了一系列的 API 封装。

在 REPL 上，起初我们纠结于自己实现，还是基于 Kotlin Jupyter，毕竟 Jupyter 包含了一系列的不需要的代码。后来，发现代码好复杂，虽然都是 MIT 协议，但是我们也不想维护一个不稳定功能的下游版本。

因此，在最后，我们基于 Kotlin Jupyter 的 API 构建了 ArchGuard 的架构 REPL。

### 探索编辑器：ProseMirror vs Others

对于编辑器来说，考虑的核心点是：**组件扩展性**。即，可以按需添加用于展示图表的组件，又或者是其它的结果展示相关组件。

在设计上 Jupyter、Zeppelin 采用的是块（Cell）式编辑器，即文档是按**块**的形式切开来的。稍有区别的是 Jupyter 基于 CodeMirror，则 Zeppelin  是基于 Monaco Editor。这种基于块式的编辑功能，有点割裂，提供的交互体验对于纯键盘操作不友好。

于是乎，为了探索更好的文档交互方式，我们陆陆续续参考了一系列的编辑器：CodeMirror、Draft.js、Lexical、ProseMirror 等。ProseMirror 是 CodeMirror 作者的另外一个作品，融合了 Markdown 与传统的 WYSIWYG 编辑器。也就是说：即可以写 Markdown 也可以用富文本的方式（PS：在编写此文时，我使用的 Quake 的底层也是 ProseMirror）。即，它可以同时满足两类人的需求，**使用 Markdown 和不使用 Markdown**，他们能都从编辑器上获得自己的鼠标（markdown）和键盘（富文本）。

探索完之后，我们发现基于 ProseMirror 的 [rich-markdown-editor](https://github.com/outline/rich-markdown-editor) 能提供所需要的功能。只需要编写一些**类** ProseMirror 插件，不需要编写大量的 markdown 相关的处理功能。

## 落地：构建数据通讯与结果呈现

为了验证整个 PoC （Proof of Concept，概念证明）是可行的，接下来就是让数据作为胶水把一切串联起来，构建这样一个完整的端到端示例：


1. 前端 → REPL。在前端编写 DSL，执行运行，交数据发送给 REPL。
2. REPL → 前端。REPL 解析数据，将后续的 Action，返回给前端。
3. 前端 → 后端。前端根据 Action，决定是显示架构图，还是发请求给后端。
4. 后端 → 前端。后端根据前端的请求，执行对应的命令，再将结果返回给前端。
5. 前端。前端再根据后端的数据处理。

所以，其实核心的部分只有一个：**模型的设计**，诸如于：Message 和 Action。

### 数据传输与处理：Message 模型

在 REPL 服务中，通过 WebSocket 接收到前端的数据之后，就需要将其转换为对应的数据，并返回给前端。如下是在 PoC 中，我们所定义的 Message ：

```javascript
data class Message(
    var id: Int = -1,
    var resultValue: String,
    var className: String = "",
    var msgType: MessageType = MessageType.NONE,
    var content: MessageContent? = null,
    var action: ReactiveAction? = null,
)
```

在执行前端传入的代码后，会根据不同的执行结果，返回一些后续的 Action 信息（代码中的 `ReactiveAction`），以及对应的数据（在 `action` 中）。

### REPL：构建执行环境

对于 REPL 来说，我们还需要做的事情有：


1. 构建 REPL 环境。如添加 ArchGuard DSL 的 jar 包，以及对应的 Kotlin Scripting、Kotlin Jupyter 的 Jar。
2. 添加 `% archguard` Magic。添加一个自定义的 `LibraryResolver` 。

虽然对于 REPL 不熟悉，但是幸好在有 Kotlin Jupyter 的源码作为参考，这个过程并不算太痛苦。虽然过程，也是异常的痛苦：没有可用的文档、环境只为 Jupyter 设计、只能看测试用例。但是，至少还是可以看**测试用例** —— 测试是个好东西。

在开发环境下，会加载 Java 运行环境的 classpath （详细见：**KotlinReplWrapper**）：

```javascript
val property = System.getProperty("java.class.path")
var embeddedClasspath: MutableList<File> = property.split(File.pathSeparator).map(::File).toMutableList()
```

在运行环境下，则会只引用所需要的 jar 包。两个环境的不一致，也需要在后续探索一下如何进行优化。

### 编辑器：

在我们落地的过程中，编辑器的实现被分为两部分，一个是编写 ProseMirror 插件，另外一个则是完善 Monaco Editor 的感知。

**ProseMirror** **插件编写**

针对于代码块，编写了 `LivingCodeFenceExtension` 插件替换了 rich-markdown-editor 中的代码块语法功能，并指向了 Monaco Editor 组件：

```javascript
<CellEditor
  language={language}
  code={value}
  removeSelf={this.deleteSelf(props)}
  codeChange={this.handleCodeChange}
  context={this.options.context}
  languageChange={this.handleLanguageChange}
/>
```

再围绕于两个编辑器，构建了一系列的交互，如：语言变更、删除代码块、执行代码等。

**围绕 Monaco Editor** **构建 DSL 开发者体验**

Monaco Editor 的完善，主要会围绕于：添加代码高亮、自动填充与智能感知。现在，只完成了基本的功能，还有很多功能需要后续进行探索。

### 结果展示与图形

对于结果来说，其核心的部分在 `ResultDispatcher` 上，顾名思义，根据不同的结果来展示不同的展示结果，诸如于：

```javascript
switch (result.action.actionType) {
  case ActionType.CREATE_REPO:
    return <BackendActionView data={data} actionType={BackendActionType.CreateRepos} />;
  case ActionType.CREATE_SCAN:
    return <BackendActionView data={data} actionType={BackendActionType.CreateScan} />;
  case ActionType.GRAPH:
    return <GraphRender result={result} context={context}/>;
}
```

而为了更好的呈现**技术相关的图形细节**，我们在 ArchGuard 中引入了第**五个图形库**（由于几个图形库的存在，构建变成了一件痛苦的事，大概是最大的技术债了）：Mermaid。先前的 Echart.js 可以为我们提供低成本的图形编写，D3.js 则是提供了更灵活的定制能力。

## 最后，尝试一下部署吧

在我们写完 PoC ，并自信满满地打了 tag 之后，发现自动构建出来的 Docker 镜像是不 work 的，这大半夜的。最后，总结下来，原因有两：


1. 未配置 Nginx 的 WebSocket 。
2. Kotlin REPL 依赖于 unpack 环境。

好在，只要再快速修复（quickfix）、打个 tag 就能解决了。事实证明，但凡是想 quickfix，都没法 quickfix。

### 配置 WebSocket

首先，根据网上的文档，配置好对应的 WebSocket：

```javascript
location /ascode {
  proxy_pass http://archguard-backend:8080;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection $connection_upgrade;
}
```

于是，重构构建镜像之后，发现后端又出问题了，运行的 REPL 环境出错。

### 配置 Kotlin REPL classpath

如上所述，REPL 在代码中配置的是：

```javascript
val property = System.getProperty("java.class.path")
var embeddedClasspath: MutableList<File> = property.split(File.pathSeparator).map(::File).toMutableList()
```

但是，在 Spring 打包后，classpath 只有一个，并且 Kotlin Scripting 会有一系列的问题，这个时候需要 `requiresUnpack`。详细见：Spring Gradle 插件文档：《[Spring Boot Gradle Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/)》，只是对应的解释说明：必须从 fat jars 中解压才能运行的**库列表**。 将每个库指定为具有 <groupId> 和 <artifactId> 的 <dependency>，它们将在运行时解包。

效果上，就是 Spring 在运行的时候，会将对应的库从 BootJar 中解压出来到临时的目录。

```javascript
tasks.withType<KotlinCompile> {
    kotlinOptions {
        jvmTarget = "1.8"
        freeCompilerArgs = listOf("-Xjsr305=strict")
    }
}

tasks.withType<BootJar> {
    requiresUnpack("**/kotlin-compiler-*.jar")
    requiresUnpack("**/kotlin-script-*.jar")
    requiresUnpack("**/kotlin-jupyter-*.jar")
    requiresUnpack("**/dsl-*.jar")
}
```

当然，编码上，一个一个去找太麻烦了，于是就找到临时目录遍历一下：

```javascript
val tempdir = compiler[0].parent
embeddedClasspath = File(tempdir).walk(FileWalkDirection.BOTTOM_UP).sortedBy { it.isDirectory }.toMutableList()
```

最后，生成的 classpath 值如下所示：

```bash
ikotlin - Classpath used in script: [/tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-script-runtime-1.6.21.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-jupyter-kernel-0.11.0-89-1.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/dsl-2.0.0-alpha.12.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-compiler-embeddable-1.6.21.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-jupyter-api-0.11.0-89-1.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-jupyter-lib-0.11.0-89-1.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-jupyter-shared-compiler-0.11.0-89-1.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-jupyter-common-dependencies-0.11.0-89-1.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f/kotlin-script-util-1.6.21.jar, /tmp/app.jar-spring-boot-libs-5edaa25c-496e-4eb0-b7d6-1118a8cc280f]
```

如此一来，它总算能正确启动了，然后再打一个新的 tag：`v2.0.0-alpha.12` 。


# 参考资料

* 《整洁架构之道 》
* 《架构之美》
* 《软件架构：架构模式、特征及实践指南》
* 《面向模式的软件架构 卷 1：模式系统》
* 《实用软件体系结构》
* 《Software Architecture in Industrial Applications》
* 《Architectural Blueprints—The “4+1” View Model of Software Architecture》
* 《领域特定语言》
* 《演进式架构》
* 《利用 Python 进行数据分析》
* 《[IllustrativeProgramming](https://martinfowler.com/bliki/IllustrativeProgramming.html)》

## License

This code is distributed under the Creative Commons Attribution-Noncommercial-No Derivative Works 3.0  License. See `LICENSE` in this directory.


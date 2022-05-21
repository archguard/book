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


# 参考资料

* 《整洁架构之道 》
* 《架构之美》
* 《软件架构：架构模式、特征及实践指南》
* 《面向模式的软件架构 卷 1：模式系统》
* 《实用软件体系结构》
* 《Software Architecture in Industrial Applications》
* 《Architectural Blueprints—The “4+1” View Model of Software Architecture》

## License

This code is distributed under the Creative Commons Attribution-Noncommercial-No Derivative Works 3.0  License. See `LICENSE` in this directory.


# 限定规则

| 时机   | 创建态                 | 开发态                          | 测试态               | 集成态       | 运行态        |
|------|---------------------|------------------------------|-------------------|-----------|------------|
| 关注点  | 代码规范内建、规范执行机制、分层规范等 | 代码规范                         | 代码规范、分层架构、API 规范等 | 质量门禁      | 服务依赖       |
| 工具示例 | 应用脚手架               | CheckStyle 的 Intellij IDEA插件 | ArchUnit          | Sonarqube | Skywalking |

## 问题：开发规范治理

对于软件研发来说，效能的提升是一个非常宏大的史诗级话题，在这个话题里，规范的建立是一个非常有效的方案 ——
当且仅当，我们建立了配套的相关执行机制和工具。在确保了拥有统一规范的情况下，A 团队的开发人员，可以快速地到 B
团队开发，而不需要一些额外的讨论。简单来说，规范就是一种用于规模化提升效能的**模式**。

多年前，对于软件开发的规范，我们主要依赖于口头约定 + code review，这依赖于团队拥有比较好的技术能力。应对于规模化时，这样的模式是无法实施的。特别是
**开发团队质量不齐**的情况下，依附于个人的自觉，已经难于控制团队的质量。特别是，我们会因为越来越多的 quick
fix，导致一次又一次性破坏系统的规范。

人们开发了一系列的 Lint、Checkstyle、守护工具，以确保我们设计的规范能被实施下去。诸如于：

* 针对于前端，我们有 ESLint、Prettier
* 针对于后端，我们也有一系列的工具，如：PMD/CheckStyle。还有国内流行的阿里、华为 Java 规范。
* 针对于 Java 架构，我们有：ArchUnit
* 针对于 API，我们有：API Linter、Spectral
* 针对于数据库，我们有：SQLFluff

于是，在单体系统里，上述的一系列情况得到了有效的改善，但是我们来到了微服务时代、微前端时代等，整体又发现了一系列的变化。为了应对于这种变化，我们还需要一些额外的工具，以确保这些规范化的工具能被安装和使用。

### 分布式的规范工具化

对于这些规范来说，它们的工具化思路类似于，我们在《[代码分析与自动化重构](https://github.com/modernizing/modernization)》
所说的：源码分析 → 构建模型 → 识别模式 → 得到结果。为了支撑到分布式场景，一些潜在的方案便是：

* 工具化代码块。使用额外的代码模块（如 Git Submodule、软件包等）来执行规范的自动化，诸如于 npm 包、jar 包的形式。
* 工具检查器。检查是否安装了对应的工具，是否执行了对应的步骤。（并不推荐）
* 构建新的工具。如 Guarding 这种模式。
* 设计成熟度指标。用于指导和改善系统的架构设计。

去年，在设计 Guarding 这个多语言的架构守护工具时，其与 ArchUnit 相比的场景是：多语言、多代码库。与 ArchUnit 相比，Guarding
推荐的这种守护方式是：

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

## 解决方案

### 时机

从模式上，我们通常会在如下的一些时机里，来检查软件是否符合规范。（按顺序排序）

* 创建态。即将规范内嵌到每个应用的创建模板中。典型的形式是应用脚手架 等。
* 开发态。即结合开发过程中的工具（如 IDE、Git、CLI），将规范内置到开发流程中。典型的有 Git Hooks、IDE 插件等。
* 测试态。即结合自动化测试、契约测试等，在运行测试的时机，检查已有的系统是否遵循相关的规范。
* 集成态。即对于规范的检查配置在持续集成中，有时会作为一种强制的软件质量门禁。典型的有 SonarQube 等。
* 运行态。即结合软件运行的信息（如 APM、日志、分布式链路系统等），对系统进行系统进行分析。典型的有 NewRelic、Skywalking 等。

从执行顺序来时机来说，越往前便意料着越能及早的发现错误，成本也越低。当然了，每种不同的时期，都应该有各自的重点。

| 时机  | 关注点                 | 工具示例                         |
|-----|---------------------|------------------------------|
| 创建态 | 代码规范内建、规范执行机制、分层规范等 | 应用脚手架                        |
| 开发态 | 代码规范                | CheckStyle 的 Intellij IDEA插件 |
| 测试态 | 代码规范、分层架构、API 规范等   | ArchUnit                     |
| 集成态 | 质量门禁                | Sonarqube                    |
| 运行态 | 服务依赖                | Skywalking                   |

当然了，还有一些是跨越了多个不同的时机，诸如于契约测试，它是在开发时期定义的，但是可能会在测试态、集成态才验证的。然而，在过程中，很大一部分的内容都是在代码中，由开发人员控制的。作为一个开发者，也是一个
hacker，我们会习惯性的：

1. 跳过不需要的自动化检查。
2. 路过不需要的测试。它可能跑起来很慢
3. 删除或者禁用一些不需要的规范代码或者配置。

这样一来，哪怕我们做了再好的规范设计，代码不，没有 code review 的保障，那么系统就会被进一步地腐化。

### 实现示例

对于我们来说，构建一个类似的工具，需要考虑的一些因素有：

* 插件化。开发人员可以根据已有的守护规则，开发一些新的架构守护规则，如针对于 API 的，针对于数据库调用链路的。
* 可测试性。如果采用的是完全 DSL 或者 半 DSL，那么如何让后续的
* 语言无关。如何不绑定于语言的语法树，而实现对于多种语言的支持。

出于这个目的，只好拿起现有的代码进行一番分析，主要有四个工具，适用于 Kotlin 语言的 KtLint、适用于 OpenAPI 的
Spectral、适用于多数据库的 SQLFluff，以及被诸如 MyBatis 采用的表达式语言 Ognl。

#### Kotlin 代码的治理：KtLint

KtLint 与一般的 Lint 工具稍有区别的是，它自带了一个自动格式化的功能。KtLint 整体的逻辑还是比较简单的，基于单个文件进行 AST
生成，随后针对于 AST 进行规则匹配。Ktlint 围绕于 Rule、Rulesets、RulesetsProvider 构建了规则的层级关系，同时用 Vistor （即
VisitorProvider）模式围绕 AST 进行分析，如下是 KtLint 的抽象 Rule：

```kotlin
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

如注释中所说的，三个参数代表了各自的用途。这里的 ASTNode 是来源于 Kotlin 的 AST 树（ `kotlin-compiler-embeddable`
包）。模式上也是获取配置，然后运行检测规则：

```kotlin
val ruleSets = ruleSetProviders.map { it.value.get() }
val visitorProvider = VisitorProvider(ruleSets, debug)
```

其中对应的 visit：

```kotlin
visitorProvider
  .visitor(
      params.ruleSets,
      preparedCode.rootNode,
      concurrent = false
  ).invoke { node, rule, fqRuleId ->    }
```

在 VistorProvider 中会过滤对应的规则：

```kotlin
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

```kotlin
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

#### 基于 API 数据的 Spectral

与 Ktlint 不同的是 Spectral 是一个针对于 JSON/YAML Lint 的工具，特别是针对于 OpenAPI 文档（就是 swagger 的 yaml/json 文件）。与
Ktlint 相比，Spectral 最有趣的地方是，它提供了一个 JSON Path（类似于 XPath）的功能，可以针对于对象中的特定部分，进采用特定的规则。如下是
Spectral 的示例：

```javascript
'oas3-valid-schema-example'
:
{
    description: 'Examples must be valid against their defined schema.',
        message
:
    '{{error}}',
        severity
:
    0,
        formats
:
    [oas3],
        recommended
:
    true,
        type
:
    'validation',
        given
:
    [
        "$.components.schemas..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
        "$..content..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
        "$..headers..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
        "$..parameters..[?(@property !== 'properties' && @ && (@ && @.example !== void 0 || @.default !== void 0) && (@.enum || @.type || @.format || @.$ref || @.properties || @.items))]",
    ],
        then
:
    {
        function: oasExample

    ,
        functionOptions: {
            schemaField: '$',
                oasVersion
        :
            3,
                type
        :
            'schema',
        }
    ,
    }
,
}
```

上面对象中的 `given` 即是针对于对象中的相关属性作为条件，执行后面的 `then`
函数，详细可以见官方的文档：《[Custom Rulesets](https://meta.stoplight.io/docs/spectral/ZG9jOjI1MTg5-custom-rulesets#then)
》。顺带一提：Spectral 采用的是 [nimma](https://github.com/P0lip/nimma) 作为 JSON Path 表达式。

#### Spectral 的模型

与 Ktlint 相比，由于 Spectral 是与 OpenAPI/Async API
进行了相关的绑定，加上特定的规则表达式，所以其数据模型稍微复杂一些。其数据模型包含了：描述，消息级别，given - then，上下文。如下所示：

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

#### SQLFluff

与 Ktlint 和 Spectral 这种基于已有的数据模型的应用来说，SQLFluff 显得更有挑战性 —— 它是基于多种不同的数据库方言来构建规则的。SQLFluff
是直接基于源码来进行分析的，将不同的数据库方言转换为基本元素（分词）。随后，基于分词的类型 + 规则
，来对它们进行处理。简单来说，就是更抽象的分词上下文，构建对应的规则上下文。如下是

* segement。位于其核心的是 BaseSegment，它定义了 Lexing、Parsing 和 Linting
  三个基本的元素，产生诸如：`groupby_clause`、`orderby_clause` 、`select_clause` 等分词。
* parent_stack。
* siblings_pre。
* siblings_post。
* raw_stack。
* memory。
* dialect。作为语法运行时解析的基础。
* path。路径。
* templated_file。模板文件。

示例：

```json
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

```python
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

在这里所有的规则判断都是基于这种抽象的语法树。从某种意义上来说，构建了一个统一的抽象。本来想进一步分析，但是发现各种 SQL
dialect 里是各种正则表达式，我就选择了临时性撤退。

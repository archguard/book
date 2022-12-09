# 架构决策记录（示例）

**职务**

该决策定义了 ABC 应用程序开发的软件开发生命周期方法。

**状态**

已接受

**日期**

2022 年 3 月 11 日

**上下文**

ABC 应用程序是一种打包的解决方案，它将使用部署包部署到客户的环境中。我们需要有一个开发过程，使我们能够拥有可控的功能、修补程序和发布管道。

**决策**

我们使用的是改编版[GitFlow 工作流程](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)

开发 ABC 应用程序。

![GitFlow 工作流程，适用于 ABC 示例应用](https://docs.aws.amazon.com/zh_cn/prescriptive-guidance/latest/architectural-decision-records/images/gitflow-workflow.png)

为简单起见，我们不会使用`hotfix/*`和`release/*`分支，因为 ABC
应用程序将被打包而不是部署到特定环境中。出于这个原因，不需要额外的复杂性，这可能会阻止我们快速做出反应来修复生产版本中的错误，或者在单独的环境中测试版本。

以下是商定的分支策略：

* 每个存储库都必须有受保护`main`将用于标记发布版本的分支。
* 每个存储库都必须有受保护`develop`用于所有正在进行的开发工作。

**后果**

积极：

* 适配 GitFlow 进程将使我们能够控制 ABC 应用程序的发布版本控制。

消极：

* GitFlow 比基于中继的开发更复杂，或者 GitHub 流动并有更多的开销。

**合规**

* 这些区域有：`main`和`develop`每个仓库中的分支必须标记为`Protected`.
* 对的更改`main`和`develop`必须使用合并请求传播分支。
* 每个合并请求至少需要一次批准。

**备注**

* 作者：Jane Doe
* 版本：0.1
* 日志：
    * 0.1: 初始提议的版本
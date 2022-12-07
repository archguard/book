# 表征化建模

(characterization modeling) or (fact-based modeling)

> 概括来讲，纵观各种语言，其语法成分汇总起来构成一个关键概念集合。 —— Leonard Talmy 

维基百科定义：

> 表征 (Characterization), 在材料科学中使用时，指的是探测和测量材料的结构和特性的广泛而通用的过程。

## 背景

## 问题
   
## 解决方案

### 结构

- 基于可确定的 “事实” 确定状态（or 数据）。
- 借助模式来编排状态。
- 映射与过滤去除影响因子。分离变化要素，得到基本的概念。
- 精炼概念，得到模型元素与行为。
- 概念类聚。上下文边界划分

### 示例：DDD 事件风暴工作坊

http://domain-driven-design.org/zh/ddd-design-workshop-guide.html

事件风暴（Event Storming）是一种捕获行为需求的方法，类似传统软件开发的用例分析法。所有人员 (领域专家和技术专家) 对业务行为进行一次发散，并最终收敛达到业务的统一。

所以事件风暴可以作为 DDD 建模工作坊的一种重要的形式。使用事件风暴工作坊建模的逻辑闭环是：

- 产品愿景，识别软件价值和定位
- 领域划分，对业务的领域划分，以便能聚焦核心域
- 事件风暴，观察业务系统变化的关键点，找出系统状态的变化规律
- 命令风暴，找出系统状态的触发者和行为
- 寻找模型，找出业务中具有代表性的名词，建立初步的模型
- 模型展开，根据领域名词设计聚合、实体、值对象，细化模型
- 限界上下文划分，分解问题，战略规划整个系统
- 规范化输出，输出为清晰、明了的标准 UML 图

![Processes](http://domain-driven-design.org/zh/ddd-design-workshop-guide/process.png)

## 相关资源

参考文章：《[元素建模：探索建模的要素](https://www.phodal.com/blog/elemental-modeling/)》
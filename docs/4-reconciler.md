## reconciler
reconciler是React核心逻辑所在的模块，中文名协调器。协调（reconcile）就是diff算法的意思。

reconciler用途

jQuery工作原理，过程驱动：jQuery -> 调用 -> 宿主环境API -> 显示 -> 真实UI

前端框架结构与工作原理（状态驱动）

描述UI的方法（jsx, 模板语法） -> **编译优化**  -> 运行时核心模块（reconciler, renderer） -> 调用 -> 宿主环境API -> 显示 -> 真实UI

react:
- 消费jsx
- 没有编译优化
- 开放通用API供不同宿主环境使用

核心模块消费jsx的过程
- 核心模块操作的数据结构是？
  当前已知的数据结构：React Element 
  React Element如果作为核心模块操作的数据结构，存在问题：
  - 无法表达节点之间的关系
  - 字段有限，无法表达状态
- 需要一种新的数据结构 FiberNode（虚拟DOM在React中的实现），特点
  - 介于React Element与真实UI节点之间
  - 能够表达节点之间的关系
  - 方便拓展（不仅作为数据存储单元，也能作为工作单元）
- 节点类型：
  - JSX
  - React Element
  - FiberNode
  - DOM Element

reconciler的工作方式

- 对于同一个节点，比较其React Element与fiberNode，生成子fiberNode。并根据比较的结果生成不同标记（插入，删除，移动），对应不同宿主环境API的执行。
![](4-reconciler/workloop.png)
当所有React Element比较完后，会生成一棵FiberNode树，一共会存在两棵FiberNode树：
- current: 与视图中真实UI对应的FiberNode树
- workInProgress: 触发更新后，正在reconciler中计算的FiberNode树

JSX消费的顺序
- 以DFS（深度优先遍历）的顺序遍历React Element，意味着：
  - 如果有子节点，遍历子节点
  - 如果没有子节点，遍历兄弟节点

递：对应beginWork
归：对应completeWork
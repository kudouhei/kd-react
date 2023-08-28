# mount 流程

更新流程的目的：
- 生成wip fiberNode树
- 标记副作用flags

更新流程的步骤：
- 递：beginWork
- 归：completeWork

### beginWork
对于如下结构的reactElement：
```
<A>
    <B/>
</A>
```
当进入A的beginWork时，通过对比B current fiberNode与B reactElement，生成B对应wip fiberNode。
在此过程中最多会标记2类与「结构变化」相关的flags：
- Placement
  - 插入：a -> ab 移动：abc -> bca
- ChildDeletion
  - 删除：ul>li 3 -> ul>li 1

不包含与「属性变化」相关的flag：
- Update
  - `<img title="a" />` -> `<img title="b" />`

### 实现与Host相关节点的beginWork
首先，为开发环境增加`__DEV__`标识，方便Dev包打印更多信息
```pnpm i -d -w @rollup/plugin-replace```

HostRoot的beginWork工作流程：
- 计算状态的最新值
- 创造子fiberNode

HostComponent的beginWork工作流程：
- 创造子fiberNode

HostText没有beginWork工作流程，因为其没有子节点

### CompleteWork 性能优化策略
flags分布在不同fiberNode中，如何快速找到他们：
- 利用completeWork向上遍历的流程，将子fiberNode的flags冒泡到父fiberNode
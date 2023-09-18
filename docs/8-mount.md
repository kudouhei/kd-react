# mount 流程

更新流程的目的：
- 生成wip fiberNode树
- 标记副作用flags

更新流程的步骤：
- 递：beginWork
- 归：completeWork

### beginWork
beginWork阶段发生了什么：
1. 创建节点：根据ReactElement对象创建所有的fiber节点，最终构造出fiber树形结构（设置return和sibling指针）
2. 给节点打标签：设置fiber.flags
3. 设置真实DOM的局部状态：设置fiber.stateNode局部状态（如Class类型节点fiber.stateNode=new Class()）


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

### CompleteWork
CompleteWork阶段发生了什么：
1. 调用completeWork
   1. 给fiber节点(tag=HostComponent, HostText)创建 DOM 实例, 设置fiber.stateNode局部状态(如tag=HostComponent, HostText节点: fiber.stateNode 指向这个 DOM 实例).
   2. 为 DOM 节点设置属性, 绑定事件(合成事件原理).
   3. 设置fiber.flags标记
2. 把当前 fiber 对象的副作用队列(firstEffect和lastEffect)添加到父节点的副作用队列之后, 更新父节点的firstEffect和lastEffect指针
3. 识别beginWork阶段设置的fiber.flags, 判断当前 fiber 是否有副作用(增,删,改), 如果有, 需要将当前 fiber 加入到父节点的effects队列, 等待commit阶段处理.



### CompleteWork 性能优化策略
flags分布在不同fiberNode中，如何快速找到他们：
- 利用completeWork向上遍历的流程，将子fiberNode的flags冒泡到父fiberNode
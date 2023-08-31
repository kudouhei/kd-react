
## Fiber

React15架构的缺点：
在组件初始化或者更新的时候，会递归更新子组件。 由于是递归执行，所以更新一旦开始，中途就无法中断。 当组件层级很深时，递归更新时间超过了16.6ms（主流浏览器每16.6ms刷新一次），用户交互界面就会卡顿，用户体验较差。

- 在Reconciler中，mount的组件会调用mountComponent，update的组件会调用updateComponent。这两个方法都会递归更新子组件。
- Stack reconciler(协调器)：找出变化的组件
  1. 调用函数组件，或class组件的render方法，将返回的jsx转化为虚拟DOM
  2. 将虚拟DOM和上次更新时的虚拟DOM对比
  3. 通过对比找出本次更新中变化的虚拟DOM
  4. 通知Renderer将变化的虚拟DOM渲染到页面上
  - 同步更新  ->  由于递归更新，无法中断，更新时间超过16ms, 出现卡顿
- Renderer(渲染器)：负责将变化的组件渲染到页面上

React16架构
- 可中断异步更新
- Scheduler 调度器：调度任务的优先级，高优任务优先进入Reconciler
- Fiber reconciler 协调器：负责找出变化的组件
  1. 支持任务不同优先级，可中断与恢复，并且恢复后可以复用之前的中间状态
  2. 任务更新单元为React Element对应的Fiber节点
  3. 没有采用Generator实现协调器，自己实现了一套异步可中断更新机制
  4. Fiber取代React16虚拟DOM的叫法
- Renderer渲染器：将变化的组件渲染到页面上

Fiber节点组成：
- 静态的数据结构：每个Fiber节点对应一个React element，保存了该组件的类型，对应的DOM节点等信息
  - tag, key, elementType, type, stateNode
- 连接其他Fiber节点形成Fiber树的属性
  - return: 指向父级Fiber节点，return指节点执行完completeWork后会返回的下一个节点。 child：指向子Fiber节点。sibling：指向右边第一个兄弟Fiber节点
- 作为动态的工作单元的属性：每个Fiber节点保存了本次更新中该组件改变的状态，要执行的工作（被删除，被插入，被更新）
  - pendingProps, memoizedProps, updateQueue, memoizedState
- 调度优先级相关属性
  - lanes, childLanes
- 指向该fiber在另一次更新时对应的fiber属性
  - alternate

```
class FiberNode {
  constructor(tag, pendingProps, key, mode) {
    // 实例属性
    this.tag = tag; // 标记不同组件类型，如函数组件、类组件、文本、原生组件...
    this.key = key; // react 元素上的 key 就是 jsx 上写的那个 key ，也就是最终 ReactElement 上的
    this.elementType = null; // createElement的第一个参数，ReactElement 上的 type
    this.type = null; // 表示fiber的真实类型 ，elementType 基本一样，在使用了懒加载之类的功能时可能会不一样
    this.stateNode = null; // 实例对象，比如 class 组件 new 完后就挂载在这个属性上面，如果是RootFiber，那么它上面挂的是 FiberRoot,如果是原生节点就是 dom 对象
    // fiber
    this.return = null; // 父节点，指向上一个 fiber
    this.child = null; // 子节点，指向自身下面的第一个 fiber
    this.sibling = null; // 兄弟组件, 指向一个兄弟节点
    this.index = 0; //  一般如果没有兄弟节点的话是0 当某个父节点下的子节点是数组类型的时候会给每个子节点一个 index，index 和 key 要一起做 diff
    this.ref = null; // reactElement 上的 ref 属性
    this.pendingProps = pendingProps; // 新的 props
    this.memoizedProps = null; // 旧的 props
    this.updateQueue = null; // fiber 上的更新队列执行一次 setState 就会往这个属性上挂一个新的更新, 每条更新最终会形成一个链表结构，最后做批量更新
    this.memoizedState = null; // 对应  memoizedProps，上次渲染的 state，相当于当前的 state，理解成 prev 和 next 的关系
    this.mode = mode; // 表示当前组件下的子组件的渲染方式
    // effects
    this.effectTag = NoEffect; // 表示当前 fiber 要进行何种更新（更新、删除等）
    this.nextEffect = null; // 指向下个需要更新的fiber
    this.firstEffect = null; // 指向所有子节点里，需要更新的 fiber 里的第一个
    this.lastEffect = null; // 指向所有子节点中需要更新的 fiber 的最后一个
    this.expirationTime = NoWork; // 过期时间，代表任务在未来的哪个时间点应该被完成
    this.childExpirationTime = NoWork; // child 过期时间
    this.alternate = null; // current 树和 workInprogress 树之间的相互引用
  }
}
```

双缓存
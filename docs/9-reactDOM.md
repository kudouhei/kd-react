# ReactDOM

React内部三个阶段：
- schedule阶段
- render阶段（beginWork, completeWork）
- commit阶段（commitWork）

React 整体的渲染流程就是 render（reconcile 的过程） + commit（执行增删改 dom 和 effect、生命周期函数的执行、ref 的更新等）。

当 setState 之后，就会触发一次渲染的流程，也就是上面的 render + commit。

在render阶段的末尾会调用commitRoot(root);进入commit阶段，这里的root指的就是fiberRoot，然后会遍历render阶段生成的effectList，effectList上的Fiber节点保存着对应的props变化。之后会遍历effectList进行对应的dom操作和生命周期、hooks回调或销毁函数.

React 会把 vdom 树转成 fiber 链表，因为 vdom 里只有 children，没有 parent、sibling 信息，而 fiber 节点里有，这样就算打断了也可以找到下一个节点继续处理。fiber 结构就是为实现并发而准备的。
按照 child -> sibling -> sibling -> return -> sibling -> return 之类的遍历顺序，可以把整个 vdom 树变成线性的链表结构，一个循环就可以处理完。
循环处理每个 fiber 节点的时候，有个指针记录着当前的 fiber 节点，叫做 workInProgress。这个循环叫做 workLoop


## commit阶段的3个子阶段
- beforeMutation：执行DOM操作前
  - 这个阶段通过执行 commitBeforeMutationEffects 函数来更新 class 组件实例上的 state、props 等，并且这个阶段是生命周期函数 getSnapshotBeforeUpdate 调用的地方。
- mutation：执行DOM操作
  - 这个阶段通过调用 commitMutationEffects 来完成副作用的执行，主要是处理副作用队列中带有Placement、Update、Deletion、Hydrating标记的fiber节点，与 react-dom 交互，完成DOM节点的插入、更新以及删除操作。
- layout：执行DOM操作后
  - 这个阶段通过执行 commitLayoutEffects 函数来处理副作用队列中带有Update | Callback标记的fiber节点，并触发 componentDidMount、componentDidUpdate 以及各种回调函数等。

当前commit阶段要执行的任务
1. fiber树的切换
2. 执行Placement对应操作

打包ReactDOM
- 兼容原版React的导出
- 处理hostConfig的指向

在 reconcile （调和）阶段，给 fiber 打了很多的 flags（标记），commit 阶段是会读取这些 flags 进行不同的操作的。
flags 是通过二进制掩码的方式来保存的，掩码优点是节省内存，缺点是可读性很差。
使用或位运算，可以将多个 flag 组合成一个组。

# 实现useState

useState特点就是使组件具有状态，且有存储数据的功能。

- hook如何知道在另一个hook的上下文环境内执行？
- hook如何知道当前是mount还是update？
  - 解决：在不同上下文中调用的hook不是同一个函数。
- hook如何知道自身数据保存在哪里？
  - 解决：可以记录当前正在render的FC对应fiberNode，在fiberNode中保存hook数据

实现Hooks的数据结构
fiberNode中可用的字段：
- memoizedState
- updateQueue

对于FC对应的fiberNode,存在两层数据
- fiberNode.memoizedState对应Hooks链表
- 链表中每个hook对应自身的数据


## 如何触发更新
常见的触发更新的方式：
- ReactDOM.createRoot().render or (old version ReactDOM.render)
- this.setState
- useState的dispatch方法

希望实现一套统一的更新机制，特点是：
- 兼容上述触发更新的方式
- 方便后续拓展

### 更新机制的组成部分
- 代表更新的数据结构 -- Update
- 消费update的数据结构 -- UpdateQueue
  - 关系：UpdateQueue 包含shared.pending 包含 update, update

接下来工作包括：
- 实现mount时调用的API
- 将该API接入上述更新机制中
需要考虑的事情：
- 更新可能发生于任意组件，而更新流程使从根节点递归的
- 需要一个统一的根节点保存通用信息

mount 时上面的Fiber树构建过程如下：
1. 首次执行ReactDOM.createRoot(root)会创建fiberRootNode；
2. 接着执行到`render(<App />)`时会创建HostRootFiber，实际上它是一个HostRoot节点；
   `fiberRootNode 是整个应用的根节点，HostRootFiber 是 <App /> 所在组件树的根节点`
3. 从HostRootFiber开始，以DFS（深度优先搜索）的的顺序遍历子节点，以及生成对应的FiberNode；
4. 在遍历过程中，为FiberNode标记"代表不同副作用的 flags"，以便后续在宿主环境中渲染的使用；

之所以要区分fiberRootNode和HostRootFiber是因为在整个React应用程序中开发者可以多次多次调用render方法渲染不同的组件树，它们会有不同的HostRootFiber，但是整个应用的根节点只有一个，那就是fiberRootNode

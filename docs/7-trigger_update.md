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
## React trigger update

### Common ways to trigger updates include
- ReactDOM.createRoot().render or (old version ReactDOM.render)
- this.setState
- useState dispatch method

### Components of the Update Mechanism

- Data structure representing updates: Update
- Data structure consuming updates: UpdateQueue
  - Relationship: UpdateQueue contains shared.pending, which contains updates.

### The next steps
- Implementing the API to be called during mount
- Integrating this API into the update mechanism mentioned above

**Things to consider:**
- Updates may occur in any component, but the update process is recursive from the root node.
- A unified root node is needed to store common information.

### The Fiber tree construction process during **Mount**

1. When ReactDOM.createRoot(root) is first executed, it creates a fiberRootNode.
2. when render(`<App />`) is executed, it creates a HostRootFiber, which is, in fact, a HostRoot node.
   1. `fiberRootNode` is the root of the entire application, and HostRootFiber is the root of the component tree containing `<App />`."
3.  Starting from HostRootFiber, it traverses child nodes in DFS (depth-first search) order and generates corresponding FiberNodes.
4.  During the traversal, it marks "flags representing different side effects" for FiberNodes for subsequent use in the host environment rendering.

The reason for distinguishing between fiberRootNode and HostRootFiber is that in an entire React application, developers can call the render method multiple times to render different component trees. Each of them will have a different HostRootFiber, but the root of the entire application is a single fiberRootNode.

## React Fiber

### React 15 architecture

During components initialization or updates, child components are updated recursively. Since it's a recursive process, once the update begins, it cannot be interrupted. When the component hierarchy is deep, and the recursive updates take longer than 16.6ms (most mainstream browsers refresh every 16.6ms), in this scenario, the user interaction interface is stuck, resulting in a poor user experience.

- In the **Reconciler**, mount components invoke `mountComponent`, and update components invoke `updateComponent`. Both of these methods recursively update child components.

- Stack **Reconciler**: Identifying the changed components
  1. Call the render method of functional or class components, converting the returned JSX into a virtual DOM.
  2. Compare the current virtual DOM with the virtual DOM from the previous update.
  3. Identify the changed virtual DOM components for this update.
  4. Notify the Renderer to render the changed virtual DOM on the page.

- **Renderer**: Responsible for rendering the changed components onto the page.


### React Fiber Architecture

- Interruptible asynchronous updates
- **Scheduler**: Prioritizes tasks, with high-priority tasks entering the Reconciler first.
- **Fiber Reconciler**: Responsible for identifying changed components.
  1. Supports tasks of different priorities, interruptibility, and resumption, with the ability to reuse previous intermediate states upon resumption.
  2. Task update units correspond to Fiber nodes associated with React Elements.
  3. It doesn't use Generators to implement the reconciler; it has its own asynchronous interruptible update mechanism.
  4. Fiber replaces the term "React 16 virtual DOM."
- **Renderer**: Renders the changed components onto the page.

### Fiber Node Structure

- Static data structure: Each Fiber node corresponds to a React element, storing information such as component type, associated DOM node, etc.
  - tag, key, elementType, type, stateNode
- Properties for forming the Fiber tree by connecting to other Fiber nodes
  - return: Points to the parent Fiber node. "return" indicates the next node to execute after completing the "completeWork" process. child: Points to the child Fiber node. sibling: Points to the first right sibling Fiber node.
- Properties as dynamic work units: Each Fiber node stores the component's changed state and the work to be executed for this update (deletion, insertion, or update).
  - pendingProps, memoizedProps, updateQueue, memoizedState
- Scheduling priority-related properties
  - lanes, childLanes
- Properties pointing to the corresponding Fiber in the next update
  - alternate

#### Fiber Objects

A Fiber object represents a component (React Element) that is either about to be rendered or has already been rendered. A component may correspond to two fibers (current and WorkInProgress).

React Fiber is implemented using a linked list. Each Virtual DOM can be represented as a fiber. In the diagram below, each node is a fiber. A fiber includes properties such as child (the first child node), sibling (sibling node), return (parent node), and so on. The React Fiber mechanism relies on this data structure for its implementation.


```
class FiberNode {
  constructor(tag, pendingProps, key, mode) {
    // instance attribute
    this.tag = tag; //  Marks different component types, such as functional components, class components, text, native components
    this.key = key; // The key on the React element is the one written in JSX, which is also the one on the final ReactElement.
    this.elementType = null; // The first parameter of createElement, the 'type' on ReactElement.
    this.type = null; // Represents the actual type of the fiber. 'elementType' is almost the same, but may differ when features like lazy loading are used.
    this.stateNode = null; // Instance object, for example, if it's a class component, the instance is mounted here; if it's a RootFiber, it holds the FiberRoot; if it's a native node, it's the DOM object.

    // fiber
    this.return = null; // Parent node, points to the previous fiber.
    this.child = null; //  Child node, points to the first fiber below itself.
    this.sibling = null; //// Sibling component, points to a sibling node.
    this.index = 0; //  // Typically 0 if there are no sibling nodes. When the child nodes of a parent node are of an array type, each child node is assigned an index. The index is used in conjunction with the key for diffing.
    this.ref = null; // The 'ref' attribute on ReactElement.

    this.pendingProps = pendingProps; // New props, passed from the 'ReactElement' object. Used to compare with 'fiber.memoizedProps' to determine if the attributes have changed.
    this.memoizedProps = null; // Old props, the attributes used during the previous generation of child nodes. Kept in memory after generating child nodes.

    this.updateQueue = null; // Update queue on the fiber. A new update is attached to this property every time 'setState' is called. Each update eventually forms a linked list structure, and batch updates are performed later.
    this.memoizedState = null; // Corresponds to 'memoizedProps,' the state from the last render, essentially the current state. Think of it as a relationship between the previous and current states.

    this.mode = mode; // Indicates how child components are rendered within the current component.

    // Flags
    this.flags: Flags; // Flags or indicators.
    this.subtreeFlags: Flags; // Replaces 'firstEffect' and 'nextEffect' in version 16.x. Enabled only when 'enableNewReconciler' is set to true.
    this.effectTag = NoEffect; // Indicates the type of update to be performed on the current fiber (update, delete, etc.).

    // Effects
    this.nextEffect = null; // Points to the next fiber that needs an update.
    this.firstEffect = null; // Points to the first fiber among all child nodes that needs an update.
    this.lastEffect = null; // Points to the last fiber among all child nodes that needs an update.
    this.expirationTime = NoWork; // Expiration time represents when the task should be completed in the future.
    this.childExpirationTime = NoWork; // Child expiration time.

    this.alternate = null; // Mutual reference between the current tree and the work-in-progress tree.
  }
}
```

### React Fiber is used in React16, but there is no Fiber in Vue, why?

Cause the two have different optimization ideas
- Vue is a component-level update based on templates and watchers, splitting each update task small enough that it doesn't need to use the Fiber architecture to split the task at a finer granularity.
- No matter where React calls setState, the update starts from the root node, and the update task is huge. Hence, you need to use Fiber to split the enormous task into multiple small tasks, which can be interrupted and resumed without blocking the main process from executing high-priority tasks.




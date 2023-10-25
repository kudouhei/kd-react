# React useState

### function组件特点
- 没有状态
- 没有生命周期
- 没有 this

### function组件和class组件本质区别
 
- class组件，只需要实例化一次，实例中保存了组件的state等状态。每一次更新只需要调用render方法。
- function组件中，每一次更新都是一次新的函数执行,为了保存状态,执行一些副作用钩子,需要react-hooks记录组件的状态。

### 实现useState
useState特点就是使组件具有状态，且有存储数据的功能。useState()在初始时，或调用 dispatch()时，都有两种传参方式：一种是直接传入数据；一种是以函数 callback 的形式传入，state 的值就是该函数执行后的结果。

- hook如何知道在另一个hook的上下文环境内执行？
- hook如何知道当前是mount还是update？
  - 解决：在不同上下文中调用的hook不是同一个函数。
- hook如何知道自身数据保存在哪里？
  - 解决：可以记录当前正在render的FC对应fiberNode，在fiberNode中保存hook数据

#### notes
- 实现Hooks的数据结构
  fiberNode中可用的字段：
  - memoizedState
  - updateQueue
- 对于FC对应的fiberNode,存在两层数据
  - fiberNode.memoizedState对应Hooks链表
  - 链表中每个hook对应自身的数据
- current fiber树: 当完成一次渲染之后，会产生一个current树,current会在commit阶段替换成真实的Dom树。
- workInProgress fiber树: 即将调和渲染的 fiber 树。再一次新的组件更新过程中，会从current复制一份作为workInProgress,更新完毕后，将当前的workInProgress树赋值给current树。
- workInProgress.memoizedState: 在class组件中，memoizedState存放state信息，在function组件中，memoizedState在一次调和渲染过程中，以链表的形式存放hooks信息。
- currentHook : current树上的指向的当前调度的 hooks节点。
- workInProgressHook : workInProgress树上指向的当前调度的 hooks节点

#### 多次调用useState()中的dispatch方法，会产生多次渲染吗？
针对相同优先级的操作，即使有多个 useState()，或执行多次的 dispatch()方法，也仅会引起一次的组件渲染。

#### props发生变动时，useState()中的数据会变吗？
不会。虽然 props 的变动，会导致组件的重新刷新，但 useState()中的数据并不会发生变动，即使 useState()用了 props 中的数据作为初始值。这是因为 state 值的变动，只受 dispatch() 的影响。
若想在 props 变动时，重新调整 state 的值，可以用 useEffect() 来监听 props 的变动。

```
function App(props) {
  const [count, setCount] = useState(props.count);

  useEffect(() => {
    // props 中的 count 属性发生变动时，重新赋值
    setCount(props.count);
  }, [props.count]);
}
```

#### React Hooks useState异步问题
useState 返回的更新状态方法是异步的，要在下次重绘才能获取新值。不要试图在更改状态之后立马获取状态。
解决方法:
- 应该使用useRef 存储这个数据,在useEffect里监听data的变化

```
const dataRef = useRef()
const [data,setData] = useState[{}]
useEffect(() => {
     dataRef.current =   data
}, [data])

// 最新的数据
console.log(dataRef.current)
```
#### How to compute React state from the previous state?

When using setState, you can access the previous state as an argument of callback.
`setCount(prevCount => prevCount + 1);`

#### Storing global state in useState

useState is only suitable to store components local states. If your data is used within multiple pages or widgets - consider putting it into a global state (React Context, Redux, MobX)

#### Avoid Mutating state instead of returning a new one
We should avoid mutating state and simply return a new state.

```
  const handleChangeInfo = useCallback((field) => {
    // e is input onChange event
    return (e) => {
      setUserInfo((prev) => {
        // Here we are mutating prev state.
        // That simply won't work as React doesn't recognize the change
        prev[field] = e.target.value;

        return prev;
      });
    };
  }, []);
```

```
  setUserInfo((prev) => ({
    // So when we update name, surname stays in state and vice versa
    ...prev,
    [field]: e.target.value
  }));
```
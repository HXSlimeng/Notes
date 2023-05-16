### 1. hooks

- `useMemo`(callback, [ dependence] )

  - 适用于返回数组或者对象

  > ​	一般来说 页面中的页面计算 在每一次一个响应式变量发生变化时都会发生一次`repaint`，此hooks是为了防止 依赖项未发生改变，因为其他的响应式变量发生变化repaint时导致该计算重新运行而导致不必要的开销，此方法首先会判断`dependence`内的变量是否发生变化、如果发生变化则重新计算反之直接返回上一次计算的值（缓存的值）
  
  ```jsx
  //返回值接收最后计算出来的值
  const allPrimes = React.useMemo(() => {
    const result = [];
    for (let counter = 2; counter < selectedNum; counter++) {
      if (isPrime(counter)) {
        result.push(counter);
      }
    }
    return result;
  }, [selectedNum]);

  ```
  
  - 可以使用memo包裹子组件（纯组件）、使之在父组件重新渲染时 不影响子组件重新渲染 只要组件的props不发生改变（==需注意引用类型的引用==）
  
- `useCallback`

  - 作用与useMemo相同
  - 适用于返回函数(子组件有接收父组件传来的函数)

- `useEffect`(callback,dep？)
  
  - 在dep发生变化时触发callback
  - 若传空数组则在组件挂载及卸载的时候触发一次
  - 在React18中useEffect会执行两次、若想避免该种情况可以传入一个 不变的state 可以使用useRef生成一个随机数
  - 严格模式下useEffect在挂载时会执行两次、此情况是在严格模式下导致的、可以用一个Ref变量控制 或者 取消严格模式
  
- `useRef`
  
  - 生成一个不需要渲染的值,每次重新渲染函数组件返回的ref对象都是同一个
  - 访问其current可以访问到目标值，数值改变不会影响dom渲染
  
- `useReducer`

  > 类似于redux的 reducer, useState的底层也是使用useReducer实现的

  ```typescript
  type Istate = {
    number: number;
  };
  const initialState = 0;
  function reducer(state: Istate, action: any) {
    switch (action) {
      case "increment":
        return { number: state.number + 1 };
      case "decrement":
        return { number: state.number - 1 };
      default:
        throw new Error();
    }
  }
  function init(initialState: number) {
    return { number: initialState };
  }
  function Counter() {
    const [state, dispatch] = useReducer(reducer, initialState, init);
    console.log("refresh");
  
    return (
      <>
        Count: {state.number}
        <button onClick={() => dispatch("increment")}>+</button>
        <button onClick={() => dispatch("decrement")}>-</button>
      </>
    );
  }
  ```

  - `reducer`:（state，action）可以获取到当前的`state`,根据`action`改变当前state
  - `initialState`：传入的初始值
  - `init`：根据传入的初始值进行一些初始操作

- `forwardRef`:使函数组件可以接收ref属性，配合 `useImperativeHandle`可以访问子组件的方法

  ```tsx
  //子组件
  // 1. 使用forwardRef包裹子组件
  export default forwardRef(function FetchTable(props,parentRef){
      
      const clickFun = ()=>{}
      // 2. 使用useImperativeHandle将clickFun映射给parentRef
      useImperativeHandle(parentRef,()=>{
          clickFun
      })
      return(
      //...
      )
  })
  //父组件
  export default function SqlSource() {
      // 3. 创建ref接收
      const tableRef = useRef()
      
      useEffect(()=>{
          // 5. 此时可以通过ref直接访问子组件的方法
          tableRef.current.clickFun()
      },[])
      
      return(
          <FetchTable
              // 4. 映射useRef
              ref={tableRef}
              searchPart={SearchInp}
              col={column}
              fetchObj={fetchObj}
  		    ></FetchTable>
      )
  
  ```

  

### 2. react-router-dom

### 3. 细节

- 点击更新状态时、函数组件都会被重新调用
- useState(initialState) 如果穿入的`initialState`是一个函数,此函数只会在`初始渲染`时被调用

### 4. 优化

- 默认情况、只要父组件状态改变了、子组件也会重新渲染
  - 类组件：使用`pureComponent`
  - 函数组件：使用`React.memo`，组件接收到的属性不变，则不重新渲染函数
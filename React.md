### 1. hooks

- useMemo(callback, [ dependence] )

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
  
- useCallback

  - 作用与useMemo相同
  - 适用于返回函数(子组件有接收父组件传来的函数)

- useEffect(callback,dep？)
  - 在dep发生变化时触发callback
  - 若传空数组则在组件挂载及卸载的时候触发一次
  - 在React18中useEffect会执行两次、若想避免该种情况可以传入一个 不变的state 可以使用useRef生成一个随机数
  - 严格模式下useEffect在挂载时会执行两次、此情况是在严格模式下导致的、可以用一个Ref变量控制 或者 取消严格模式
- useRef
  - 生成一个不需要渲染的值
  - 访问其current可以访问到目标值，数值改变不会影响dom渲染

### 2. react-router-dom
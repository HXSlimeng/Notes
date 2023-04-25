1. ### watch

   - immediate:在watch创建的时候即触发一次
   - watchEffect与watch的异同：
     - `watch`需要显示的监听数据源，`watchEffect`是寻找回调函数中的所有响应式依赖
     - `watch`声明即触发需要配置immediate，而`watchEffect`默认会在初始渲染的时候执行一次回调函数
     - `watch`可以获取到新旧值
     - 如果 `watchEffect`监听嵌套数据结构中的几个属性，则只跟踪这几个属性，而不是递归的追踪这些属性

   - watchPostEffect：在依赖值发生变化时，若更新了dom，`watchEffect`触发在dom更新之前，`watchPostEffect`则触发在dom更新之后。

   - 停止侦听器：调用侦听器的返回值函数则可以停止监听

     ```js
     const unwatch = watchEffect(() => {})
     const unwatch = 
     // ...当该侦听器不再需要时
     unwatch()
     ```

     
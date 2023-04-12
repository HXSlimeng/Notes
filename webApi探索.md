1. #### 同域下不通浏览器之间通信(BroadcastChannel)

   ```js
   var bc = new BroadcastChannel('internal_notification');
   
   bc.postMessage('New listening connected!');
   ```

   
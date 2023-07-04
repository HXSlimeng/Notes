### 1. 同域下不通浏览器之间通信(BroadcastChannel)

```js
var bc = new BroadcastChannel('internal_notification');
bc.postMessage('New listening connected!');
```

### 2. 浏览器加减密

- 加密算法

  对称加密：信息的发送方和接收方使用`同一个密钥`去加密和解密数据 

  > 加密过程: 明文 + 加密算法 + 私钥 => 密文
  >
  > 解密过程: 密文 + 解密算法 + 私钥 => 明文

- 非对称加密：使用一对密钥（`公钥`和`私钥`）`公钥`和`私钥`成对出现  

	`RSA` `ECC`

  > 加密过程：明文 + 加密算法 + 公钥 => 密文
  >
  > 解密过程：密文 + 解密算法 + 私钥 => 明文

- 散列算法（不需要密钥）

  `SHA-1` `MD5`
  
  #### 在浏览器当中使用`crypto`进行加减密
  
  ```typescript
  async function encrypt(text: string, key: CryptoKey) {
      
    //返回一个包含 UTF-8 编码的文本的 Uint8Array字节流。
    const encodedText = new TextEncoder().encode(text)
    
    //getRandomValues 返回一个随机的 Uint8Array字节流。生成一个随机生成的初始化向量IV
    const iv = crypto.getRandomValues(new Uint8Array(16))
    
    //使用的加密算法选项
    const algorithm = { name: 'AES-CBC', iv }
    
    //使用crypto方法进行加密 该方法返回Uinit8Array字节流
    const ciphertext = await crypto.subtle.encrypt(algorithm, key, encodedText)
    
    //fromCharCode 将Uinit8Array字节流使用UTF-16转为字符串,方便传输存储
    const encodedCiphertext = Array.from(new Uint8Array(ciphertext)).map(byte => String.fromCharCode(byte)).join('')
    
    //初始化向量同理
    const encodedIv = Array.from(iv).map(byte => String.fromCharCode(byte)).join('')
    
    //拼接返回
    return `${encodedIv}:${encodedCiphertext}`
  }
  
  async function decrypt(ciphertext: string, key: CryptoKey) {
    const [encodedIv, encodedCiphertext] = ciphertext.split(':')
    //charCodeAt将字符串解为Uint8Array字节流
    const iv = new Uint8Array(encodedIv.split('').map(char => char.charCodeAt(0)))
    const encodedCiphertextArray = encodedCiphertext.split('').map(char => char.charCodeAt(0))
    const ciphertextArray = new Uint8Array(encodedCiphertextArray)
    const algorithm = { name: 'AES-CBC', iv }
    const plaintextArray = await crypto.subtle.decrypt(algorithm, key, ciphertextArray)
    const plaintext = new TextDecoder().decode(plaintextArray)
    return plaintext
  }
  
  async function generateKey() {
    const algorithm = { name: 'AES-CBC', length: 256 }
    const key = await crypto.subtle.generateKey(algorithm, true, ['encrypt', 'decrypt'])
    return key
  }
  
  const plaintext = 'Hello, world!'
  const key = await generateKey()
  //导出key为一个CryptoKey对象
  const exportScrect = await window.crypto.subtle.exportKey('jwk',key)
  const screct = await window.crypto.subtle.importKey('jwk',exportScrect,{name: 'AES-CBC'},true,['encrypt', 'decrypt'])
  const ciphertext = await encrypt(plaintext, screct)
  const decryptedtext = await decrypt(ciphertext, screct)
  ```
  
  

### 3. ArrayBuffer

​	`ArrayBuffer` 是以字节流的形式存储的

### 4. Uint8Array (TypedArray的一种)

表示一个 8 位无符号整型数组，每个元素代表一个字节8位即（0~255）`00000000~11111111`

 new Uint8Array(32) 即生成一个 32位 256字节的 数据

> 一个 ***TypedArray\*** 对象描述了底层[二进制数据缓冲区](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)的类数组视图

### 5. 浏览器下base64的编码解码

​	`btoa`加密 `atob`解密

### 6. TextEncoder

### 7.动画event

​	可以为dom添加该事件以再transition动画结束后触发

​	useage:

```typescript
transitionstart/end animationstart/end
```

### 8.监听某个dom的尺寸变化`ResizeObserver`

```typescript
const resizeOb = new ResizeObserver((entries)=>{
//...callback    
})
resizeOb.observe(dom)

```

### 9.[ArrayBuffer,blob,File](https://blog.csdn.net/lychee_xiahua/article/details/113170727)

​	`FileReader`可以对`blob`对象进行处理

> 在浏览器环境中  `Blob`对象自带`text`属性方法，返回一个`Promise`，该`promise`成功后返回一个`utf-8`编码的文本

- `ArrayBuffer`:（可操作、大型数据，比Blob更底层）ArrayBuffer是一种表示通用内存缓冲区的对象,没有任何类型信息；它只是一个连续的、固定长度的二进制数据块,可以使用`TypedArray`或者`DataView`来操作

  `nodejs`中可以使用`Buffer.from`创建一个`Buffer`,解析则使用`buffer.toString()`可以得到一个默认`utf-8`解码的内容

- `Blob`:(不可操控、可以设置MIME 类型)

- `Node.js中的Buffer`：可以直接修改和访问其内容，`Buffer.buffer`返回`ArrayBuffer`

- `ArrayBuffer`对象代表原始的二进制数据

- `TypedArray`视图用来读写简单类型的二进制数据（ArrayBuffer），

- `DataView`视图用来读写复杂类型的二进制数据(ArrayBuffer)。

- Node中的`Buffer`类是以更优化和更适合Nodejs的方式实现了`Uint8Array` API，意思就是`Buffer`类其实是`TypedArray(Uint8Array)`的nodejs实现。

  
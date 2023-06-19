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


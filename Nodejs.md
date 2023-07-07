### 特性：单线程、异步I/O、事件驱动

> 由于node是单线程、所以不用考虑锁和线程池的问题

- `cross-env` 可以在运行时配置环境变量

  1. useage：

     ```powershell
     cross-env NODE_ENV=development nodemon ./src/app.ts
     ```

     ```typescript
     console.log(process.env.NODE_ENV);//development
     ```

     

- `nodemon`node服务器发生更改时热重载

  > ORM:对象关系映射(Object-Relational Mapping)的缩写，它是一种编程技术，用于在关系型数据库和面向对象编程语言之间建立映射
  
### 第三方类库

- [typeOrm](https://github.com/typeorm/typeorm)：操作数据库的工具类库
- [socket.io](https://socket.io/zh-CN/)：实现b端与c端的高效实时通讯

### MVC架构项目目录

├─ src                     源码
│  ├─ app                  业务代码
│  │  ├─ controllers       控制器：用于解析用户输入，处理后返回相应的结果
│  │  ├─ models            模型  ：用于定义数据模型
│  │  ├─ services          服务  ：用于编写业务逻辑层，比如连接数据库，调用第三方接口等
│  │  └─ views             视图  ：用于放置模板文件，返回客户端的视图层
│  │
│  ├─ core                 核心代码
│  │  ├─ controller.js     控制器基类
│  │  ├─ model.js          模型基类
│  │  └─ service.js        服务基类
│  │
│  ├─ middlewares          中间件
│  ├─ public               静态资源
│  ├─ router               URL 路由
│  ├─ utils                工具库
│  └─ index.js             入口：用于自定义启动时的初始化工作，比如启动 https，调用中间件、路由等
│  
├─ .eslintrc               eslint 配置文件
├─ nodemon.json            nodemon 配置文件
├─ package.json            npm 配置文件
├─ processes.json          pm2 配置文件

### 数据库相关

- `nvarchart`支持unicode字符 `varchar`只支持ASCII字符

### child_process

- `spawn`:用于调系统上的命令（unix系统直接调用，）

### fs文件系统

- `fs.watch| fs.watchFile`:监听文件或者文件夹的变化,`watchFile`方法可以配置轮询时间，watch是系统提供的监听文件方法
- `fs.unwatchFile`:停止监听文件
- `fs.writeFile`:将内容写入指定文件 编码默认为utf-8，默认直接覆盖原内容，可以配置[`option.flag`](https://nodejs.cn/dist/latest-v18.x/docs/api/fs.html#file-system-flags)配置定位的位置，改用`fs.appendFile`可以将指定内容追加到文件末尾

### os操作系统相关

### 锁和线程池

- `锁`：锁是一种同步机制，防止多个线程同事访问共享资源
- `线程池`：是一种管理和复用线程的机制

### 子进程child_process

1. 创建

- `spawn`：执行`非node`程序，以`流`的形式返回
- `execFile`:执行`非node`程序，结果以`回调`形式返回
- `exec`:执行`非node`程序，传入一段`shell`命令，以`回调`形式返回
- `fork`:执行node程序

### Nest 

1. `commond`:

   ```powershell
   nest g pipe testPipe //创建一个管道类型
   nest g resource user //创建一个user实体类
   nest g mi Logger //快速创建一个middleware
   ```

2. `管道pipe`：对入参进行验证、反之则抛出异常、可以引用`nest/common`导出的exception类型,可以使用**useGlobalPipes**方法创建全局管道

3. `中间件middleware`:

4. `架构思想`

   `controller`处理http请求 => `module`中的`providers`


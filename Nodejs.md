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
  
- [typeOrm](https://github.com/typeorm/typeorm)

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